---
title: Start and Stop an Azure Kubernetes Service (AKS)
description: Learn how to stop or start an Azure Kubernetes Service (AKS) cluster.
services: container-service
ms.topic: article
ms.date: 09/24/2020
author: palma21

---

# Stop and Start an Azure Kubernetes Service (AKS) cluster (preview)

Your AKS workloads may not need to run continuously, for example a development cluster that is used only during business hours. This leads to times where your Azure Kubernetes Service (AKS) cluster might be idle, running no more than the system components. You can reduce the cluster footprint by [scaling all the `User` node pools to 0](scale-cluster.md#scale-user-node-pools-to-0), but your [`System` pool](use-system-pools.md) is still required to run the system components while the cluster is running. 
To optimize your costs further during these periods, you can completely turn off (stop) your cluster. This action will stop your control plane and agent nodes altogether, allowing you to save on all the compute costs, while maintaining all your objects and cluster state stored for when you start it again. You can then pick up right where you left of after a weekend or to have your cluster running only while you run your batch jobs.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## Before you begin

This article assumes that you have an existing AKS cluster. If you need an AKS cluster, see the AKS quickstart [using the Azure CLI][aks-quickstart-cli] or [using the Azure portal][aks-quickstart-portal].


### Limitations

When using the cluster start/stop feature, the following restrictions apply:

- This feature is only supported for Virtual Machine Scale Sets backed clusters.
- During preview, this feature is not supported for Private clusters.
- The cluster state of a stopped AKS cluster is preserved for up to 12 months. If your cluster is stopped for more than 12 months, the cluster state cannot be recovered. For more information, see the [AKS Support Policies](support-policies.md).
- During preview, you need to stop the cluster autoscaler (CA) before attempting to stop the cluster.
- You can only start or delete a stopped AKS cluster. To perform any operation like scale or upgrade, start your cluster first.

### Install the `aks-preview` Azure CLI 

You also need the *aks-preview* Azure CLI extension version 0.4.64 or later. Install the *aks-preview* Azure CLI extension by using the [az extension add][az-extension-add] command. Or install any available updates by using the [az extension update][az-extension-update] command.

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 

### Register the `StartStopPreview` preview feature

To use the start/stop cluster feature, you must enable the `StartStopPreview` feature flag on your subscription.

Register the `StartStopPreview` feature flag by using the [az feature register][az-feature-register] command, as shown in the following example:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "StartStopPreview"
```

It takes a few minutes for the status to show *Registered*. Verify the registration status by using the [az feature list][az-feature-list] command:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/StartStopPreview')].{Name:name,State:properties.state}"
```

When ready, refresh the registration of the *Microsoft.ContainerService* resource provider by using the [az provider register][az-provider-register] command:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## Stop an AKS Cluster

You can use the `az aks stop` command to stop a running AKS cluster's nodes and control plane. The following example stops a cluster named *myAKSCluster*:

```azurecli-interactive
az aks stop --name myAKSCluster --resource-group myResourceGroup
```

You can verify when your cluster is stopped by using the [az aks show][az-aks-show] command and confirming the `powerState` shows as `Stopped` as on the below output:

```json
{
[...]
  "nodeResourceGroup": "MC_myResourceGroup_myAKSCluster_westus2",
  "powerState":{
    "code":"Stopped"
  },
  "privateFqdn": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
[...]
}
```

If the `provisioningState` shows `Stopping` that means your cluster hasn't fully stopped yet.

> [!IMPORTANT]
> If you are using [Pod Disruption Budgets](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) the stop operation can take longer as the drain process will take more time to complete.


## Start an AKS Cluster

You can use the `az aks start` command to start a stopped AKS cluster's nodes and control plane. The cluster is restarted with the previous control plane state and number of agent nodes.  
The following example starts a cluster named *myAKSCluster*:

```azurecli-interactive
az aks start --name myAKSCluster --resource-group myResourceGroup
```

You can verify when your cluster has started by using the [az aks show][az-aks-show] command and confirming the `powerState` shows `Running` as on the below output:

```json
{
[...]
  "nodeResourceGroup": "MC_myResourceGroup_myAKSCluster_westus2",
  "powerState":{
    "code":"Running"
  },
  "privateFqdn": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
[...]
}
```

If the `provisioningState` shows `Starting` that means your cluster hasn't fully started yet.


## Next steps

- To learn how to scale `User` pools to 0, see [Scale `User` pools to 0](scale-cluster.md#scale-user-node-pools-to-0).
- To learn how to save costs using Spot instances, see [Add a spot node pool to AKS](spot-node-pool.md).
- To learn more about the AKS support policies, see [AKS support policies](support-policies.md).

<!-- LINKS - external -->

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli&preserve-view=true
[az-extension-add]: /cli/azure/extension?view=azure-cli-latest#az-extension-add&preserve-view=true
[az-extension-update]: /cli/azure/extension?view=azure-cli-latest#az-extension-update&preserve-view=true
[az-feature-register]: /cli/azure/feature?view=azure-cli-latest#az-feature-register&preserve-view=true
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list&preserve-view=true
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register&preserve-view=true
[az-aks-show]: /cli/azure/aks?view=azure-cli-latest#az_aks_show
