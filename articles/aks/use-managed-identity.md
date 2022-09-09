---
title: Use managed identities in Azure Kubernetes Service
description: Learn how to use managed identities in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 12/06/2020
---

# Use managed identities in Azure Kubernetes Service

Currently, an Azure Kubernetes Service (AKS) cluster (specifically, the Kubernetes cloud provider) requires an identity to create additional resources like load balancers and managed disks in Azure. This identity can be either a *managed identity* or a *service principal*. If you use a [service principal](kubernetes-service-principal.md), you must either provide one or AKS creates one on your behalf. If you use managed identity, this will be created for you by AKS automatically. Clusters using service principals eventually reach a state in which the service principal must be renewed to keep the cluster working. Managing service principals adds complexity, which is why it's easier to use managed identities instead. The same permission requirements apply for both service principals and managed identities.

*Managed identities* are essentially a wrapper around service principals, and make their management simpler. Credential rotation for MI happens automatically every 46 days according to Azure Active Directory default. AKS uses both system-assigned and user-assigned managed identity types. These identities are currently immutable. To learn more, read about [managed identities for Azure resources](../active-directory/managed-identities-azure-resources/overview.md).

## Before you begin

You must have the following resource installed:

- The Azure CLI, version 2.15.1 or later

## Limitations

* During cluster **upgrade** operations, the managed identity is temporarily unavailable.
* Tenants move / migrate of managed identity enabled clusters isn't supported.
* If the cluster has `aad-pod-identity` enabled, Node-Managed Identity (NMI) pods modify the nodes'
  iptables to intercept calls to the Azure Instance Metadata endpoint. This configuration means any
  request made to the Metadata endpoint is intercepted by NMI even if the pod doesn't use
  `aad-pod-identity`. AzurePodIdentityException CRD can be configured to inform `aad-pod-identity`
  that any requests to the Metadata endpoint originating from a pod that matches labels defined in
  CRD should be proxied without any processing in NMI. The system pods with
  `kubernetes.azure.com/managedby: aks` label in _kube-system_ namespace should be excluded in
  `aad-pod-identity` by configuring the AzurePodIdentityException CRD. For more information, see
  [Disable aad-pod-identity for a specific pod or application](https://azure.github.io/aad-pod-identity/docs/configure/application_exception).
  To configure an exception, install the
  [mic-exception YAML](https://github.com/Azure/aad-pod-identity/blob/master/deploy/infra/mic-exception.yaml).

## Summary of managed identities

AKS uses several managed identities for built-in services and add-ons.

| Identity                       | Name    | Use case | Default permissions | Bring your own identity
|----------------------------|-----------|----------|
| Control plane | not visible | Used by AKS control plane components to manage cluster resources including ingress load balancers and AKS managed public IPs, and Cluster Autoscaler operations | Contributor role for Node resource group | Preview
| Kubelet | AKS Cluster Name-agentpool | Authentication with Azure Container Registry (ACR) | NA (for kubernetes v1.15+) | Not currently supported
| Add-on | AzureNPM | No identity required | NA | No
| Add-on | AzureCNI network monitoring | No identity required | NA | No
| Add-on | azure-policy (gatekeeper) | No identity required | NA | No
| Add-on | azure-policy | No identity required | NA | No
| Add-on | Calico | No identity required | NA | No
| Add-on | Dashboard | No identity required | NA | No
| Add-on | HTTPApplicationRouting | Manages required network resources | Reader role for node resource group, contributor role for DNS zone | No
| Add-on | Ingress application gateway | Manages required network resources| Contributor role for node resource group | No
| Add-on | omsagent | Used to send AKS metrics to Azure Monitor | Monitoring Metrics Publisher role | No
| Add-on | Virtual-Node (ACIConnector) | Manages required network resources for Azure Container Instances (ACI) | Contributor role for node resource group | No
| OSS project | aad-pod-identity | Enables applications to access cloud resources securely with Azure Active Directory (AAD) | NA | Steps to grant permission at https://github.com/Azure/aad-pod-identity#role-assignment.

## Create an AKS cluster with managed identities

You can now create an AKS cluster with managed identities by using the following CLI commands.

First, create an Azure resource group:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Then, create an AKS cluster:

```azurecli-interactive
az aks create -g myResourceGroup -n myManagedCluster --enable-managed-identity
```

A successful cluster creation using managed identities contains this service principal profile information:

```output
"servicePrincipalProfile": {
    "clientId": "msi"
  }
```

Use the following command to query objectid of your control plane managed identity:

```azurecli-interactive
az aks show -g myResourceGroup -n myManagedCluster --query "identity"
```

The result should look like:

```output
{
  "principalId": "<object_id>",   
  "tenantId": "<tenant_id>",      
  "type": "SystemAssigned"                                 
}
```

Once the cluster is created, you can then deploy your application workloads to the new cluster and interact with it just as you've done with service-principal-based AKS clusters.

> [!NOTE]
> For creating and using your own VNet, static IP address, or attached Azure disk where the resources are outside of the worker node resource group, use the PrincipalID of the cluster System Assigned Managed Identity to perform a role assignment. For more information on role assignment, see [Delegate access to other Azure resources](kubernetes-service-principal.md#delegate-access-to-other-azure-resources).
>
> Permission grants to cluster Managed Identity used by Azure Cloud provider may take up 60 minutes to populate.

Finally, get credentials to access the cluster:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```
## Update an AKS cluster to managed identities (Preview)

You can now update an AKS cluster currently working with service principals to work with managed identities by using the following CLI commands.

First, Register the Feature Flag for system-assigned identity:

```azurecli-interactive
az feature register --namespace Microsoft.ContainerService -n MigrateToMSIClusterPreview
```

Update the system-assigned identity:

```azurecli-interactive
az aks update -g <RGName> -n <AKSName> --enable-managed-identity
```

Register the Feature Flag for user-assigned identity:

```azurecli-interactive
az feature register --namespace Microsoft.ContainerService -n UserAssignedIdentityPreview
```

Update the user-assigned identity:

```azurecli-interactive
az aks update -g <RGName> -n <AKSName> --enable-managed-identity --assign-identity <UserAssignedIdentityResourceID> 
```
> [!NOTE]
> Once the system-assigned or user-assigned identities have been updated to managed identity, perform an `az nodepool upgrade --node-image-only` on your nodes to complete the update to managed identity.

## Bring your own control plane MI
A custom control plane identity enables access to be granted to the existing identity prior to cluster creation. This feature enables scenarios such as using a custom VNET or outboundType of UDR with a pre-created managed identity.

You must have the Azure CLI, version 2.15.1 or later installed.

### Limitations
* Azure Government isn't currently supported.
* Azure China 21Vianet isn't currently supported.

If you don't have a managed identity yet, you should go ahead and create one for example by using [az identity CLI][az-identity-create].

```azurecli-interactive
az identity create --name myIdentity --resource-group myResourceGroup
```
The result should look like:

```output
{                                                                                                                                                                                 
  "clientId": "<client-id>",
  "clientSecretUrl": "<clientSecretUrl>",
  "id": "/subscriptions/<subscriptionid>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myIdentity", 
  "location": "westus2",
  "name": "myIdentity",
  "principalId": "<principalId>",
  "resourceGroup": "myResourceGroup",                       
  "tags": {},
  "tenantId": "<tenant-id>>",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

If your managed identity is part of your subscription, you can use [az identity CLI command][az-identity-list] to query it.  

```azurecli-interactive
az identity list --query "[].{Name:name, Id:id, Location:location}" -o table
```

Now you can use the following command to create your cluster with your existing identity:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myManagedCluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 \
    --enable-managed-identity \
    --assign-identity <identity-id> \
```

A successful cluster creation using your own managed identities contains this userAssignedIdentities profile information:

```output
 "identity": {
   "principalId": null,
   "tenantId": null,
   "type": "UserAssigned",
   "userAssignedIdentities": {
     "/subscriptions/<subscriptionid>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myIdentity": {
       "clientId": "<client-id>",
       "principalId": "<principal-id>"
     }
   }
 },
```

## Next steps
* Use [Azure Resource Manager (ARM) templates ][aks-arm-template] to create Managed Identity enabled clusters.

<!-- LINKS - external -->
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters
[az-identity-create]: /cli/azure/identity?view=azure-cli-latest#az-identity-create&preserve-view=true
[az-identity-list]: /cli/azure/identity?view=azure-cli-latest#az-identity-list&preserve-view=true
