---
title: BareMetal Instance units in Azure
description: Learn how to identify and interact with BareMetal Instance units through the Azure portal.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: shortpatti
manager: gwallace
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/18/2020
ms.author: v-patsho
---

# Manage BareMetal Instances through the Azure portal
 
This article shows how the [Azure portal](https://portal.azure.com/) displays BareMetal Instances. This article also shows you the activities you can do in the Azure portal with your deployed BareMetal Instance units. 
 
## Register the resource provider
An Azure resource provider for BareMetal Instances provides visibility of the instances in the Azure portal, currently in public preview. By default, the Azure subscription you use for BareMetal Instance deployments registers the *BareMetalInfrastructure* resource provider. If you don't see your deployed BareMetal Instance units, you must register the resource provider with your subscription. There are two ways to register the BareMetal Instance resource provider:
 
* [Azure CLI](#azure-cli)
 
* [Azure portal](#azure-portal)
 
### Azure CLI
 
Sign in to the Azure subscription you use for the BareMetal Instance deployment through the Azure CLI. You can register the BareMetalInfrastructure resource provider with:

```azurecli-interactive
az provider register --namespace Microsoft.BareMetalInfrastructure
```
 
For more information, see the article [Azure resource providers and types](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell).
 
### Azure portal
 
You can register the BareMetalInfrastructure resource provider through the Azure portal.
 
You'll need to list your subscription in the Azure portal and then double-click on the subscription used to deploy your BareMetal Instance units.
 
1. Sign in to the [Azure portal](https://portal.azure.com).

1. On the Azure portal menu, select **All services**.

1. In the **All services** box, enter **subscription**, and then select **Subscriptions**.

1. Select the subscription from the subscription list to view.

1. Select **Resource providers** and enter **BareMetalInfrastructure** into the search. The resource provider should be **Registered**, as the image shows.
 
>[!NOTE]
>If the resource provider is not registered, select **Register**.
 
:::image type="content" source="media/baremetal-infrastructure-portal/register-resource-provider-azure-portal.png" alt-text="Screenshot that shows the BareMetal Instance unit registered":::
 
## BareMetal Instance units in the Azure portal
 
When you submit a BareMetal Instance deployment request, you'll specify the Azure subscription that you're connecting to the BareMetal Instances. Use the same subscription you use to deploy the application layer that works against the BareMetal Instance units.
 
During the deployment of your BareMetal Instances, a new [Azure resource group](../../../azure-resource-manager/management/manage-resources-portal.md) gets created in the Azure subscription you used in the deployment request. This new resource group lists all your BareMetal Instance units you've deployed in the specific subscription.

1. In the BareMetal subscription, in the Azure portal, select **Resource groups**.
 
   :::image type="content" source="media/baremetal-infrastructure-portal/view-baremetal-instance-units-azure-portal.png" alt-text="Screenshot that shows the list of Resource Groups":::

1. In the list, locate the new resource group.
 
   :::image type="content" source="media/baremetal-infrastructure-portal/filter-resource-groups.png" alt-text="Screenshot that shows the BareMetal Instance unit in a filtered Resource groups list" lightbox="media/baremetal-infrastructure-portal/filter-resource-groups.png":::
   
   >[!TIP]
   >You can filter on the subscription you used to deploy the BareMetal Instance. After you filter to the proper subscription, you might have a long list of resource groups. Look for one with a post-fix of **-Txxx** where xxx is three digits like **-T250**.

1. Select the new resource group to show the details of it. The image shows one BareMetal Instance unit deployed.
   
   >[!NOTE]
   >If you deployed several BareMetal Instance tenants under the same Azure subscription, you would see multiple Azure resource groups.
 
## View the attributes of a single instance
 
You can view the details of a single unit. In the list of the BareMetal instance, select the single instance you want to view.
 
:::image type="content" source="media/baremetal-infrastructure-portal/view-attributes-single-baremetal-instance.png" alt-text="Screenshot that shows the BareMetal Instance unit attributes of a single instance" lightbox="media/baremetal-infrastructure-portal/view-attributes-single-baremetal-instance.png":::
 
The attributes in the image don't look much different than the Azure virtual machine (VM) attributes. On the left, you'll see the Resource group, Azure region, and subscription name and ID. If you assigned tags, then you'll see them here as well. By default, the BareMetal Instance units don't have tags assigned.
 
On the right, you'll see the unit's name, operating system (OS), IP address, and SKU that shows the number of CPU threads and memory. You'll also see the power state and hardware version (revision of the BareMetal Instance stamp). The power state indicates if the hardware unit is powered on or off. The operating system details, however, don't indicate whether it's up and running.
 
The possible hardware revisions are:
 
* Revision 3
 
* Revision 4
 
* Revision 4.2
 
>[!NOTE]
>Revision 4.2 is the latest rebranded BareMetal Infrastructure using the Revision 4 architecture. It has significant improvements in network latency between Azure VMs and BareMetal instance units deployed in Revision 4 stamps or rows.
 
Also, on the right side, you'll find the [Azure Proximity Placement Group's](../../../virtual-machines/linux/co-location.md) name, which is created automatically for each deployed BareMetal Instance unit. Reference the Proximity Placement Group when you deploy the Azure VMs that host the application layer. When you use the Proximity Placement Group associated with the BareMetal Instance unit, you ensure that the Azure VMs get deployed close to the BareMetal Instance unit.
 
>[!TIP]
>To locate the application layer in the same Azure datacenter as Revision 4.x, see [Azure proximity placement groups for optimal network latency](../../../virtual-machines/workloads/sap/sap-proximity-placement-scenarios.md).
 
## Check activities of a single instance
 
You can check the activities of a single unit. One of the main activities recorded are restarts of the unit. The data listed includes the activity's status, timestamp the activity triggered, subscription ID, and the Azure user who triggered the activity.
 
:::image type="content" source="media/baremetal-infrastructure-portal/check-activities-single-baremetal-instance.png" alt-text="Screenshot that shows the BareMetal Instance unit activities" lightbox="media/baremetal-infrastructure-portal/check-activities-single-baremetal-instance.png":::
 
Changes to the unit's metadata in Azure also get recorded in the Activity log. Besides the restart initiated, you can see the activity of **Write BareMetallnstances**. This activity makes no changes on the BareMetal Instance unit itself but documents the changes to the unit's metadata in Azure.
 
Another activity that gets recorded is when you add or delete a [tag](../../../azure-resource-manager/management/tag-resources.md) to an instance.
 
## Add and delete an Azure tag to an instance
 
You can add Azure tags to a BareMetal Instance unit or delete them. The way tags get assigned doesn't differ from assigning tags to VMs. As with VMs, the tags exist in the Azure metadata, and for BareMetal Instances, they have the same restrictions as the tags for VMs.
 
Deleting tags work the same way as with VMs. Applying and deleting a tag are listed in the BareMetal Instance unit's Activity log.
 
## Check properties of an instance
 
When you acquire the instances, you can go to the Properties section to view the data collected about the instances. The data collected includes the Azure connectivity, storage backend, ExpressRoute circuit ID, unique resource ID, and the subscription ID. You'll use this information in support requests or when setting up storage snapshot configuration.
 
Another critical piece of information you'll see is the storage NFS IP address. It isolates your storage to your **tenant** in the BareMetal Instance stack. You'll use this IP address when you edit the [configuration file for storage snapshot backups](../../../virtual-machines/workloads/sap/hana-backup-restore.md#set-up-storage-snapshots).
 
:::image type="content" source="media/baremetal-infrastructure-portal/baremetal-instance-properties.png" alt-text="Screenshot that shows the BareMetal Instance property settings" lightbox="media/baremetal-infrastructure-portal/baremetal-instance-properties.png":::
 
## Restart a unit through the Azure portal
 
There are various situations where the OS won't finish a restart, which requires a power restart of the BareMetal Instance unit. You can do a power restart of the unit directly from the Azure portal:
 
Select **Restart** and then **Yes** to confirm the restart of the unit.
 
:::image type="content" source="media/baremetal-infrastructure-portal/baremetal-instance-restart.png" alt-text="Screenshot that shows how to restart the BareMetal Instance unit":::
 
When you restart a BareMetal Instance unit, you'll experience a delay. During this delay, the power state moves from **Starting** to **Started**, which means the OS has started up completely. As a result, after a restart, you can't log into the unit as soon as the state switches to **Started**.
 
>[!IMPORTANT]
>Depending on the amount of memory in your BareMetal Instance unit, a restart and a reboot of the hardware and the operating system can take up to one hour.
 
## Open a support request for BareMetal Instances
 
You can submit support requests specifically for a BareMetal Instance unit.
1. In Azure portal, under **Help + Support**, create a **[New support request](https://rc.portal.azure.com/#create/Microsoft.Support)** and provide the following information for the ticket:
 
   - **Issue type:** Select an issue type
 
   - **Subscription:** Select your subscription
 
   - **Service:** BareMetal Infrastructure
 
   - **Resource:** Provide the name of the instance
 
   - **Summary:** Provide a summary of your request
 
   - **Problem type:** Select a problem type
 
   - **Problem subtype:** Select a subtype for the problem

1. Select the **Solutions** tab to find a solution to your problem. If you can't find a solution, go to the next step.

1. Select the **Details** tab and select whether the issue is with VMs or the BareMetal Instance units. This information helps direct the support request to the correct specialists.

1. Indicate when the problem began and select the instance region.

1. Provide more details about the request and upload a file if needed.

1. Select **Review + Create** to submit the request.
 
It takes up to five business days for a support representative to confirm your request.

## Next steps

If you want to learn more about BareMetal, see [BareMetal workload types](../../../virtual-machines/workloads/sap/get-started.md).