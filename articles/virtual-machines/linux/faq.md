---
title: Frequently asked questions for Linux VMs in Azure 
description: Provides answers to some of the common questions about Linux virtual machines created with the Resource Manager model.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-management

ms.assetid: 3648e09c-1115-4818-93c6-688d7a54a353
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux

ms.topic: article
ms.date: 05/08/2019
ms.author: cynthn

---
# Frequently asked question about Linux Virtual Machines
This article addresses some common questions about Linux virtual machines created in Azure using the Resource Manager deployment model. For the Windows version of this topic, see [Frequently asked question about Windows Virtual Machines](../windows/faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## What can I run on an Azure VM?
All subscribers can run server software on an Azure virtual machine. For more information, see [Linux on Azure-Endorsed Distributions](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## How much storage can I use with a virtual machine?
Each data disk can be up to 32,767 GiB. The number of data disks you can use depends on the size of the virtual machine. For details, see [Sizes for Virtual Machines](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Azure Managed Disks are the recommended disk storage offerings for use with Azure Virtual Machines for persistent storage of data. You can use multiple Managed Disks with each Virtual Machine. Managed Disks offer two types of durable storage options: Premium and Standard Managed Disks. For pricing information, see [Managed Disks Pricing](https://azure.microsoft.com/pricing/details/managed-disks).

Azure storage accounts can also provide storage for the operating system disk and any data disks. Each disk is a .vhd file stored as a page blob. For pricing details, see [Storage Pricing Details](https://azure.microsoft.com/pricing/details/storage/).

## How can I access my virtual machine?
Establish a remote connection to sign on to the virtual machine, using Secure Shell (SSH). See the instructions on how to connect [from Windows](ssh-from-windows.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) or
[from Linux and Mac](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). By default, SSH allows a maximum of 10 concurrent connections. You can increase this number by editing the configuration file.

If you’re having problems, check out [Troubleshoot Secure Shell (SSH) connections](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## Can I use the temporary disk (/dev/sdb1) to store data?
Don't use the temporary disk (/dev/sdb1) to store data. It is only there for temporary storage. You risk losing data that can’t be recovered.

## Can I copy or clone an existing Azure VM?
Yes. For instructions, see [How to create a copy of a Linux virtual machine in the Resource Manager deployment model](copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## Why am I not seeing Canada Central and Canada East regions through Azure Resource Manager?
The two new regions of Canada Central and Canada East are not automatically registered for virtual machine creation for existing Azure subscriptions. This registration is done automatically when a virtual machine is deployed through the Azure portal to any other region using Azure Resource Manager. After a virtual machine is deployed to any other Azure region, the new regions should be available for subsequent virtual machines.

## Can I add a NIC to my VM after it's created?
Yes, this is now possible. The VM first needs to be stopped deallocated. Then you can add or remove a NIC (unless it's the last NIC on the VM). 

## Are there any computer name requirements?
Yes. The computer name can be a maximum of 64 characters in length. See [Naming conventions rules and restrictions](/azure/architecture/best-practices/resource-naming) for more information around naming your resources.

## Are there any resource group name requirements?
Yes. The resource group name can be a maximum of 90 characters in length. See [Naming conventions rules and restrictions](/azure/architecture/best-practices/resource-naming) for more information about resource groups.

## What are the username requirements when creating a VM?

Usernames should be 1 - 32 characters in length.

The following usernames are not allowed:

| | | | |
|-----------------|-----------|--------------------|----------|
| `administrator` | `admin`   | `user`             | `user1`  |
| `test`          | `user2`   | `test1`            | `user3`  |
| `admin1`        | `1`       | `123`              | `a`      |
| `actuser`       | `adm`     | `admin2`           | `aspnet` |
| `backup`        | `console` | `david`            | `guest`  |
| `john`          | `owner`   | `root`             | `server` |
| `sql`           | `support` | `support_388945a0` | `sys`    |
| `test2`         | `test3`   | `user4`            | `user5`  |
| `video`         |

## What are the password requirements when creating a VM?

There are varying password length requirements, depending on the tool you are using:
 - Portal - between 12 - 72 characters
 - PowerShell - between 8 - 123 characters
 - CLI - between 12 - 123
 

Passwords must also meet 3 out of the following 4 complexity requirements:

* Have lower characters
* Have upper characters
* Have a digit
* Have a special character (Regex match [\W_])

The following passwords are not allowed:

<table>
    <tr>
        <td style="text-align:center">abc@123</td>
        <td style="text-align:center">P@$$w0rd</td>
        <td style="text-align:center">P@ssw0rd</td>
        <td style="text-align:center">P@ssword123</td>
        <td style="text-align:center">Pa$$word</td>
    </tr>
    <tr>
        <td style="text-align:center">pass@word1</td>
        <td style="text-align:center">Password!</td>
        <td style="text-align:center">Password1</td>
        <td style="text-align:center">Password22</td>
        <td style="text-align:center">iloveyou!</td>
    </tr>
</table>
