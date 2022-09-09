---
title: Frequently asked questions (FAQ) about Azure Network Watcher | Microsoft Docs
description: This article answers frequently asked questions about the Azure Network Watcher service.
services: network-watcher
documentationcenter: na
author: damendo
manager:
editor:
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload:  infrastructure-services
ms.date: 10/10/2019
ms.author: damendo


---

# Frequently asked questions (FAQ) about Azure Network Watcher
The [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) service provides a suite of tools to monitor, diagnose, view metrics, and enable or disable logs for resources in an Azure virtual network. This article answers common questions about the service.

## General

### What is Network Watcher?
Network Watcher is designed to monitor and repair the network health of IaaS (Infrastructure-as-a-Service) components, which includes Virtual Machines, Virtual Networks, Application Gateways, Load balancers, and other resources in an Azure virtual network. It is not a solution for monitoring PaaS (Platform-as-a-Service) infrastructure or getting web/mobile analytics.

### What tools does Network Watcher provide?
Network Watcher provides three major sets of capabilities
* Monitoring
  * [Topology view](https://docs.microsoft.com/azure/network-watcher/view-network-topology) shows you the resources in your virtual network and the relationships between them.
  * [Connection Monitor](https://docs.microsoft.com/azure/network-watcher/connection-monitor) allows you to monitor connectivity and latency between a VM and another network resource.
  * [Network performance monitor](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor) allows you to monitor connectivity and latencies across hybrid network architectures, Expressroute circuits, and service/application endpoints.  
* Diagnostics
  * [IP Flow Verify](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) allows you to detect traffic filtering issues at a VM level.
  * [Next Hop](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) helps you verify traffic routes and detect routing issues.
  * [Connection Troubleshoot](https://docs.microsoft.com/azure/network-watcher/network-watcher-connectivity-portal) enables a one-time connectivity and latency check between a VM and another network resource.
  * [Packet Capture](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview) enables you to capture all traffic on a VM in your virtual network.
  * [VPN Troubleshoot](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-overview) runs multiple diagnostics checks on your VPN gateways and connections to help debug issues.
* Logging
  * [NSG Flow Logs](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) allows you to log all traffic in your [Network Security Groups (NSGs)](https://docs.microsoft.com/azure/virtual-network/security-overview)
  * [Traffic Analytics](https://docs.microsoft.com/azure/network-watcher/traffic-analytics) processes your NSG Flow Log data enabling you to visualize, query, analyze, and understand your network traffic.


For more detailed information, see the [Network Watcher overview page](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview).


### How does Network Watcher pricing work?
Visit the [Pricing page](https://azure.microsoft.com/pricing/details/network-watcher/) for Network Watcher components and their pricing.

### Which regions is Network Watcher supported/available in?
You can view the latest regional availability on the [Azure Service availability page](https://azure.microsoft.com/global-infrastructure/services/?products=network-watcher)

### Which permissions are needed to use Network Watcher?
See the list of [Azure RBAC permissions required to use Network Watcher](https://docs.microsoft.com/azure/network-watcher/required-rbac-permissions). For deploying resources, you need contributor permissions to the NetworkWatcherRG (see below).

### How do I enable Network Watcher?
The Network Watcher service is [enabled automatically](https://azure.microsoft.com/updates/azure-network-watcher-will-be-enabled-by-default-for-subscriptions-containing-virtual-networks/) for every subscription.

### What is the Network Watcher deployment model?
The Network Watcher parent resource is deployed with a unique instance in every region. Naming format: NetworkWatcher_RegionName. Example: NetworkWatcher_centralus is the Network Watcher resource for the "Central US" region.

### What is the NetworkWatcherRG?
Network Watcher resources are located in the hidden **NetworkWatcherRG** resource group which is created automatically. For example, the NSG Flow Logs resource is a child resource of Network Watcher and is enabled in the NetworkWatcherRG.

### Why do I need to install the Network Watcher extension? 
The Network Watcher extension is required for any feature that needs to generate or intercept traffic from a VM. 

### Which features require the Network Watcher extension?
The Packet Capture, Connection Troubleshoot and Connection Monitor features need the Network Watcher extension to be present.

### What are resource limits on Network Watcher?
See the [Service limits](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#network-watcher-limits) page for all limits.  

### Why is only one instance of Network Watcher allowed per region? 
Network Watcher just needs to be enabled once for a subscription for it's features to work, this is a not a service limit.

### How can I manage the Network Watcher Resource? 
The Network Watcher resource represents the backend service for Network Watcher and is fully managed by Azure. Customers do no need to manage it. Operations like move are not supported on the resource. However, [the resource can be deleted](https://docs.microsoft.com/azure/network-watcher/network-watcher-create#delete-a-network-watcher-in-the-portal). 

## Service availability and redundancy 

### Is the Network Watcher service zone resilient? 
Yes. The the Network Watcher service is zone-resilient by default. 

### How do I configure the Network Watcher service to be zone-resilient? 
No customer configuration is necessary to enable zone-resiliency. Zone-resiliency for Network Watcher resources is available by default and managed by the service itself. 

## NSG Flow Logs

### What does NSG Flow Logs do?
Azure network resources can be combined and managed through [Network Security Groups (NSGs)](https://docs.microsoft.com/azure/virtual-network/security-overview). NSG Flow Logs enable you to log 5-tuple flow information about all traffic through your NSGs. The raw flow logs are written to an Azure Storage account from where they can be further processed, analyzed, queried, or exported as needed.

### How do I use NSG Flow Logs with a Storage account behind a firewall?

To use a Storage account behind a firewall, you have to provide an exception for Trusted Microsoft Services to access your storage account:

* Navigate to the storage account by typing the storage account's name in the global search on the portal or from the [Storage Accounts page](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Storage%2FStorageAccounts)
* Under the **SETTINGS** section, select **Firewalls and virtual networks**
* In "Allow access from", select **Selected networks**. Then under **Exceptions**, tick the box next to **"Allow trusted Microsoft services to access this storage account"** 
* If it is already selected, no change is needed.  
* Locate your target NSG on the [NSG Flow Logs overview page](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) and enable NSG Flow Logs with the above storage account selected.

You can check the storage logs after a few minutes, you should see an updated TimeStamp or a new JSON file created.

### How do I use NSG Flow Logs with a Storage account behind a Service Endpoint?

NSG Flow Logs are compatible with Service Endpoints without requiring any extra configuration. Please see the [tutorial on enabling Service Endpoints](https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources#enable-a-service-endpoint) in your virtual network.


### What is the difference between flow logs versions 1 & 2?
Flow Logs version 2 introduces the concept of *Flow State* & stores information about bytes and packets transmitted. [Read more](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview#log-file).

## Next Steps
 - Head over to our [documentation overview page](https://docs.microsoft.com/azure/network-watcher/) for some tutorials to get you started with Network Watcher.
