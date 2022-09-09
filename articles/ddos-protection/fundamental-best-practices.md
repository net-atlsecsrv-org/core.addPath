---
title: Azure DDoS Protection fundamental best practices
description: Learn the best security practices using DDoS protection.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh

---
# Fundamental best practices

The following sections give prescriptive guidance to build DDoS-resilient services on Azure.

## Design for security

Ensure that security is a priority throughout the entire lifecycle of an application, from design and implementation to deployment and operations. Applications can have bugs that allow a relatively low volume of requests to use an inordinate amount of resources,  resulting in a service outage. 

To help protect a service running on Microsoft Azure, you should have a good understanding of your application architecture and focus on the [five pillars of software quality](/azure/architecture/guide/pillars).
You should know typical traffic volumes, the connectivity model between the application and other applications, and the service endpoints that are exposed to the public internet.

Ensuring that an application is resilient enough to handle a denial of service that's targeted at the application itself is most important. Security and privacy are built into the Azure platform, beginning with the [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl/default.aspx). The SDL addresses security at every development phase and ensures that Azure is continually updated to make it even more secure.

## Design for scalability

Scalability is how well a system can handle increased load. Design your applications to [scale horizontally](/azure/architecture/guide/design-principles/scale-out) to meet the demand of an amplified load, specifically in the event of a DDoS attack. If your application depends on a single instance of a service, it creates a single point of failure. Provisioning multiple instances makes your system more resilient and more scalable.

For [Azure App Service](/azure/app-service/app-service-value-prop-what-is), select an [App Service plan](/azure/app-service/overview-hosting-plans) that offers multiple instances. For Azure Cloud Services, configure each of your roles to use [multiple instances](/azure/cloud-services/cloud-services-choose-me). 
For [Azure Virtual Machines](../virtual-machines/index.yml), ensure that your virtual machine (VM) architecture includes more than one VM and that each VM is
included in an [availability set](../virtual-machines/windows/tutorial-availability-sets.md). We recommend using [virtual machine scale sets](../virtual-machine-scale-sets/overview.md)
for autoscaling capabilities.

## Defense in depth

The idea behind defense in depth is to manage risk by using diverse defensive strategies. Layering security defenses in an application reduces the chance of a successful attack. We recommend that you implement secure designs for your applications by using the built-in capabilities of the Azure platform.

For example, the risk of attack increases with the size (*surface area*) of the application. You can reduce the surface area by using an approval list to close down the exposed IP address space and listening ports that are not needed on the load balancers ([Azure Load Balancer](/azure/load-balancer/load-balancer-get-started-internet-portal) and [Azure Application Gateway](/azure/application-gateway/application-gateway-create-probe-portal)). [Network security groups (NSGs)](/azure/virtual-network/security-overview) are another way to reduce the attack surface.
You can use [service tags](/azure/virtual-network/security-overview#service-tags) and [application security groups](/azure/virtual-network/security-overview#application-security-groups) to minimize complexity for creating security rules and configuring network security, as a natural extension of an application’s structure.

You should deploy Azure services in a [virtual network](/azure/virtual-network/virtual-networks-overview) whenever possible. This practice allows service resources to communicate through private IP addresses. Azure service traffic from a virtual network uses public IP addresses as source IP addresses by default. Using [service endpoints](/azure/virtual-network/virtual-network-service-endpoints-overview) will switch service traffic to use virtual network private addresses as the source IP addresses when they're accessing the Azure service from a virtual network.

We often see customers' on-premises resources getting attacked along with their resources in Azure. If you're connecting an on-premises environment to Azure, we recommend that you minimize exposure of on-premises resources to the public internet. You can use the scale and advanced DDoS protection capabilities of Azure by deploying your well-known public entities in Azure. Because these publicly accessible entities are often a target for DDoS attacks, putting them in Azure reduces the impact on your on-premises resources.

## Next steps

- Learn how to [create a DDoS protection plan](manage-ddos-protection.md).