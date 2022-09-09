---
title: Azure Load Balancer portal settings
description: Get started learning about Azure Load Balancer portal settings
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/8/2020
ms.author: allensu
---

# Azure Load Balancer portal settings

As you create Azure Load Balancer, information in this article will help you learn more about the individual settings and what the right configuration is for you.

## Create load balancer

Azure Load Balancer is a network load balancer that distributes traffic across VM instances in the backend pool.

### Basics

In the **Basics** tab of the create load balancer portal page, you'll see the following information:



| Setting |  Details |
| ---------- | ---------- |
| Subscription  | Select your subscription. This selection is the subscription you want your load balancer to be deployed in. |
| Resource group | Select **Create new** and type in the name for your resource group in the text box. If you have an existing resource group created, select it. |
| Name | This setting is the name for your Azure Load Balancer. |
| Region | Select an Azure region you'd like to deploy your load balancer in. |
| Type | Load balancer has two types: </br> **Internal (Private)** </br> **Public (External)**.</br> An internal load balancer (ILB) routes traffic to backend pool members via a private IP address.</br> A public load balancer directs requests from clients over the internet to the backend pool.</br> Learn more about [load balancer types](components.md#frontend-ip-configuration-).|
| SKU  | Select **Standard**. </br> Load balancer has two SKUs: **Basic** and **Standard**. </br> Basic has limited functionality. </br> **Standard** is recommended for production workloads. </br> Learn more about [SKUs](skus.md). |

If you select **Public** as your type, you'll see the following information:

| Setting |  Details |
| ---------- | ---------- |
| Public IP address | Select **Create new** to create a public IP address for your public load balancer. </br> If you have an existing public IP, select **Use existing**.  |
| Public IP address name | The name of the public IP address.|
| Public IP address SKU | Public IP addresses have two SKUs: **Basic** and **Standard**. </br> Basic doesn't support zone-resiliency and zonal attributes. </br> **Standard** is recommended for production workloads. </br> Load balancer and public IP address SKUs **must match**. |
| Assignment | **Static** is auto selected for standard. </br> Basic public IPs have two types: **Dynamic** and **Static**. </br> Dynamic public IP addresses aren't assigned until creation. </br> IPs can be lost if the resource is deleted. </br> Static IP addresses are recommended. |
| Availability zone | Select **Zone-redundant** to create a resilient load balancer. </br> To create a zonal load balancer, select a specific zone from **1**, **2**, or **3**. </br> Standard load balancer and public IPs support zones. </br> Learn more about [load balancer and availability zones](load-balancer-standard-availability-zones.md). </br> You won't see zone selection for basic. Basic load balancer doesn't support zones. |
| Add a public IPv6 address | Load balancer supports **IPv6** addresses for your frontend. </br> Learn more about [load Balancer and IPv6](load-balancer-ipv6-overview.md)
| Routing preference | Select **Microsoft Network**. </br> Microsoft Network means that traffic is routed via the Microsoft global network. </br> Internet means that traffic is routed through the internet service provider network. </br> Learn more about [Routing Preferences](../virtual-network/routing-preference-overview.md)|

:::image type="content" source="./media/manage/create-public-load-balancer-basics.png" alt-text="Create load balancer public." border="true":::

If you select **Internal** in Type, you'll see the following information:

| Setting |  Details |
| ---------- | ---------- |
| Virtual network | The virtual network you want your internal load balancer to be part of. </br> The private frontend IP address you select for your internal load balancer will be from this virtual network. |
| IP address assignment | Your options are **Static** or **Dynamic**. </br> Static ensures the IP doesn't change. A dynamic IP could change. |
| Availability zone | Your options are: </br> **Zone redundant** </br> **Zone 1** </br> **Zone 2** </br> **Zone 3** </br> To create a load balancer that is highly available and resilient to availability zone failures, select a **zone-redundant** IP. |

:::image type="content" source="./media/manage/create-internal-load-balancer-basics.png" alt-text="Create load balancer internal." border="true":::

## Frontend IP configuration

The IP address of your Azure Load Balancer. It's the point of contact for clients. 

You can have one or many frontend IP configurations. If you went through the basics section above, you would have already created a frontend for your load balancer. 

If you want to add a frontend IP configuration to your load balancer, go to your load balancer in the Azure portal, select **Frontend IP configuration**, and then select **+Add**.

| Setting |  Details |
| ---------- | ---------- |
| Name | The name of your frontend IP configuration. |
| IP version | The IP address version you'd like your frontend to have. </br> Load balancer supports both IPv4 and IPv6 frontend IP configurations. |
| IP type | IP type determines if a single IP address is associated with your frontend or a range of IP addresses using an IP Prefix. </br> A [public IP prefix](../virtual-network/public-ip-address-prefix.md) assists when you need to connect to the same endpoint repeatedly. The prefix ensures enough ports are given to assist with SNAT port issues. |
| Public IP address (or Prefix if you selected prefix above) | Select or create a new public IP (or prefix) for your load balancer frontend. |

:::image type="content" source="./media/manage/frontend.png" alt-text="Create frontend ip configuration page." border="true":::

## Backend pools

A backend address pool contains the IP addresses of the virtual network interfaces in the backend pool. 

If you want to add a backend pool to your load balancer, go to your load balancer in the Azure portal, select **Backend pools**, and then select **+Add**.

| Setting | Details |
| ---------- |  ---------- |
| Name | The name of your backend pool. |
| Virtual network | The virtual network your backend instances are. |
| IP version | Your options are **IPv4** or **IPv6**. |

You can add virtual machines or virtual machine scale sets to the backend pool of your Azure Load Balancer. Create the virtual machines or virtual machine scale sets first. Next, add them to the load balancer in the portal.

:::image type="content" source="./media/manage/backend.png" alt-text="Create backend pool page." border="true":::

## Health probes

A health probe is used to monitor the status of your backend VMs or instances. The health probe status determines when new connections are sent to an instance based on health checks. 

If you want to add a health probe to your load balancer, go to your load balancer in the Azure portal, select **Health probes**, then select **+Add**.

| Setting | Details |
| ---------- | ---------- |
| Name | The name of your health probe. |
| Protocol | The protocol you select determines the type of check used to determine if the backend instance(s) are healthy. </br> Your options are: </br> **TCP** </br> **HTTPS** </br> **HTTP** </br> Ensure you're using the right protocol. This selection will depend on the nature of your application. </br> The configuration of the health probe and probe responses determines which backend pool instances will receive new flows. </br> You can use health probes to detect the failure of an application on a backend endpoint. </br> Learn more about [health probes](load-balancer-custom-probe-overview.md). |
| Port | The destination port for the health probe. </br> This setting is the port on the backend instance the health probe will use to determine the instance's health. |
| Interval | The number of seconds in between probe attempts. </br> The interval will determine how frequently the health probe will attempt to reach the backend instance. </br> If you select 5, the second probe attempt will be made after 5 seconds and so on. |
| Unhealthy threshold | The number of consecutive probe failures that must occur before a VM is considered unhealthy.</br> If you select 2, no new flows will be set to this backend instance after two consecutive failures. |

:::image type="content" source="./media/manage/health-probe.png" alt-text="Add health probe." border="true":::

## Load-balancing rules

Defines how incoming traffic is distributed to all the instances within the backend pool. A load-balancing rule maps a given frontend IP configuration and port to multiple backend IP addresses and ports.

If you want to add a load balancer rule to your load balancer, go to your load balancer in the Azure portal, select **Load-balancing rules**, and then select **+Add**.
    
| Setting | Details |
| ---------- | ---------- |
| Name | The name of the load balancer rule. |
| IP Version | Your options are **IPv4** or **IPv6**.  |
| Frontend IP address | Select the frontend IP address. </br> The frontend IP address of your load balancer you want the load balancer rule associated to.|
| Protocol | Azure Load Balancer is a layer 4 network load balancer. </br> Your options are: **TCP** or **UDP**. |
| Port | This setting is the port associated with the frontend IP that you want traffic to be distributed based on this load-balancing rule. |
| Backend port | This setting is the port on the instances in the backend pool you would like the load balancer to send traffic to. This setting can be the same as the frontend port or different if you need the flexibility for your application. |
| Backend pool | The backend pool you would like this load balancer rule to be applied on. |
| Health probe | The health probe you created to check the status of the instances in the backend pool. </br> Only healthy instances will receive new traffic. |
| Session persistence |  Your options are: </br> **None** </br> **Client IP** </br> **Client IP and protocol**</br> </br> Maintain traffic from a client to the same virtual machine in the backend pool. This traffic will be maintained for the duration of the session. </br> **None** specifies that successive requests from the same client may be handled by any virtual machine. </br> **Client IP** specifies that successive requests from the same client IP address will be handled by the same virtual machine. </br> **Client IP and protocol** ensure that successive requests from the same client IP address and protocol will be handled by the same virtual machine. </br> Learn more about [distribution modes](load-balancer-distribution-mode.md). |
| Idle timeout (minutes) | Keep a **TCP** or **HTTP** connection open without relying on clients to send keep-alive messages |  
| TCP reset | Load balancer can send **TCP resets** to help create a more predictable application behavior on when the connection is idle. </br> Learn more about [TCP reset](load-balancer-tcp-reset.md)|
| Floating IP | Floating IP is Azure's terminology for a portion of what is known as **Direct Server Return (DSR)**. </br> DSR consists of two parts: <br> 1. Flow topology </br> 2. An IP address-mapping scheme at a platform level. </br></br> Azure Load Balancer always operates in a DSR flow topology whether floating IP is enabled or not. </br> This operation means that the outbound part of a flow is always correctly rewritten to flow directly back to the origin. </br> Without floating IP, Azure exposes a traditional load-balancing IP address-mapping scheme, the VM instances' IP. </br> Enabling floating IP changes the IP address mapping to the frontend IP of the load Balancer to allow for additional flexibility. </br> For more information, see [Multiple frontends for Azure Load Balancer](load-balancer-multivip-overview.md).|
| Create implicit outbound rules | Select **No**. </br> Default: **disableOutboundSnat = false**  </br> In this case outbound occurs via same frontend IP. </br></br> **disableOutboundSnat = true** </br>In this case, outbound rules are needed for outbound. |

:::image type="content" source="./media/manage/load-balancing-rule.png" alt-text="Add load-balancing rule." border="true":::

## Inbound NAT rules

An inbound NAT rule forwards incoming traffic sent to frontend IP address and port combination. 

The traffic is sent to a specific virtual machine or instance in the backend pool. Port forwarding is done by the same hash-based distribution as load balancing.

If your scenario requires Remote Desktop Protocol (RDP) or Secure Shell (SSH) sessions to separate VM instances in a backend pool. Multiple internal endpoints can be mapped to ports on the same frontend IP address. 

The frontend IP addresses can be used to remotely administer your VMs without an additional jump box.

If you want to add an inbound nat rule to your load balancer, go to your load balancer in the Azure portal, select **Inbound NAT rules**, and then select **+Add**.

| Setting | Details |
| ---------- | ---------- |
| Name | The name of your inbound NAT rule |
| Frontend IP address | Select the frontend IP address. </br> The frontend IP address of your load balancer you want the inbound NAT rule associated to. |
| IP Version | Your options are IPv4 and IPv6. |
| Service | The type of service you'll be running on Azure Load Balancer. </br> A selection here will update the port information appropriately. |
| Protocol | Azure Load Balancer is a layer 4 network load balancer. </br> Your options are: TCP or UDP. |
| Idle timeout (minutes) | Keep a TCP or HTTP connection open without relying on clients to send keep-alive messages. |
| TCP Reset | Load Balancer can send TCP resets to help create a more predictable application behavior on when the connection is idle. </br> Learn more about [TCP reset](load-balancer-tcp-reset.md) |
| Port | This setting is the port associated with the frontend IP that you want traffic to be distributed based on this inbound NAT rule. |
| Target virtual machine | The virtual machine part of the backend pool you would like this rule to be associated to. |
| Port mapping | This setting can be default or custom based on your application preference. |

:::image type="content" source="./media/manage/inbound-nat-rule.png" alt-text="Add inbound NAT rule." border="true":::

## Outbound rules

Load balancer outbound rules configure outbound SNAT for VMs in the backend pool.

If you want to add an outbound rule to your load balancer, go to your load balancer in the Azure portal, select **Outbound rules**, and then select **+Add**.

| Setting | Details |
| ------- | ------ |
| Name | The name of your outbound rule. |
| Frontend IP address | Select the frontend IP address. </br> The frontend IP address of your load balancer you want the outbound rule to be associated to. |
| Protocol | Azure Load Balancer is a layer 4 network load balancer. </br> Your options are: **All**, **TCP**, or **UDP**. |
| Idle timeout (minutes) | Keep a **TCP** or **HTTP** connection open without relying on clients to send keep-alive messages. |
| TCP Reset | Load balancer can send **TCP resets** to help create a more predictable application behavior on when the connection is idle. </br> Learn more about [TCP reset](load-balancer-tcp-reset.md) |
| Backend pool | The backend pool you would like this outbound rule to be applied on. |

### Port allocation

| Setting | Details |
| ------- | ------ |
| Port allocation | We recommend selecting **Manually choose number of outbound ports**.|

### Outbound ports

| Setting | Details |
| ------- | ------ |
| Choose by | Select **Ports per instance** |
| Ports per instance | Enter **10,000**. |

:::image type="content" source="./media/manage/outbound-rule.png" alt-text="Add inbound outbound rule." border="true":::

## Next Steps

In this article, you learned about the different terms and settings in the Azure portal for Azure Load Balancer.

* [Learn](./load-balancer-overview.md) more about Azure Load Balancer.
* [FAQs](./load-balancer-faqs.md) for Azure Load Balancer.