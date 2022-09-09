---
title: Azure network security groups overview
titlesuffix: Azure Virtual Network
description: Learn about network security groups. Network security groups help you filter network traffic between Azure resources.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: kumud
ms.reviewer: kumud
ms.custom: contperfq1
---

# Network security groups
<a name="network-security-groups"></a>

You can use an Azure network security group to filter network traffic to and from Azure resources in an Azure virtual network. A network security group contains [security rules](#security-rules) that allow or deny inbound network traffic to, or outbound network traffic from, several types of Azure resources. For each rule, you can specify source and destination, port, and protocol.

This article describes properties of a network security group rule, the [default security rules](#default-security-rules) that are applied, and the rule properties that you can modify to create an [augmented security rule](#augmented-security-rules).

## <a name="security-rules"></a> Security rules

A network security group contains zero, or as many rules as desired, within Azure subscription [limits](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Each rule specifies the following properties:

|Property  |Explanation  |
|---------|---------|
|Name|A unique name within the network security group.|
|Priority | A number between 100 and 4096. Rules are processed in priority order, with lower numbers processed before higher numbers, because lower numbers have higher priority. Once traffic matches a rule, processing stops. As a result, any rules that exist with lower priorities (higher numbers) that have the same attributes as rules with higher priorities are not processed.|
|Source or destination| Any, or an individual IP address, classless inter-domain routing (CIDR) block (10.0.0.0/24, for example), service tag, or application security group. If you specify an address for an Azure resource, specify the private IP address assigned to the resource. Network security groups are processed after Azure translates a public IP address to a private IP address for inbound traffic, and before Azure translates a private IP address to a public IP address for outbound traffic. . Specifying a range, a service tag, or application security group, enables you to create fewer security rules. The ability to specify multiple individual IP addresses and ranges (you cannot specify multiple service tags or application groups) in a rule is referred to as [augmented security rules](#augmented-security-rules). Augmented security rules can only be created in network security groups created through the Resource Manager deployment model. You cannot specify multiple IP addresses and IP address ranges in network security groups created through the classic deployment model.|
|Protocol     | TCP, UDP, ICMP or Any.|
|Direction| Whether the rule applies to inbound, or outbound traffic.|
|Port range     |You can specify an individual or range of ports. For example, you could specify 80 or 10000-10005. Specifying ranges enables you to create fewer security rules. Augmented security rules can only be created in network security groups created through the Resource Manager deployment model. You cannot specify multiple ports or port ranges in the same security rule in network security groups created through the classic deployment model.   |
|Action     | Allow or deny        |

Network security group security rules are evaluated by priority using the 5-tuple information (source, source port, destination, destination port, and protocol) to allow or deny the traffic. You may not create two security rules with the same priority and direction. A flow record is created for existing connections. Communication is allowed or denied based on the connection state of the flow record. The flow record allows a network security group to be stateful. If you specify an outbound security rule to any address over port 80, for example, it's not necessary to specify an inbound security rule for the response to the outbound traffic. You only need to specify an inbound security rule if communication is initiated externally. The opposite is also true. If inbound traffic is allowed over a port, it's not necessary to specify an outbound security rule to respond to traffic over the port.

Existing connections may not be interrupted when you remove a security rule that enabled the flow. Traffic flows are interrupted when connections are stopped and no traffic is flowing in either direction, for at least a few minutes.

There are limits to the number of security rules you can create in a network security group. For details, see [Azure limits](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

### <a name="default-security-rules"></a> Default security rules

Azure creates the following default rules in each network security group that you create:

#### Inbound

##### AllowVNetInBound

|Priority|Source|Source ports|Destination|Destination ports|Protocol|Access|
|---|---|---|---|---|---|---|
|65000|VirtualNetwork|0-65535|VirtualNetwork|0-65535|Any|Allow|

##### AllowAzureLoadBalancerInBound

|Priority|Source|Source ports|Destination|Destination ports|Protocol|Access|
|---|---|---|---|---|---|---|
|65001|AzureLoadBalancer|0-65535|0.0.0.0/0|0-65535|Any|Allow|

##### DenyAllInbound

|Priority|Source|Source ports|Destination|Destination ports|Protocol|Access|
|---|---|---|---|---|---|---|
|65500|0.0.0.0/0|0-65535|0.0.0.0/0|0-65535|Any|Deny|

#### Outbound

##### AllowVnetOutBound

|Priority|Source|Source ports| Destination | Destination ports | Protocol | Access |
|---|---|---|---|---|---|---|
| 65000 | VirtualNetwork | 0-65535 | VirtualNetwork | 0-65535 | Any | Allow |

##### AllowInternetOutBound

|Priority|Source|Source ports| Destination | Destination ports | Protocol | Access |
|---|---|---|---|---|---|---|
| 65001 | 0.0.0.0/0 | 0-65535 | Internet | 0-65535 | Any | Allow |

##### DenyAllOutBound

|Priority|Source|Source ports| Destination | Destination ports | Protocol | Access |
|---|---|---|---|---|---|---|
| 65500 | 0.0.0.0/0 | 0-65535 | 0.0.0.0/0 | 0-65535 | Any | Deny |

In the **Source** and **Destination** columns, *VirtualNetwork*, *AzureLoadBalancer*, and *Internet* are [service tags](service-tags-overview.md), rather than IP addresses. In the protocol column, **Any** encompasses TCP, UDP, and ICMP. When creating a rule, you can specify TCP, UDP, ICMP or Any. *0.0.0.0/0* in the **Source** and **Destination** columns represents all addresses. Clients like Azure portal, Azure CLI, or PowerShell can use * or any for this expression.
 
You cannot remove the default rules, but you can override them by creating rules with higher priorities.

### <a name="augmented-security-rules"></a> Augmented security rules

Augmented security rules simplify security definition for virtual networks, allowing you to define larger and complex network security policies, with fewer rules. You can combine multiple ports and multiple explicit IP addresses and ranges into a single, easily understood security rule. Use augmented rules in the source, destination, and port fields of a rule. To simplify maintenance of your security rule definition, combine augmented security rules with [service tags](service-tags-overview.md) or [application security groups](#application-security-groups). There are limits to the number of addresses, ranges, and ports that you can specify in a rule. For details, see [Azure limits](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

#### Service tags

A service tag represents a group of IP address prefixes from a given Azure service. It helps to minimize the complexity of frequent updates on network security rules.

For more information, see [Azure service tags](service-tags-overview.md). For an example on how to use the Storage service tag to restrict network access, see [Restrict network access to PaaS resources](tutorial-restrict-network-access-to-resources.md).

#### Application security groups

Application security groups enable you to configure network security as a natural extension of an application's structure, allowing you to group virtual machines and define network security policies based on those groups. You can reuse your security policy at scale without manual maintenance of explicit IP addresses. To learn more, see [Application security groups](application-security-groups.md).

## Azure platform considerations

- **Virtual IP of the host node**: Basic infrastructure services like DHCP, DNS, IMDS, and health monitoring are provided through the virtualized host IP addresses 168.63.129.16 and 169.254.169.254. These IP addresses belong to Microsoft and are the only virtualized IP addresses used in all regions for this purpose. Effective security rules and effective routes will not include these platform rules. To override this basic infrastructure communication, you can create a security rule to deny traffic by using the following [service tags](service-tags-overview.md) on your Network Security Group rules: AzurePlatformDNS, AzurePlatformIMDS, AzurePlatformLKM. Learn how to [diagnose network traffic filtering](diagnose-network-traffic-filter-problem.md) and [diagnose network routing](diagnose-network-routing-problem.md).
- **Licensing (Key Management Service)**: Windows images running in virtual machines must be licensed. To ensure licensing, a request is sent to the Key Management Service host servers that handle such queries. The request is made outbound through port 1688. For deployments using [default route 0.0.0.0/0](virtual-networks-udr-overview.md#default-route) configuration, this platform rule will be disabled.
- **Virtual machines in load-balanced pools**: The source port and address range applied are from the originating computer, not the load balancer. The destination port and address range are for the destination computer, not the load balancer.
- **Azure service instances**: Instances of several Azure services, such as HDInsight, Application Service Environments, and Virtual Machine Scale Sets are deployed in virtual network subnets. For a complete list of services you can deploy into virtual networks, see [Virtual network for Azure services](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network). Ensure you familiarize yourself with the port requirements for each service before applying a network security group to the subnet the resource is deployed in. If you deny ports required by the service, the service doesn't function properly.
- **Sending outbound email**: Microsoft recommends that you utilize authenticated SMTP relay services (typically connected via TCP port 587, but often others, as well) to send email from Azure Virtual Machines. SMTP relay services specialize in sender reputation, to minimize the possibility that third-party email providers reject messages. Such SMTP relay services include, but are not limited to, Exchange Online Protection and SendGrid. Use of SMTP relay services is in no way restricted in Azure, regardless of your subscription type. 

  If you created your Azure subscription prior to November 15, 2017, in addition to being able to use SMTP relay services, you can send email directly over TCP port 25. If you created your subscription after November 15, 2017, you may not be able to send email directly over port 25. The behavior of outbound communication over port 25 depends on the type of subscription you have, as follows:

     - **Enterprise Agreement**: Outbound port 25 communication is allowed. You are able to send an outbound email directly from virtual machines to external email providers, with no restrictions from the Azure platform. 
     - **Pay-as-you-go:** Outbound port 25 communication is blocked from all resources. If you need to send email from a virtual machine directly to external email providers (not using an authenticated SMTP relay), you can make a request to remove the restriction. Requests are reviewed and approved at Microsoft's discretion and are only granted after anti-fraud checks are performed. To make a request, open a support case with the issue type *Technical*, *Virtual Network Connectivity*, *Cannot send e-mail (SMTP/Port 25)*. In your support case, include details about why your subscription needs to send email directly to mail providers, instead of going through an authenticated SMTP relay. If your subscription is exempted, only virtual machines created after the exemption date are able to communicate outbound over port 25.
     - **MSDN, Azure Pass, Azure in Open, Education, BizSpark, and Free trial**: Outbound port 25 communication is blocked from all resources. No requests to remove the restriction can be made, because requests are not granted. If you need to send email from your virtual machine, you have to use an SMTP relay service.
     - **Cloud service provider**: Customers that are consuming Azure resources via a cloud service provider can create a support case with their cloud service provider, and request that the provider create an unblock case on their behalf, if a secure SMTP relay cannot be used.

  If Azure allows you to send email over port 25, Microsoft cannot guarantee email providers will accept inbound email from your virtual machine. If a specific provider rejects mail from your virtual machine, work directly with the provider to resolve any message delivery or spam filtering issues, or use an authenticated SMTP relay service.

## Next steps

* To learn about which Azure resources can be deployed into a virtual network and have network security groups associated to them, see [Virtual network integration for Azure services](virtual-network-for-azure-services.md)
* To learn how traffic is evaluated with network security groups, see [How network security groups work](network-security-group-how-it-works.md).
* If you've never created a network security group, you can complete a quick [tutorial](tutorial-filter-network-traffic.md) to get some experience creating one.
* If you're familiar with network security groups and need to manage them, see [Manage a network security group](manage-network-security-group.md). 
* If you're having communication problems and need to troubleshoot network security groups, see [Diagnose a virtual machine network traffic filter problem](diagnose-network-traffic-filter-problem.md). 
* Learn how to enable [network security group flow logs](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) to analyze network traffic to and from resources that have an associated network security group.
