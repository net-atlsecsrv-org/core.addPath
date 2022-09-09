---
title: Azure Hybrid Benefit and Linux VMs
description: The Azure Hybrid Benefit allows you to save money on your Linux virtual machines running on Azure.
services: virtual-machines
documentationcenter: ''
author: mathapli
manager: westonh
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/22/2020
ms.author: mathapli
---

# Azure Hybrid Benefit – how it applies for Linux Virtual Machines

## Overview

Azure Hybrid Benefit is a licensing benefit that helps you to significantly reduce the costs of running your Red Hat Enterprise Linux (RHEL) and SUSE Linux Enterprise Server (SLES) virtual machines in the cloud. With this benefit, you only pay for the infrastructure costs of your VM because the software fee is covered by your RHEL or SLES subscription. The benefit is applicable to all RHEL and SLES Marketplace pay-as-you-go (PAYG) images.

> [!IMPORTANT]
> Azure Hybrid Benefit for Linux VMs is available to public now


## Benefit description

Through Azure Hybrid Benefit, you can more easily migrate your on-premise RHEL and SLES servers to Azure by converting existing RHEL and SLES PAYG VMs on Azure to bring-your-own-subscription (BYOS) billing. Typically, VMs deployed from PAYG images on Azure will charge both an infrastructure fee as well as a software fee. With the Azure Hybrid Benefit, PAYG VMs can be converted to a BYOS billing model without a redeployment, avoiding any downtime risk.

:::image type="content" source="./media/ahb-linux/azure-hybrid-benefit-cost.png" alt-text="Azure Hybrid Benefit cost visualization on Linux VMs.":::

Upon enabling the benefit on a RHEL or SLES VM, you will no longer be charged for the additional software fee typically incurred on a PAYG VM. Instead, your VM will begin emitting a BYOS charge, which includes only the compute hardware fee and no software fee.

If you desire, you may also convert a VM which has had the benefit enabled on it back to a PAYG billing model.

## Scope of Azure Hybrid Benefit eligibility for Linux VMs

Azure Hybrid Benefit is available for all RHEL and SLES Marketplace PAYG images. The benefit is not yet available for RHEL or SLES Marketplace BYOS images or custom images.

Reserved Instances, Dedicated Hosts, and SQL hybrid benefits are not eligible for the Azure Hybrid Benefit if you are already using the benefit with Linux VMs.

## How to get started

### Red Hat customers

Azure Hybrid Benefit for RHEL is available to customers who have active/unused RHEL subscriptions that are eligible for use in Azure and who have enabled one or more of those subscriptions for use in Azure with the [Red Hat Cloud Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) program. 

1.  Enable one or more of your eligible RHEL subscriptions for use in Azure using the [Red Hat Cloud Access customer interface](https://access.redhat.com/management/cloud).
1.  The Azure subscription(s) you provided during the Red Hat Cloud Access enablement process will then be permitted to use the Azure Hybrid Benefit feature.
1.  Apply the Azure Hybrid Benefit to any of your existing RHEL PAYG VMs as well as any new RHEL VMs you deploy from Azure Marketplace PAYG images.
1.  Follow recommended [next steps](https://access.redhat.com/articles/5419341) for configuring update sources for your RHEL VMs and for RHEL subscription compliance guidelines.


### SUSE customers

1.    Register with the SUSE Public Cloud program
1.    Apply the benefit to your existing VMs either via Azure CLI
1.    Register your VMs receiving the benefit with a separate source of updates


### Enable and disable the benefit in the Azure CLI

You may use the 'az vm update' command to update existing VMs. For RHEL VMs, run the command with a --license-type parameter of "RHEL_BYOS". For SLES VMs, run the command with a --license-type parameter of "SLES_BYOS".

#### CLI example to enable the benefit:
```azurecli
# This will enable the benefit on a RHEL VM
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS

# This will enable the benefit on a SLES VM
az vm update -g myResourceGroup -n myVmName --license-type SLES_BYOS
```
#### CLI example to disable the benefit:
To disable the benefit, use a license-type value of "None"
```azurecli
# This will disable the benefit on a VM
az vm update -g myResourceGroup -n myVmName --license-type None
```

#### CLI example to enable the benefit on a large number of VMs
To enable the benefit on a large number of VMs, you may use the `--ids` parameter in the Azure CLI.

```azurecli
# This will enable the benefit on a RHEL VM. In this example, ids.txt is an
# existing text file containing a delimited list of resource IDs corresponding
# to the VMs using the benefit
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS --ids $(cat ids.txt)
```

The following examples show two methods of getting a list of resource IDs – one at the resource group level, one at the subscription level.
```azurecli
# To get a list of all the resource IDs in a resource group:
$(az vm list -g MyResourceGroup --query "[].id" -o tsv)

# To get a list of all the resource IDs of VMs in a subscription:
az vm list -o json | jq '.[] | {VMName: .name, ResourceID: .id}'
```

## Check AHB status of a VM
You may view the AHB status of a VM in two ways:  using Azure CLI, or using the Azure Instance Metadata Service (Azure IMDS).


### Azure CLI

The `az vm get-instance-view` command may be used for this purpose. Look for a licenseType field in the response. If the licenseType field exists and the value is 'RHEL_BYOS' or 'SLES_BYOS', then your VM has the benefit enabled.

```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVm
```

### Azure Instance Metadata Service

From within the VM itself, you may query the IMDS Attested Metadata to determine the VM's licenseType. A licenseType value of 'RHEL_BYOS' or 'SLES_BYOS' will indicate that your VM has the benefit enabled. Learn more about attested metadata [here](./instance-metadata-service.md#attested-data)

## Compliance

### Red Hat

Customers using the Azure Hybrid Benefit for RHEL agree to the standard [legal terms](http://www.redhat.com/licenses/cloud_CSSA/Red_Hat_Cloud_Software_Subscription_Agreement_for_Microsoft_Azure.pdf) and [privacy statement](http://www.redhat.com/licenses/cloud_CSSA/Red_Hat_Privacy_Statement_for_Microsoft_Azure.pdf) associated with the Azure Marketplace RHEL offerings.

Customers using Azure Hybrid Benefit for RHEL have three options for providing software updates and patches to those VMs:

1.	[Red Hat Update Infrastructure (RHUI)](../workloads/redhat/redhat-rhui.md) (default option)
1.	Red Hat Satellite Server
1.	Red Hat Subscription Manager

Customers choosing the RHUI option, can continue to use RHUI as the main update source for their AHB RHEL VMs without attaching RHEL subscriptions to those VMs.  Customers choosing the RHUI option are responsible for ensuring RHEL subscription compliance.

Customers choosing either Red Hat Satellite Server or Red Hat Subscription Manager should remove RHUI configuration and then attach a Cloud Access enabled RHEL subscription to their AHB RHEL VM(s).  

More information about Red Hat subscription compliance, software updates, and sources for AHB RHEL VMs can be found [here](https://access.redhat.com/articles/5419341).

### SUSE

In order to use Azure Hybrid Benefit for your SLES VMs, you must first be registered with the SUSE Public Cloud program. Learn more about the program here. Once you have purchased SUSE subscriptions, you must register your VMs using those subscriptions to your own source of updates using either SUSE Customer Center, the Subscription Management Tool server, or SUSE Manager.

## Frequently asked questions
*Q: Can I use a license type of "RHEL_BYOS" with a SLES image, or vice-versa?*

A: No you cannot. Attempting to enter a license type that incorrectly matches the distro running on your VM will not update any billing metadata. However, if you accidentally enter the wrong license type, updating your VM again to the correct license type will still enable the benefit.

*Q: I have registered with Red Hat Cloud Access but am still unable to enable the benefit on my RHEL VMs. What do I do?*

A: It may take some time for your Red Hat Cloud Access subscription registration to propagate from Red Hat to Azure. If you are still seeing the error after one business day, contact Microsoft support.

*Q: I have deployed a VM using a RHEL BYOS “Golden Image”. Can I convert the billing on these images from BYOS to PAYG?*

A: No you cannot. Azure Hybrid Benefit supports conversion only on Pay-As-You-Go images.

*Q: I have deployed a VM using a RHEL BYOS “Golden Image”. Can I convert the billing on these images from BYOS to PAYG?*

A: No you cannot. Azure Hybrid Benefit supports conversion only on Pay-As-You-Go images.

*Q: I have uploaded my own RHEL image from on-prem (via Azure Migrate, ASR or otherwise) to Azure. Can I convert the billing on these images from BYOS to PAYG?*

A: No you cannot. The Azure Hybrid Benefit capability is available only to RHEL and SLES Marketplace images today. 

*Q: I have uploaded my own RHEL image from on-prem (via Azure Migrate, ASR or otherwise) to Azure. Do I need to do anything to benefit from Azure Hybrid Benefit?*

A: No you do not. RHEL images you upload are already considered BYOS, and you are only charged for Azure infrastructure costs. You are responsible for RHEL subscriptions costs just like you do for your on-premises environments. 

*Q: Can I use Azure Hybrid Benefit on VMs deployed from Marketplace RHEL and SLES SAP images?*

A: Yes, you can. You can use the license type of 'RHEL_BYOS' for RHEL VMs and 'SLES_BYOS' for conversions of VMs deployed from Marketplace RHEL and SLES SAP images.

*Q: Can I use Azure Hybrid Benefit on Virtual Machine Scale Set(VMSS) for RHEL and SLES?*

A: No, you cannot. VMSS are not in scope of Azure Hybrid Benefit today for RHEL and SLES currently.

*Q: Can I use Azure Hybrid Benefit on Reserved Instances(RIs) for RHEL and SLES?*

A: No, you cannot. RIs are not in scope of Azure Hybrid Benefit today for RHEL and SLES currently.

*Q: Can I use Azure Hybrid Benefit on a Virtual Machine deployed for SQL Server on RHEL images?*

A: No, you cannot. There is no plan for supporting these.
 

## Common issues
This section contains a list of common issues that could be encountered and steps for mitigation.

| Error | Mitigation |
| ----- | ---------- |
| "The action could not be completed because our records show that you have not successfully enabled Red Hat Cloud Access on your Azure subscription…." | In order to use the benefit with RHEL VMs, you must first register your Azure subscription(s) with Red Hat Cloud Access. Visit this link to learn more about how to register your Azure subscriptions for Red Hat Cloud Access

## Next steps
* Learn on how to create and update VMs and add license types (RHEL_BYOS, SLES_BYOS) for Azure Hybrid Benefit using [Azure CLI here.](/cli/azure/vm?preserve-view=true&view=azure-cli-latest)