---
title: Tutorial - Deploy vSphere Cluster in Azure
description: Learn to deploy a vSphere Cluster in Azure using Azure VMWare Solution (AVS)
ms.topic: tutorial
ms.date: 07/15/2020
---

# Tutorial: Deploy an AVS private cloud in Azure

Azure VMware Solution (AVS) gives you the ability to deploy a vSphere cluster in Azure. The minimum initial deployment is three hosts. Additional hosts can be added one at a time, up to a maximum of 16 hosts per cluster. 

Because AVS doesn't allow you to manage your private cloud with your on-premises vCenter at launch, additional configuration of and connection to a local vCenter instance, virtual network, and more is needed. These procedures and related prerequisites are covered in this tutorial.

In this tutorial, you learn how to:

> [!div class="checklist"]
> * Create an AVS private cloud
> * Verify the private cloud deployed

## Prerequisites

- An Azure account with an active subscription. [Create an account for free](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Appropriate administrative rights and permission to create a private cloud.
- Ensure you have the appropriate networking configured as described in [Tutorial: Network checklist](tutorial-network-checklist.md).

## Register the resource provider

To use AVS, you must first register the resource provider with your subscription.

```
azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

For additional ways to register the resource provider, see [Azure resource providers and types](../azure-resource-manager/management/resource-providers-and-types.md).


## Create a Private Cloud

You can create an AVS private cloud by using the [Azure portal](#azure-portal) or by using the [Azure CLI](#azure-cli).

### Azure portal

1. Sign in to the [Azure portal](https://portal.azure.com).

1. Select **Create a new resource**. In the **Search the Marketplace** text box type `Azure VMware Solution`, and select **Azure VMware Solution** from the list. On the **Azure VMware Solution** window, select **Create**

1. On the **Basics** tab, enter values for the fields. The following table lists the properties for the fields.

   | Field   | Value  |
   | ---| --- |
   | **Subscription** | The subscription you plan to use for the deployment.|
   | **Resource group** | The resource group for your private cloud resources. |
   | **Location** | Select a location, such as **east us**.|
   | **Resource name** | The name of your AVS private cloud. |
   | **SKU** | Select the following SKU value: AV36 |
   | **Hosts** | The number of hosts to add to the private cloud cluster. The default value is 3, which can be raised or lowered after deployment.  |
   | **vCenter admin password** | Enter a cloud administrator password. |
   | **NSX-T manager password** | Enter an NSX-T administrator password. |
   | **Address block** | Enter an IP address block for the CIDR network for the private cloud, for example, 10.175.0.0/22. |

   :::image type="content" source="./media/tutorial-create-private-cloud/create-private-cloud.png" alt-text="create a private cloud" border="true":::

1. Once finished, select **Review + Create**. On the next screen, verify the information entered. If the information is all correct, select **Create**.

   > [!NOTE]
   > This step takes roughly two hours. 

1. Verify that the deployment was successful. Navigate to the resource group you created and select your private cloud.  You'll see the status of **Succeeded** when the deployment has completed. 

   :::image type="content" source="./media/tutorial-create-private-cloud/validate-deployment.png" alt-text="Validate the private cloud deployed" border="true":::

### Azure CLI

Instead of the Azure portal to create an AVS private cloud, you can use the Azure CLI using the Azure Cloud Shell. It's a free interactive shell with common Azure tools preinstalled and configured to use with your account. 

#### Open Azure Cloud Shell

To open the Cloud Shell, select **Try it** from the upper right corner of a code block. You can also launch Cloud Shell in a separate browser tab by going to [https://shell.azure.com/bash](https://shell.azure.com/bash). Select **Copy** to copy the blocks of code, paste it into the Cloud Shell, and press **Enter** to run it.

#### Create a resource group

Create a resource group with the [az group create](/cli/azure/group) command. An Azure resource group is a logical container into which Azure resources are deployed and managed. The following example creates a resource group named *myResourceGroup* in the *eastus* location:

```
azurecli-interactive
az group create --name myResourceGroup --location eastus
```

#### Create a private cloud

Provide a resource group name, a name for the private cloud, a location, the size of the cluster.

| Property  | Description  |
| --------- | ------------ |
| **-g** (Resource Group name)     | The name of the resource group for your private cloud resources.        |
| **-n** (Private Cloud name)     | The name of your AVS private cloud.        |
| **--location**     | The location used for your private cloud.         |
| **--cluster-size**     | The size of the cluster. The minimum value is 3.         |
| **--network-block**     | The CIDR IP address network block to use for your private cloud. The address block shouldn't overlap with address blocks used in other virtual networks that are in your subscription and on-premises networks.        |
| **--sku** | The SKU value: AV36 |

```
azurecli-interactive
az vmware private-cloud create -g myResourceGroup -n myPrivateCloudName --location eastus --cluster-size 3 --network-block xx.xx.xx.xx/22 --sku AV36
```

## Delete a private cloud (Azure portal)

If you have an AVS private cloud that you no longer need, you can delete it. When you delete a private cloud, all clusters, along with all their components, are deleted.

To do so, navigate to your private cloud in the Azure portal, and select **Delete**. On the confirmation page, confirm with the name of the private cloud and select **Yes**.

> [!CAUTION]
> Deleting the private cloud is an irreversible operation. Once the private cloud is deleted, the data cannot be recovered as it terminates all running workloads, components and destroys all private cloud data and configuration settings, including public IP addresses. 

## Next steps

In this tutorial, you learned how to:

> [!div class="checklist"]
> * Create an AVS private cloud
> * Verified the Private Cloud deployed

Continue to the next tutorial to learn how to create a virtual network for use with your private cloud as part of setting up local management for your private cloud clusters.

> [!div class="nextstepaction"]
> [Create a Virtual Network](tutorial-configure-networking.md)
