---
title: Classroom labs in Azure Lab Services - FAQ | Microsoft Docs
description: Find answers to common questions about classroom labs in Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''

ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: spelluru

---

# Classroom labs in Azure Lab Services - Frequently asked questions (FAQ)
Get answers to some of the most common questions about classroom labs in Azure Lab Services. 

## Quotas

### Is the quota per user or per week or per entire duration of the lab? 
The quota you set for a lab is for each student for entire duration of the lab. And, the [scheduled running time of VMs](how-to-create-schedules.md) doesn't count against the quota allotted to a user. The quota is for the time outside of schedule hours that a student spends on VMs.  For more information on quotas, see [Set quotas for users](how-to-configure-student-usage.md#set-quotas-for-users).

## Schedules

### Do all VMs in the lab start automatically when a schedule is set? 
No. Not all the VMs. Only the VMs that are assigned to users on a schedule. The VMs that aren't assigned to a user aren't automatically started. It's by design. 

## Lab accounts

### Why am I not able to create a lab because of unavailability of the address range? 
Classroom labs can create lab VMs within an IP address range you specify when creating your lab account in the Azure portal. When an address range is provided, each lab that's created after it's allotted 512 IP addresses for lab VMs. The address range for the lab account must be large enough to accommodate all the labs you intend to create under the lab account. 

For example, if you have a block of /19 - 10.0.0.0/19, this address range accommodates 8192 IP addresses and 16 labs(8192/512 = 16 labs). In this case, lab creation fails on 17th lab creation.

## Blog post
Subscribe to the [Azure Lab Services blog](https://azure.microsoft.com/blog/tag/azure-lab-services/).

## Update notifications
Subscribe to [Lab Services updates](https://azure.microsoft.com/updates/?product=lab-services) to stay informed about new features in DevTest Labs.

## General
### What if my question isn't answered here?
If your question isn't listed here, let us know, so we can help you find an answer.

- Post a question at the end of this FAQ. 
- To reach a wider audience, post a question on the [Azure DevTest Labs MSDN forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureDevTestLabs). 
- For feature requests, submit your requests and ideas to [Azure DevTest Labs User Voice](https://feedback.azure.com/forums/320373-azure-devtest-labs).

