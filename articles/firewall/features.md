---
title: Azure Firewall features
description: Learn about Azure Firewall features
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 10/08/2020
ms.author: victorh
---

# Azure Firewall features

[Azure Firewall](overview.md) is a managed, cloud-based network security service that protects your Azure Virtual Network resources..

![Firewall overview](media/overview/firewall-threat.png)

Azure Firewall includes the following features:

- [Built-in high availability](#built-in-high-availability)
- [Availability Zones](#availability-zones)
- [Unrestricted cloud scalability](#unrestricted-cloud-scalability)
- [Application FQDN filtering rules](#application-fqdn-filtering-rules)
- [Network traffic filtering rules](#network-traffic-filtering-rules)
- [FQDN tags](#fqdn-tags)
- [Service tags](#service-tags)
- [Threat intelligence](#threat-intelligence)
- [Outbound SNAT support](#outbound-snat-support)
- [Inbound DNAT support](#inbound-dnat-support)
- [Multiple public IP addresses](#multiple-public-ip-addresses)
- [Azure Monitor logging](#azure-monitor-logging)
- [Forced tunneling](#forced-tunneling)
- [Certifications](#certifications)

## Built-in high availability

High availability is built in, so no additional load balancers are required and there's nothing you need to configure.

## Availability Zones

Azure Firewall can be configured during deployment to span multiple Availability Zones for increased availability. With Availability Zones, your availability increases to 99.99% uptime. For more information, see the Azure Firewall [Service Level Agreement (SLA)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/). The 99.99% uptime SLA is offered when two or more Availability Zones are selected.

You can also associate Azure Firewall to a specific zone just for proximity reasons, using the service standard 99.95% SLA.

There's no additional cost for a firewall deployed in an Availability Zone. However, there are additional costs for inbound and outbound data transfers associated with Availability Zones. For more information, see [Bandwidth pricing details](https://azure.microsoft.com/pricing/details/bandwidth/).

Azure Firewall Availability Zones are available in regions that support Availability Zones. For more information, see [Regions that support Availability Zones in Azure](../availability-zones/az-region.md)

> [!NOTE]
> Availability Zones can only be configured during deployment. You can't configure an existing firewall to include Availability Zones.

For more information about Availability Zones, see [Regions and Availability Zones in Azure](../availability-zones/az-overview.md)

## Unrestricted cloud scalability

Azure Firewall can scale up as much as you need  to accommodate changing network traffic flows, so you don't need to budget for your peak traffic.

## Application FQDN filtering rules

You can limit outbound HTTP/S traffic or Azure SQL traffic to a specified list of fully qualified domain names (FQDN) including wild cards. This feature doesn't require TLS termination.

## Network traffic filtering rules

You can centrally create *allow* or *deny* network filtering rules by source and destination IP address, port, and protocol. Azure Firewall is fully stateful, so it can distinguish legitimate packets for different types of connections. Rules are enforced and logged across multiple subscriptions and virtual networks.

## FQDN tags

[FQDN tags](fqdn-tags.md) make it easy for you to allow well-known Azure service network traffic through your firewall. For example, say you want to allow Windows Update network traffic through your firewall. You create an application rule and include the Windows Update tag. Now network traffic from Windows Update can flow through your firewall.

## Service tags

A [service tag](service-tags.md) represents a group of IP address prefixes to help minimize complexity for security rule creation. You can't create your own service tag, nor specify which IP addresses are included within a tag. Microsoft manages the address prefixes encompassed by the service tag, and automatically updates the service tag as addresses change.

## Threat intelligence

[Threat intelligence](threat-intel.md)-based filtering can be enabled for your firewall to alert and deny traffic from/to known malicious IP addresses and domains. The IP addresses and domains are sourced from the Microsoft Threat Intelligence feed.

## Outbound SNAT support

All outbound virtual network traffic IP addresses are translated to the Azure Firewall public IP (Source Network Address Translation). You can identify and allow traffic originating from your virtual network to remote Internet destinations. Azure Firewall doesn't SNAT when the destination IP is a private IP range per [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). 

If your organization uses a public IP address range for private networks, Azure Firewall will SNAT the traffic to one of the firewall private IP addresses in AzureFirewallSubnet. You can configure Azure Firewall to **not** SNAT your public IP address range. For more information, see [Azure Firewall SNAT private IP address ranges](snat-private-range.md).

## Inbound DNAT support

Inbound Internet network traffic to your firewall public IP address is translated (Destination Network Address Translation) and filtered to the private IP addresses on your virtual networks.

## Multiple public IP addresses

You can associate [multiple public IP addresses](deploy-multi-public-ip-powershell.md) (up to 250) with your firewall.

This enables the following scenarios:

- **DNAT** - You can translate multiple standard port instances to your backend servers. For example, if you have two public IP addresses, you can translate TCP port 3389 (RDP) for both IP addresses.
- **SNAT** - Additional ports are available for outbound SNAT connections, reducing the potential for SNAT port exhaustion. At this time, Azure Firewall randomly selects the source public IP address to use for a connection. If you have any downstream filtering on your network, you need to allow all public IP addresses associated with your firewall. Consider using a [public IP address prefix](../virtual-network/public-ip-address-prefix.md) to simplify this configuration.

## Azure Monitor logging

All events are integrated with Azure Monitor, allowing you to archive logs to a storage account, stream events to your Event Hub, or send them to Azure Monitor logs. For Azure Monitor log samples, see [Azure Monitor logs for Azure Firewall](log-analytics-samples.md).

For more information, see [Tutorial: Monitor Azure Firewall logs and metrics](tutorial-diagnostics.md). 

Azure Firewall Workbook provides a flexible canvas for Azure Firewall data analysis. You can use it to create rich visual reports within the Azure portal. For more information, see [Monitor logs using Azure Firewall Workbook](firewall-workbook.md).

## Forced tunneling

You can configure Azure Firewall to route all Internet-bound traffic to a designated next hop instead of going directly to the Internet. For example, you may have an on-premises edge firewall or other network virtual appliance (NVA) to process network traffic before it's passed to the Internet. For more information, see [Azure Firewall forced tunneling](forced-tunneling.md).

## Certifications

Azure Firewall is Payment Card Industry (PCI), Service Organization Controls (SOC), International Organization for Standardization (ISO), and ICSA Labs compliant. For more information, see [Azure Firewall compliance certifications](compliance-certifications.md).

## Next steps

- [Azure Firewall rule processing logic](rule-processing.md)