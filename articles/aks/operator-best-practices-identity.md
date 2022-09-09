---
title: Best practices for managing identity
titleSuffix: Azure Kubernetes Service
description: Learn the cluster operator best practices for how to manage authentication and authorization for clusters in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 03/09/2021
ms.author: jpalma
author: palma21

---

# Best practices for authentication and authorization in Azure Kubernetes Service (AKS)

As you deploy and maintain clusters in Azure Kubernetes Service (AKS), you implement ways to manage access to resources and services. Without these controls:
* Accounts could have access to unnecessary resources and services. 
* Tracking which set of credentials were used to make changes could be difficult.

This best practices article focuses on how a cluster operator can manage access and identity for AKS clusters. In this article, you learn how to:

> [!div class="checklist"]
>
> * Authenticate AKS cluster users with Azure Active Directory.
> * Control access to resources with Kubernetes role-based access control (Kubernetes RBAC).
> * Use Azure RBAC to granularly control access to the AKS resource, the Kubernetes API at scale, and the `kubeconfig`.
> * Use a managed identity to authenticate pods themselves with other services.

## Use Azure Active Directory (Azure AD)

> **Best practice guidance** 
> 
> Deploy AKS clusters with Azure AD integration. Using Azure AD centralizes the identity management component. Any change in user account or group status is automatically updated in access to the AKS cluster. Scope users or groups to the minimum permissions amount using [Roles, ClusterRoles, or Bindings](#use-kubernetes-role-based-access-control-kubernetes-rbac).

Your Kubernetes cluster developers and application owners need access to different resources. Kubernetes lacks an identity management solution for you to control the resources with which users can interact. Instead, you typically integrate your cluster with an existing identity solution. Enter Azure AD: an enterprise-ready identity management solution that integrates with AKS clusters.

With Azure AD-integrated clusters in AKS, you create *Roles* or *ClusterRoles* defining access permissions to resources. You then *bind* the roles to users or groups from Azure AD. Learn more about these Kubernetes RBAC in [the next section](#use-kubernetes-role-based-access-control-kubernetes-rbac). Azure AD integration and how you control access to resources can be seen in the following diagram:

![Cluster-level authentication for Azure Active Directory integration with AKS](media/operator-best-practices-identity/cluster-level-authentication-flow.png)

1. Developer authenticates with Azure AD.
1. The Azure AD token issuance endpoint issues the access token.
1. The developer performs an action using the Azure AD token, such as `kubectl create pod`.
1. Kubernetes validates the token with Azure AD and fetches the developer's group memberships.
1. Kubernetes RBAC and cluster policies are applied.
1. Developer's request is successful based on previous validation of Azure AD group membership and Kubernetes RBAC and policies.

To create an AKS cluster that uses Azure AD, see [Integrate Azure Active Directory with AKS][aks-aad].

## Use Kubernetes role-based access control (Kubernetes RBAC)

> **Best practice guidance**
> 
> Define user or group permissions to cluster resources with Kubernetes RBAC. Create roles and bindings that assign the least amount of permissions required. Integrate with Azure AD to automatically update any user status or group membership change and keep access to cluster resources current.

In Kubernetes, you provide granular access control to cluster resources. You define permissions at the cluster level, or to specific namespaces. You determine what resources can be managed and with what permissions. You then apply these roles to users or groups with a binding. For more information about *Roles*, *ClusterRoles*, and *Bindings*, see [Access and identity options for Azure Kubernetes Service (AKS)][aks-concepts-identity].

As an example, you create a role with full access to resources in the namespace named *finance-app*, as shown in the following example YAML manifest:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: finance-app-full-access-role
  namespace: finance-app
rules:
- apiGroups: [""]
  resources: ["*"]
  verbs: ["*"]
```

You then create a RoleBinding and bind the Azure AD user *developer1\@contoso.com* to the RoleBinding, as shown in the following YAML manifest:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: finance-app-full-access-role-binding
  namespace: finance-app
subjects:
- kind: User
  name: developer1@contoso.com
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: finance-app-full-access-role
  apiGroup: rbac.authorization.k8s.io
```

When *developer1\@contoso.com* is authenticated against the AKS cluster, they have full permissions to resources in the *finance-app* namespace. In this way, you logically separate and control access to resources. Use Kubernetes RBAC in conjunction with Azure AD-integration.

To see how to use Azure AD groups to control access to Kubernetes resources using Kubernetes RBAC, see [Control access to cluster resources using role-based access control and Azure Active Directory identities in AKS][azure-ad-rbac].

## Use Azure RBAC 
> **Best practice guidance** 
> 
> Use Azure RBAC to define the minimum required user and group permissions to AKS resources in one or more subscriptions.

There are two levels of access needed to fully operate an AKS cluster: 
1. Access the AKS resource on your Azure subscription. 

    This access level allows you to:
      * Control scaling or upgrading your cluster using the AKS APIs
      * Pull your `kubeconfig`.

    To see how to control access to the AKS resource and the `kubeconfig`, see [Limit access to cluster configuration file](control-kubeconfig-access.md).

2. Access to the Kubernetes API. 
    
    This access level is controlled either by:
    * [Kubernetes RBAC](#use-kubernetes-role-based-access-control-kubernetes-rbac) (traditionally) or 
    * By integrating Azure RBAC with AKS for kubernetes authorization.

    To see how to granularly give permissions to the Kubernetes API using Azure RBAC, see [Use Azure RBAC for Kubernetes authorization](manage-azure-rbac.md).

## Use Pod-managed Identities

> **Best practice guidance** 
> 
> Don't use fixed credentials within pods or container images, as they are at risk of exposure or abuse. Instead, use *pod identities* to automatically request access using a central Azure AD identity solution. 

> [!NOTE]
> Pod identities are intended for use with Linux pods and container images only. Pod-managed identities support for Windows containers is coming soon.

To access other Azure services, like Cosmos DB, Key Vault, or Blob Storage, the pod needs access credentials. You could define access credentials with the container image or inject them as a Kubernetes secret. Either way, you would need to manually create and assign them. Usually, these credentials are reused across pods and aren't regularly rotated.

With pod-managed identities for Azure resources, you automatically request access to services through Azure AD. Pod-managed identities is now currently in preview for AKS. Please refer to the [Use Azure Active Directory pod-managed identities in Azure Kubernetes Service (Preview)[( https://docs.microsoft.com/azure/aks/use-azure-ad-pod-identity) documentation to get started. 

Instead of manually defining credentials for pods, pod-managed identities request an access token in real time, using it to access only their assigned services. In AKS, there are two components that handle the operations to allow pods to use managed identities:

* **The Node Management Identity (NMI) server** is a pod that runs as a DaemonSet on each node in the AKS cluster. The NMI server listens for pod requests to Azure services.
* **The Azure Resource Provider** queries the Kubernetes API server and checks for an Azure identity mapping that corresponds to a pod.

When pods request access to an Azure service, network rules redirect the traffic to the NMI server. 
1. The NMI server:
    * Identifies pods requesting access to Azure services based on their remote address.
    * Queries the Azure Resource Provider. 
1. The Azure Resource Provider checks for Azure identity mappings in the AKS cluster.
1. The NMI server requests an access token from Azure AD based on the pod's identity mapping. 
1. Azure AD provides access to the NMI server, which is returned to the pod. 
    * This access token can be used by the pod to then request access to services in Azure.

In the following example, a developer creates a pod that uses a managed identity to request access to Azure SQL Database:

![Pod identities allow a pod to automatically request access to other services](media/operator-best-practices-identity/pod-identities.png)

1. Cluster operator creates a service account to map identities when pods request access to services.
1. The NMI server is deployed to relay any pod requests, along with the Azure Resource Provider, for access tokens to Azure AD.
1. A developer deploys a pod with a managed identity that requests an access token through the NMI server.
1. The token is returned to the pod and used to access Azure SQL Database

> [!NOTE]
> Pod-managed identities is currently in preview status.

To use Pod-managed identities, see [Use Azure Active Directory pod-managed identities in Azure Kubernetes Service (Preview)]( https://docs.microsoft.com/azure/aks/use-azure-ad-pod-identity).

## Next steps

This best practices article focused on authentication and authorization for your cluster and resources. To implement some of these best practices, see the following articles:

* [Integrate Azure Active Directory with AKS][aks-aad]
* [Use Azure Active Directory pod-managed identities in Azure Kubernetes Service (Preview)]( https://docs.microsoft.com/azure/aks/use-azure-ad-pod-identity)

For more information about cluster operations in AKS, see the following best practices:

* [Multi-tenancy and cluster isolation][aks-best-practices-cluster-isolation]
* [Basic Kubernetes scheduler features][aks-best-practices-scheduler]
* [Advanced Kubernetes scheduler features][aks-best-practices-advanced-scheduler]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity

<!-- INTERNAL LINKS -->
[aks-concepts-identity]: concepts-identity.md
[aks-aad]: azure-ad-integration-cli.md
[managed-identities:]: ../active-directory/managed-identities-azure-resources/overview.md
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[azure-ad-rbac]: azure-ad-rbac.md
