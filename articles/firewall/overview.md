---
title: What is Azure Firewall?
description: Learn about Azure Firewall features.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: overview
ms.custom: mvc
ms.date: 6/26/2019
ms.author: victorh
Customer intent: As an administrator, I want to evaluate Azure Firewall so I can determine if I want to use it.
---

# What is Azure Firewall?

Azure Firewall is a managed, cloud-based network security service that protects your Azure Virtual Network resources. It's a fully stateful firewall as a service with built-in high availability and unrestricted cloud scalability.

![Firewall overview](media/overview/firewall-threat.png)

You can centrally create, enforce, and log application and network connectivity policies across subscriptions and virtual networks. Azure Firewall uses a static public IP address for your virtual network resources allowing outside firewalls to identify traffic originating from your virtual network.  The service is fully integrated with Azure Monitor for logging and analytics.

Azure Firewall offers the following features:

## Built-in high availability

High availability is built in, so no additional load balancers are required and there's nothing you need to configure.

## Availability Zones (public preview)

Azure Firewall can be configured during deployment to span multiple Availability Zones for increased availability. With Availability Zones, your availability increases to 99.99% uptime. For more information, see the Azure Firewall [Service Level Agreement (SLA)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/). The 99.99% uptime SLA is offered when two or more Availability Zones are selected.

You can also associate Azure Firewall to a specific zone just for proximity reasons, using the service standard 99.95% SLA.

There's no additional cost for a firewall deployed in an Availability Zone. However, there are additional costs for inbound and outbound data transfers associated with Availability Zones. For more information, see [Bandwidth pricing details](https://azure.microsoft.com/pricing/details/bandwidth/).

Azure Firewall Availability Zones are available in regions that support Availability Zones. For more information, see [What are Availability Zones in Azure?](../availability-zones/az-overview.md#services-support-by-region)

> [!NOTE]
> Availability Zones can only be configured during deployment. You can't configure an existing firewall to include Availability Zones.

For more information about Availability Zones, see [What are Availability Zones in Azure?](../availability-zones/az-overview.md)

## Unrestricted cloud scalability

Azure Firewall can scale up as much as you need  to accommodate changing network traffic flows, so you don't need to budget for your peak traffic.

## Application FQDN filtering rules

You can limit outbound HTTP/S traffic to a specified list of fully qualified domain names (FQDN) including wild cards. This feature doesn't require SSL termination.

## Network traffic filtering rules

You can centrally create *allow* or *deny* network filtering rules by source and destination IP address, port, and protocol. Azure Firewall is fully stateful, so it can distinguish legitimate packets for different types of connections. Rules are enforced and logged across multiple subscriptions and virtual networks.

## FQDN tags

FQDN tags make it easy for you to allow well known Azure service network traffic through your firewall. For example, say you want to allow Windows Update network traffic through your firewall. You create an application rule and include the Windows Update tag. Now network traffic from Windows Update can flow through your firewall.

## Service tags

A service tag represents a group of IP address prefixes to help minimize complexity for security rule creation. You can't create your own service tag, nor specify which IP addresses are included within a tag. Microsoft manages the address prefixes encompassed by the service tag, and automatically updates the service tag as addresses change.

## Threat intelligence

Threat intelligence-based filtering can be enabled for your firewall to alert and deny traffic from/to known malicious IP addresses and domains. The IP addresses and domains are sourced from the Microsoft Threat Intelligence feed.

## Outbound SNAT support

All outbound virtual network traffic IP addresses are translated to the Azure Firewall public IP (Source Network Address Translation). You can identify and allow traffic originating from your virtual network to remote Internet destinations. Azure Firewall doesn’t SNAT when the destination IP is a private IP range per [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). If your organization uses a public IP address range for private networks, Azure Firewall will SNAT the traffic to one of the firewall private IP addresses in AzureFirewallSubnet.

## Inbound DNAT support

Inbound network traffic to your firewall public IP address is translated (Destination Network Address Translation) and filtered to the private IP addresses on your virtual networks.

## Multiple public IPs (public preview)

You can associate multiple public IP addresses (up to 100) with your firewall.

This enables the following scenarios:

- **DNAT** - You can translate multiple standard port instances to your backend servers. For example, if you have two public IP addresses, you can translate TCP port 3389 (RDP) for both IP addresses.
- **SNAT** - Additional ports are available for outbound SNAT connections, reducing the potential for SNAT port exhaustion. At this time, Azure Firewall randomly selects the source public IP address to use for a connection. If you have any downstream filtering on your network, you need to allow all public IP addresses associated with your firewall.

> [!NOTE]
> During the public preview, if you add or remove a public IP address to a running firewall, existing inbound connectivity using DNAT rules may not function for 40 - 120 seconds. You can't remove the first public IP address assigned to the firewall unless the firewall is deallocated or deleted.

## Azure Monitor logging

All events are integrated with Azure Monitor, allowing you to archive logs to a storage account, stream events to your Event Hub, or send them to Azure Monitor logs.

## Known issues

Azure Firewall has the following known issues:

|Issue  |Description  |Mitigation  |
|---------|---------|---------|
|Conflict with Azure Security Center (ASC) Just-in-Time (JIT) feature|If a virtual machine is accessed using JIT, and is in a subnet with a user-defined route that points to Azure Firewall as a default gateway, ASC JIT doesn’t work. This is a result of asymmetric routing – a packet comes in via the virtual machine public IP (JIT opened the access), but the return path is via the firewall, which drops the packet because there's no established session on the firewall.|To work around this issue, place the JIT virtual machines on a separate subnet that doesn’t have a user-defined route to the firewall.|
Network filtering rules for non-TCP/UDP protocols (for example ICMP) don't work for Internet bound traffic|Network filtering rules for non-TCP/UDP protocols don’t work with SNAT to your public IP address. Non-TCP/UDP protocols are supported between spoke subnets and VNets.|Azure Firewall uses the Standard Load Balancer, [which doesn't support SNAT for IP protocols today](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#limitations). We're exploring options to support this scenario in a future release.|
|Missing PowerShell and CLI support for ICMP|Azure PowerShell and CLI don’t support ICMP as a valid protocol in network rules.|It's still possible to use ICMP as a protocol via the portal and the REST API. We're working to add ICMP in PowerShell and CLI soon.|
|FQDN tags require a protocol: port to be set|Application rules with FQDN tags require port: protocol definition.|You can use **https** as the port: protocol value. We're working to make this field optional when FQDN tags are used.|
|Moving a firewall to a different resource group or subscription isn't supported|Moving a firewall to a different resource group or subscription isn't supported.|Supporting this functionality is on our road map. To move a firewall to a different resource group or subscription, you must delete the current instance and recreate it in the new resource group or subscription.|
|Port range in network and application rules|Ports are limited to 64,000 as high ports are reserved for management and health probes. |We're working to relax this limitation.|
|Threat intelligence alerts may get masked|Network rules with destination 80/443 for outbound filtering masks threat intelligence alerts when configured to alert only mode.|Create outbound filtering for 80/443 using application rules. Or, change the threat intelligence mode to **Alert and Deny**.|
|Azure Firewall uses Azure DNS only for name resolution|Azure Firewall resolves FQDNs using Azure DNS only. A custom DNS server isn't supported. There's no impact on DNS resolution on other subnets.|We're working to relax this limitation.|
|Azure Firewall SNAT/DNAT doesn't work for private IP destinations|Azure Firewall SNAT/DNAT support is limited to Internet egress/ingress. SNAT/DNAT doesn't currently work for private IP destinations. For example, spoke to spoke.|This is a current limitation.|
|Can't remove first public IP address|You can't remove the first public IP address assigned to the firewall unless the firewall is deallocated or deleted.|This is by design.|
|If you add or remove a public IP address, the DNAT rules may not function temporarily.| If you add or remove a public IP address to a running firewall, existing inbound connectivity using DNAT rules may not function for 40 - 120 seconds.|This is a limitation of the public preview for this feature.|
|Availability zones can only be configured during deployment.|Availability zones can only be configured during deployment. You can't configure Availability Zones after a firewall has been deployed.|This is by design.|
|SNAT on inbound connections|In addition to DNAT, connections via the firewall public IP address (inbound) are SNATed to one of the firewall private IPs. This requirement today (also for Active/Active NVAs) to ensure symmetric routing.|To preserve the original source for HTTP/S, consider using [XFF](https://en.wikipedia.org/wiki/X-Forwarded-For) headers. For example, use a service such as [Azure Front Door](../frontdoor/front-door-http-headers-protocol.md#front-door-service-to-backend) in front of the firewall. You can also add WAF as part of Azure Front Door and chain to the firewall.

## Next steps

- [Tutorial: Deploy and configure Azure Firewall using the Azure portal](tutorial-firewall-deploy-portal.md)
- [Deploy Azure Firewall using a template](deploy-template.md)
- [Create an Azure Firewall test environment](scripts/sample-create-firewall-test.md)
