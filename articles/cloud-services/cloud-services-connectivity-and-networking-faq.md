---
title: Connectivity and networking issues
titleSuffix: Azure Cloud Services
description: This article lists the frequently asked questions about connectivity and networking for Microsoft Azure Cloud Services.
services: cloud-services
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/23/2018
ms.author: genli
---
# Connectivity and networking issues for Azure Cloud Services: Frequently asked questions (FAQs)

This article includes frequently asked questions about connectivity and networking issues for [Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). For size information, see the [Cloud Services VM size page](cloud-services-sizes-specs.md).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## I can't reserve an IP in a multi-VIP cloud service.
First, make sure that the virtual machine instance that you try to reserve the IP for is turned on. Second, make sure that you use reserved IPs for both the staging and production deployments. *Do not* change the settings while the deployment is upgrading.

## How do I use Remote Desktop when I have an NSG?
Add rules to the NSG that allow traffic on ports **3389** and **20000**. Remote Desktop uses port **3389**. Cloud service instances are load balanced, so you can't directly control which instance to connect to. The *RemoteForwarder* and *RemoteAccess* agents manage Remote Desktop Protocol (RDP) traffic and allow the client to send an RDP cookie and specify an individual instance to connect to. The *RemoteForwarder* and *RemoteAccess* agents require port **20000** to be open, which might be blocked if you have an NSG.

## Can I ping a cloud service?

No, not by using the normal "ping"/ICMP protocol. The ICMP protocol is not permitted through the Azure load balancer.

To test connectivity, we recommend that you do a port ping. While Ping.exe uses ICMP, you can use other tools, such as PSPing, Nmap, and telnet, to test connectivity to a specific TCP port.

For more information, see [Use port pings instead of ICMP to test Azure VM connectivity](https://blogs.msdn.microsoft.com/mast/2014/06/22/use-port-pings-instead-of-icmp-to-test-azure-vm-connectivity/).

## How do I prevent receiving thousands of hits from unknown IP addresses that might indicate a malicious attack to the cloud service?
Azure implements a multilayer network security to protect its platform services against distributed denial-of-service (DDoS) attacks. The Azure DDoS defense system is part of Azure's continuous monitoring process, which is continually improved through penetration testing. This DDoS defense system is designed to withstand not only attacks from the outside but also from other Azure tenants. For more information, see [Azure network security](https://download.microsoft.com/download/C/A/3/CA3FC5C0-ECE0-4F87-BF4B-D74064A00846/AzureNetworkSecurity_v3_Feb2015.pdf).

You also can create a startup task to selectively block some specific IP addresses. For more information, see [Block a specific IP address](cloud-services-startup-tasks-common.md#block-a-specific-ip-address).

## When I try to RDP to my cloud service instance, I get the message "The user account has expired."
You might get the error message "This user account has expired" when you bypass the expiration date that is configured in your RDP settings. You can change the expiration date from the portal by following these steps:

1. Sign in to the [Azure portal](https://portal.azure.com), go to your cloud service, and select the **Remote Desktop** tab.

2. Select the **Production** or **Staging** deployment slot.

3. Change the **Expires On** date, and then save the configuration.

You now should be able to RDP to your machine.

## Why is Azure Load Balancer not balancing traffic equally?
For information about how an internal load balancer works, see [Azure Load Balancer new distribution mode](https://azure.microsoft.com/blog/azure-load-balancer-new-distribution-mode/).

The distribution algorithm used is a 5-tuple (source IP, source port, destination IP, destination port, and protocol type) hash to map traffic to available servers. It provides stickiness only within a transport session. Packets in the same TCP or UDP session are directed to the same datacenter IP (DIP) instance behind the load-balanced endpoint. When the client closes and reopens the connection or starts a new session from the same source IP, the source port changes and causes the traffic to go to a different DIP endpoint.

## How can I redirect incoming traffic to the default URL of my cloud service to a custom URL?

The URL Rewrite module of IIS can be used to redirect traffic that comes to the default URL for the cloud service (for example, \*.cloudapp.net) to some custom name/URL. Because the URL Rewrite module is enabled on web roles by default and its rules are configured in the application's web.config, it's always available on the VM regardless of reboots/reimages.For more information, see:

- [Create rewrite rules for the URL Rewrite module](https://docs.microsoft.com/iis/extensions/url-rewrite-module/creating-rewrite-rules-for-the-url-rewrite-module)
- [Remove a default link](https://stackoverflow.com/questions/32286487/azure-website-how-to-remove-default-link?answertab=votes#tab-top)

## How can I block/disable incoming traffic to the default URL of my cloud service?

You can prevent incoming traffic to the default URL/name of your cloud service (for example, \*.cloudapp.net). Set the host header to a custom DNS name (for example, www\.MyCloudService.com) under site binding configuration in the cloud service definition (*.csdef) file, as indicated:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="AzureCloudServicesDemo" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
    <WebRole name="MyWebRole" vmsize="Small">
        <Sites>
            <Site name="Web">
            <Bindings>
                <Binding name="Endpoint1" endpointName="Endpoint1" hostHeader="www.MyCloudService.com" />
            </Bindings>
            </Site>
        </Sites>
        <Endpoints>
            <InputEndpoint name="Endpoint1" protocol="http" port="80" />
        </Endpoints>
        <ConfigurationSettings>
            <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
        </ConfigurationSettings>
    </WebRole>
</ServiceDefinition>
```

Because this host header binding is enforced through the csdef file, the service is accessible only via the custom name "www.MyCloudService.com." All incoming requests to the "*.cloudapp.net" domain always fail. If you use a custom SLB probe or an internal load balancer in the service, blocking the default URL/name of the service might interfere with the probing behavior.

## How can I make sure the public-facing IP address of a cloud service never changes?

To make sure the public-facing IP address of your cloud service (also known as a VIP) never changes so that it can be customarily whitelisted by a few specific clients, we recommend that you have a reserved IP associated with it. Otherwise, the virtual IP provided by Azure is deallocated from your subscription if you delete the deployment. For successful VIP swap operation, you need individual reserved IPs for both production and staging slots. Without them, the swap operation fails. To reserve an IP address and associate it with your cloud service, see these articles:

- [Reserve the IP address of an existing cloud service](../virtual-network/virtual-networks-reserved-public-ip.md#reserve-the-ip-address-of-an-existing-cloud-service)
- [Associate a reserved IP to a cloud service by using a service configuration file](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

If you have more than one instance for your roles, associating RIP with your cloud service shouldn't cause any downtime. Alternatively, you can add the IP range of your Azure datacenter to an allow list. You can find all Azure IP ranges at the [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=41653).

This file contains the IP address ranges (including compute, SQL, and storage ranges) used in Azure datacenters. An updated file is posted weekly that reflects the currently deployed ranges and any upcoming changes to the IP ranges. New ranges that appear in the file aren't used in the datacenters for at least one week. Download the new .xml file every week, and perform the necessary changes on your site to correctly identify services running in Azure. Azure ExpressRoute users might note that this file used to update the BGP advertisement of Azure space in the first week of each month.

## How can I use Azure Resource Manager virtual networks with cloud services?

Cloud services can't be placed in Azure Resource Manager virtual networks. Resource Manager virtual networks and classic deployment virtual networks can be connected through peering. For more information, see [Virtual network peering](../virtual-network/virtual-network-peering-overview.md).


## How can I get the list of public IPs used by my Cloud Services?

You can use following PS script to get the list of public IPs for Cloud Services under your subscription

```powershell
$services = Get-AzureService  | Group-Object -Property ServiceName

foreach ($service in $services)
{
    "Cloud Service '$($service.Name)'"

    $deployment = Get-AzureDeployment -ServiceName $service.Name
    "VIP - " +  $deployment.VirtualIPs[0].Address
    "================================="
}
```
