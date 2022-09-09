---
title: Secure inferencing environments with virtual networks
titleSuffix: Azure Machine Learning
description: Use an isolated Azure Virtual Network to secure your Azure Machine Learning inferencing environment.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: peterlu
author: peterclu
ms.date: 09/24/2020
ms.custom: contperfq4, tracking-python, contperfq1

---

# Secure an Azure Machine Learning inferencing environment with virtual networks

In this article, you learn how to secure inferencing environments with a virtual network in Azure Machine Learning.

This article is part four of a five-part series that walks you through securing an Azure Machine Learning workflow. We highly recommend that you read through [Part one: VNet overview](how-to-network-security-overview.md) to understand the overall architecture first. 

See the other articles in this series:

[1. VNet overview](how-to-network-security-overview.md) > [Secure the workspace](how-to-secure-workspace-vnet.md) > [3. Secure the training environment](how-to-secure-training-vnet.md) > **4. Secure the inferencing environment** > [5. Enable studio functionality](how-to-enable-studio-virtual-network.md)

In this article you learn how to secure the following inferencing resources in a virtual network:
> [!div class="checklist"]
> - Default Azure Kubernetes Service (AKS) cluster
> - Private AKS cluster
> - AKS cluster with private link
> - Azure Container Instances (ACI)


## Prerequisites

+ Read the [Network security overview](how-to-network-security-overview.md) article to understand common virtual network scenarios and overall virtual network architecture.

+ An existing virtual network and subnet to use with your compute resources.

+ To deploy resources into a virtual network or subnet, your user account must have permissions to the following actions in Azure role-based access controls (RBAC):

    - "Microsoft.Network/virtualNetworks/join/action" on the virtual network resource.
    - "Microsoft.Network/virtualNetworks/subnet/join/action" on the subnet resource.

    For more information on RBAC with networking, see the [Networking built-in roles](/azure/role-based-access-control/built-in-roles#networking)

<a id="aksvnet"></a>

## Azure Kubernetes Service

To use an AKS cluster in a virtual network, the following network requirements must be met:

> [!div class="checklist"]
> * Follow the prerequisites in [Configure advanced networking in Azure Kubernetes Service (AKS)](../aks/configure-azure-cni.md#prerequisites).
> * The AKS instance and the virtual network must be in the same region. If you secure the Azure Storage Account(s) used by the workspace in a virtual network, they must be in the same virtual network as the AKS instance too.


To add AKS in a virtual network to your workspace, use the following steps:

1. Sign in to [Azure Machine Learning studio](https://ml.azure.com/), and then select your subscription and workspace.

1. Select __Compute__ on the left.

1. Select __Inference clusters__ from the center, and then select __+__.

1. In the __New Inference Cluster__ dialog, select __Advanced__ under __Network configuration__.

1. To configure this compute resource to use a virtual network, perform the following actions:

    1. In the __Resource group__ drop-down list, select the resource group that contains the virtual network.
    1. In the __Virtual network__ drop-down list, select the virtual network that contains the subnet.
    1. In the __Subnet__ drop-down list, select the subnet.
    1. In the __Kubernetes Service address range__ box, enter the Kubernetes service address range. This address range uses a Classless Inter-Domain Routing (CIDR) notation IP range to define the IP addresses that are available for the cluster. It must not overlap with any subnet IP ranges (for example, 10.0.0.0/16).
    1. In the __Kubernetes DNS service IP address__ box, enter the Kubernetes DNS service IP address. This IP address is assigned to the Kubernetes DNS service. It must be within the Kubernetes service address range (for example, 10.0.0.10).
    1. In the __Docker bridge address__ box, enter the Docker bridge address. This IP address is assigned to Docker Bridge. It must not be in any subnet IP ranges, or the Kubernetes service address range (for example, 172.17.0.1/16).

   ![Azure Machine Learning: Machine Learning Compute virtual network settings](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. Make sure that the NSG group that controls the virtual network has an inbound security rule enabled for the scoring endpoint so that it can be called from outside the virtual network.
   > [!IMPORTANT]
   > Keep the default outbound rules for the NSG. For more information, see the default security rules in [Security groups](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).

   [![An inbound security rule](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png#lightbox)

You can also use the Azure Machine Learning SDK to add Azure Kubernetes Service in a virtual network. If you already have an AKS cluster in a virtual network, attach it to the workspace as described in [How to deploy to AKS](how-to-deploy-and-where.md). The following code creates a new AKS instance in the `default` subnet of a virtual network named `mynetwork`:

```python
from azureml.core.compute import ComputeTarget, AksCompute

# Create the compute configuration and set virtual network information
config = AksCompute.provisioning_configuration(location="eastus2")
config.vnet_resourcegroup_name = "mygroup"
config.vnet_name = "mynetwork"
config.subnet_name = "default"
config.service_cidr = "10.0.0.0/16"
config.dns_service_ip = "10.0.0.10"
config.docker_bridge_cidr = "172.17.0.1/16"

# Create the compute target
aks_target = ComputeTarget.create(workspace=ws,
                                  name="myaks",
                                  provisioning_configuration=config)
```

When the creation process is completed, you can run inference, or model scoring, on an AKS cluster behind a virtual network. For more information, see [How to deploy to AKS](how-to-deploy-and-where.md).

## Secure VNet traffic

There are two approaches to isolate traffic to and from the AKS cluster to the virtual network:

* __Private AKS cluster__: This approach uses Azure Private Link to create a private endpoint for the AKS cluster within the VNet.
* __Internal AKS load balancer__: This approach configures the load balancer for the cluster to use an internal IP address in the VNet.

> [!WARNING]
> Both configurations are different ways to achieve the same goal (securing traffic to the AKS cluster within the VNet). **Use one or the other, but not both**.

### Private AKS cluster

By default, AKS clusters have a control plane, or API server, with public IP addresses. You can configure AKS to use a private control plane by creating a private AKS cluster. For more information, see [Create a private Azure Kubernetes Service cluster](../aks/private-clusters.md).

After you create the private AKS cluster, [attach the cluster to the virtual network](how-to-create-attach-kubernetes.md) to use with Azure Machine Learning.

> [!IMPORTANT]
> Before using a private link enabled AKS cluster with Azure Machine Learning, you must open a support incident to enable this functionality. For more information, see [Manage and increase quotas](how-to-manage-quotas.md#private-endpoint-and-private-dns-quota-increases).

## Internal AKS load balancer

By default, AKS deployments use a [public load balancer](../aks/load-balancer-standard.md). In this section, you learn how to configure AKS to use an internal load balancer. An internal (or private) load balancer is used where only private IPs are allowed as frontend. Internal load balancers are used to load balance traffic inside a virtual network

A private load balancer is enabled by configuring AKS to use an _internal load balancer_. 

#### Network contributor role

> [!IMPORTANT]
> If you create or attach an AKS cluster by providing a virtual network you previously created, you must grant the service principal (SP) or managed identity for your AKS cluster the _Network Contributor_ role to the resource group that contains the virtual network. This must be done before you try to change the internal load balancer to private IP.
>
> To add the identity as network contributor, use the following steps:

1. To find the service principal or managed identity ID for AKS, use the following Azure CLI commands. Replace `<aks-cluster-name>` with the name of the cluster. Replace `<resource-group-name>` with the name of the resource group that _contains the AKS cluster_:

    ```azurecli-interactive
    az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query servicePrincipalProfile.clientId
    ``` 

    If this command returns a value of `msi`, use the following command to identify the principal ID for the managed identity:

    ```azurecli-interactive
    az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query identity.principalId
    ```

1. To find the ID of the resource group that contains your virtual network, use the following command. Replace `<resource-group-name>` with the name of the resource group that _contains the virtual network_:

    ```azurecli-interactive
    az group show -n <resource-group-name> --query id
    ```

1. To add the service principal or managed identity as a network contributor, use the following command. Replace `<SP-or-managed-identity>` with the ID returned for the service principal or managed identity. Replace `<resource-group-id>` with the ID returned for the resource group that contains the virtual network:

    ```azurecli-interactive
    az role assignment create --assignee <SP-or-managed-identity> --role 'Network Contributor' --scope <resource-group-id>
    ```
For more information on using the internal load balancer with AKS, see [Use internal load balancer with Azure Kubernetes Service](/azure/aks/internal-lb).

#### Enable private load balancer

> [!IMPORTANT]
> You cannot enable private IP when creating the Azure Kubernetes Service cluster in Azure Machine Learning studio. You can create one with an internal load balancer when using the Python SDK or Azure CLI extension for machine learning.

The following examples demonstrate how to __create a new AKS cluster with a private IP/internal load balancer__ using the SDK and CLI:

# [Python](#tab/python)

```python
import azureml.core
from azureml.core.compute import AksCompute, ComputeTarget

# Verify that cluster does not exist already
try:
    aks_target = AksCompute(workspace=ws, name=aks_cluster_name)
    print("Found existing aks cluster")

except:
    print("Creating new aks cluster")

    # Subnet to use for AKS
    subnet_name = "default"
    # Create AKS configuration
    prov_config=AksCompute.provisioning_configuration(load_balancer_type="InternalLoadBalancer")
    # Set info for existing virtual network to create the cluster in
    prov_config.vnet_resourcegroup_name = "myvnetresourcegroup"
    prov_config.vnet_name = "myvnetname"
    prov_config.service_cidr = "10.0.0.0/16"
    prov_config.dns_service_ip = "10.0.0.10"
    prov_config.subnet_name = subnet_name
    prov_config.docker_bridge_cidr = "172.17.0.1/16"

    # Create compute target
    aks_target = ComputeTarget.create(workspace = ws, name = "myaks", provisioning_configuration = prov_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
```

# [Azure CLI](#tab/azure-cli)

```azurecli
az ml computetarget create aks -n myaks --load-balancer-type InternalLoadBalancer
```

For more information, see the [az ml computetarget create aks](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-computetarget-create-aks) reference.

---

When __attaching an existing cluster__ to your workspace, you must wait until after the attach operation to configure the load balancer. For information on attaching a cluster, see [Attach an existing AKS cluster](how-to-create-attach-kubernetes.md).

After attaching the existing cluster, you can then update the cluster to use an internal load balancer/private IP:

```python
import azureml.core
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute import AksCompute

# ws = workspace object. Creation not shown in this snippet
aks_target = AksCompute(ws,"myaks")

# Change to the name of the subnet that contains AKS
subnet_name = "default"
# Update AKS configuration to use an internal load balancer
update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", subnet_name)
aks_target.update(update_config)
# Wait for the operation to complete
aks_target.wait_for_completion(show_output = True)
```

## Enable Azure Container Instances (ACI)

Azure Container Instances are dynamically created when deploying a model. To enable Azure Machine Learning to create ACI inside the virtual network, you must enable __subnet delegation__ for the subnet used by the deployment.

> [!WARNING]
> When using Azure Container Instances in a virtual network, the virtual network must be in the same resource group as your Azure Machine Learning workspace.
>
> When using Azure Container Instances inside the virtual network, the Azure Container Registry (ACR) for your workspace cannot also be in the virtual network.

To use ACI in a virtual network to your workspace, use the following steps:

1. To enable subnet delegation on your virtual network, use the information in the [Add or remove a subnet delegation](../virtual-network/manage-subnet-delegation.md) article. You can enable delegation when creating a virtual network, or add it to an existing network.

    > [!IMPORTANT]
    > When enabling delegation, use `Microsoft.ContainerInstance/containerGroups` as the __Delegate subnet to service__ value.

2. Deploy the model using [AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py&preserve-view=true#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none--vnet-name-none--subnet-name-none-&preserve-view=true), use the `vnet_name` and `subnet_name` parameters. Set these parameters to the virtual network name and subnet where you enabled delegation.


## Next steps

This article is part three in a four-part virtual network series. See the rest of the articles to learn how to secure a virtual network:

* [Part 1: Virtual network overview](how-to-network-security-overview.md)
* [Part 2: Secure the workspace resources](how-to-secure-workspace-vnet.md)
* [Part 3: Secure the training environment](how-to-secure-training-vnet.md)
* [Part 5:Enable studio functionality](how-to-enable-studio-virtual-network.md)