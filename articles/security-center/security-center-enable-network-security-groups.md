---
title: Enable Network Security Groups in Azure Security Center | Microsoft Docs
description: This document shows you how to implement the Azure Security Center recommendation **Enable Network Security Groups**.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''

ms.assetid: f53ed853-ffaf-4530-a019-1906ba6f341b
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin

---
# Enable Network Security Groups in Azure Security Center
Azure Security Center recommends that you enable a network security group (NSG) if one is not already enabled. NSGs contain a list of Access Control List (ACL) rules that allow or deny network traffic to your VM instances in a Virtual Network. NSGs can be associated with either subnets or individual VM instances within that subnet. When an NSG is associated with a subnet, the ACL rules apply to all the VM instances in that subnet. In addition, traffic to an individual VM can be restricted further by associating an NSG directly to that VM. To learn more see [What is a Network Security Group (NSG)?](../virtual-network/security-overview.md)

If you do not have NSGs enabled, Security Center presents two recommendations to you: Enable Network Security Groups on subnets and Enable Network Security Groups on virtual machines. You choose which level, subnet or VM, to apply NSGs.

> [!NOTE]
> This document introduces the service by using an example deployment.  This is not a step-by-step guide.
>
>

## Implement the recommendation
1. In the **Recommendations** blade, select **Enable Network Security Groups** on subnets or on virtual machines.
   ![Enable Network Security Groups][1]
2. This opens the blade **Configure Missing Network Security Groups** for subnets or for virtual machines, depending on the recommendation that you selected. Select a subnet or a virtual machine to configure an NSG on.

   ![Configure NSG for subnet][2]

   ![Configure NSG for VM][3]
3. On the **Choose network security group** blade, select an existing NSG or select **Create new** to create an NSG.

   ![Choose Network Security Group][4]

If you create an NSG, follow the steps in [Manage a network security group](../virtual-network/manage-network-security-group.md) to create an NSG and set security rules.

## See also
This article showed you how to implement the Security Center recommendation "Enable Network Security Groups" for subnets or virtual machines. To learn more about enabling NSGs, see the following:

* [What is a Network Security Group (NSG)?](../virtual-network/security-overview.md)
* [Manage a network security group](../virtual-network/manage-network-security-group.md)

To learn more about Security Center, see the following:

* [Setting security policies in Azure Security Center](tutorial-security-policy.md) -- Learn how to configure security policies for your Azure subscriptions and resource groups.
* [Managing security recommendations in Azure Security Center](security-center-recommendations.md) -- Learn how recommendations help you protect your Azure resources.
* [Security health monitoring in Azure Security Center](security-center-monitoring.md) -- Learn how to monitor the health of your Azure resources.
* [Managing and responding to security alerts in Azure Security Center](security-center-managing-and-responding-alerts.md) -- Learn how to manage and respond to security alerts.
* [Monitoring partner solutions with Azure Security Center](security-center-partner-solutions.md) -- Learn how to monitor the health status of your partner solutions.
* [Azure Security Center FAQ](security-center-faq.md) -- Find frequently asked questions about using the service.
* [Azure Security blog](https://blogs.msdn.com/b/azuresecurity/) -- Get the latest Azure security news and information.

<!--Image references-->
[1]: ./media/security-center-enable-nsg/enable-nsg.png
[2]:./media/security-center-enable-nsg/configure-nsg-for-subnet.png
[3]: ./media/security-center-enable-nsg/configure-nsg-for-vm.png
[4]: ./media/security-center-enable-nsg/choose-nsg.png
