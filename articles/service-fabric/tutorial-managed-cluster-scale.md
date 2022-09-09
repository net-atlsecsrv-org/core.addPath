---
title: Scale out a Service Fabric managed cluster (preview)
description: In this tutorial, learn how to scale out a node type of a Service Fabric managed cluster.
ms.topic: tutorial
ms.date: 09/28/2020
---

# Tutorial: Scale out a Service Fabric managed cluster (preview)

In this tutorial series we will discuss:

> [!div class="checklist"]
> * [How to deploy a Service Fabric managed cluster.](tutorial-managed-cluster-deploy.md)
> * How to scale out a Service Fabric managed cluster
> * [How to add and remove node types in a Service Fabric managed cluster](tutorial-managed-cluster-add-remove-node-type.md)
> * [How to deploy an application to a Service Fabric managed cluster](tutorial-managed-cluster-deploy-app.md)

This part of the series covers how to:

> [!div class="checklist"]
> * Scale a Service Fabric managed cluster node

## Prerequisites

* A Service Fabric managed cluster (see [*Deploy a managed cluster*](tutorial-managed-cluster-deploy.md)).
* [Azure PowerShell 4.7.0](/powershell/azure/release-notes-azureps?preserve-view=true&view=azps-4.7.0#azservicefabric) or later (see [*Install Azure PowerShell*](/powershell/azure/install-az-ps?preserve-view=true&view=azps-4.7.0)).

## Scale a Service Fabric managed cluster
Change the instance count to increase or decrease the number of nodes on the node type that you would like to scale. You can find node type names in the Azure Resource Manager template (ARM template) from your cluster deployment, or in the Service Fabric Explorer.  

> [!NOTE]
> If the node type is primary you will not be able to go below 3 nodes for a Basic SKU cluster, and 5 nodes for a Standard SKU cluster.

```powershell
$resourceGroup = "myResourceGroup"
$clusterName = "mysfcluster"
$nodeTypeName = "FE"
$instanceCount = "7"

Set-AzServiceFabricManagedNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName -name $nodeTypeName -InstanceCount $instanceCount -Verbose
```

The cluster will begin upgrading automatically and after a few minutes you will see the additional nodes.

## Next steps

In this step we scaled a node type on a Service Fabric managed cluster. To learn more about adding and removing node types, see:

> [!div class="nextstepaction"]
> [Add and remove node types to a Service Fabric managed cluster](tutorial-managed-cluster-add-remove-node-type.md)