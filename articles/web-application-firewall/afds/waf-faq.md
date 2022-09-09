---
title: Azure Web Application Firewall on Azure Front Door Service - frequently asked questions
description: This article provides answers to frequently asked questions about Web Application Firewall on Azure Front Door
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.devlang: na
ms.topic: article
ms.date: 05/05/2020
ms.author: victorh
---

# Frequently asked questions for Azure Web Application Firewall on Azure Front Door Service

This article answers common questions about Azure Web Application Firewall (WAF) on Azure Front Door Service features and functionality. 

## What is Azure WAF?

Azure WAF is a web application firewall that helps protect your web applications from common threats such as SQL injection, cross-site scripting, and other web exploits. You can define a WAF policy consisting of a combination of custom and managed rules to control access to your web applications.

An Azure WAF policy can be applied to web applications hosted on Application Gateway or Azure Front Doors.

## What is WAF on Azure Front Door? 

Azure Front Door is a highly scalable, globally distributed application and content delivery network. Azure WAF, when integrated with Front Door, stops denial-of-service and targeted application attacks at the Azure network edge, close to attack sources before they enter your virtual network, offers protection without sacrificing performance.

## Does Azure WAF support HTTPS?

Front Door offers TLS offloading. WAF is natively integrated with Front Door and can inspect a request after it's decrypted.

## Does Azure WAF support IPv6?

Yes. You can configure IP restriction for IPv4 and IPv6.

## How up-to-date are the managed rule sets?

We do our best to keep up with changing threat landscape. Once a new rule is updated, it's added to the Default Rule Set with a new version number.

## What is the propagation time if I make a change to my WAF policy?

Deploying a WAF policy globally usually takes about 5 minutes and often completes sooner.

## Can WAF policies be different for different regions?

When integrated with Front Door, WAF is a global resource. Same configuration applies across all Front Door locations.
 
## How do I limit access to my back-end to be from Front Door only?

You may configure IP Access Control List in your back-end to allow for only Front Door outbound IP address ranges and deny any direct access from Internet. Service tags are supported for you to use on your virtual network. Additionally, you can verify that the X-Forwarded-Host HTTP header field is valid for your web application.

## Which Azure WAF options should I choose?

There are two options when applying WAF policies in Azure. WAF with Azure Front Door is a globally distributed, edge security solution. WAF with Application Gateway is a regional, dedicated solution. We recommend you choose a solution based on your overall performance and security requirements. For more information, see [Load-balancing with Azure’s application delivery suite](../../frontdoor/front-door-lb-with-azure-app-delivery-suite.md).

## What's the recommended approach to enabling WAF on Front Door?

When you enable the WAF on an existing application, it's common to have false positive detections where the WAF rules detect legitimate traffic as a threat. To minimize the risk of an impact to your users, we recommend the following process:

* Enable the WAF in [**Detection** mode](./waf-front-door-create-portal.md#change-mode) to ensure that the WAF doesn't block requests while you are working through this process.
  > [!IMPORTANT]
  > This process describes how to enable the WAF on a new or existing solution when your priority is to minimize the disturbance to your application's users. If you are under attack or imminent threat, you may want to instead deploy the WAF in **Prevention** mode immediately, and use the tuning process to monitor and tune the WAF over time. This will probably cause some of your legitimate traffic to be blocked, which is why we only recommend doing this when you are under threat.
* Follow our [guidance for tuning the WAF](./waf-front-door-tuning.md). This process requires that you enable diagnostic logging, review the logs regularly, and add rule exclusions and other mitigations.
* Repeat this whole process, checking the logs regularly, until you're satisfied that no legitimate traffic is being blocked. The whole process may take several weeks. Ideally you should see fewer false positive detections after each tuning change you make.
* Finally, enable the WAF in **Prevention mode**.
* Even once you're running the WAF in production, you should keep monitoring the logs to identify any other false-positive detections. Regularly reviewing the logs will also help you to identify any real attack attempts that have been blocked.

## Do you support same WAF features in all integrated platforms?

Currently, ModSec CRS 2.2.9, CRS 3.0, and CRS 3.1 rules are only supported with WAF on Application Gateway. Rate-limiting, geo-filtering, and Azure managed Default Rule Set rules are supported only with WAF on Azure Front Door.

## Is DDoS protection integrated with Front Door? 

Globally distributed at Azure network edges, Azure Front Door can absorb and geographically isolate large volume attacks. You can create custom WAF policy to automatically block and rate limit http(s) attacks that have known signatures. Further more, you can enable DDoS Protection Standard on the VNet where your back-ends are deployed. Azure DDoS Protection Standard customers receive additional benefits including cost protection, SLA guarantee, and access to experts from DDoS Rapid Response Team for immediate help during an attack. For more information, see [DDoS protection on Front Door](../../frontdoor/front-door-ddos.md).

## Why do additional requests above the threshold configured for my rate limit rule get passed to my backend server?

A rate limit rule can limit abnormally high traffic from any client IP address. You may configure a threshold on the number of web requests allowed from a client IP address during a one-minute or five-minute duration. For granular rate control, rate limiting can be combined with additional match conditions such as HTTP(S) parameter matching. 

Requests from the same client often arrive at the same Front Door server. In that case, you'll see additional requests above the threshold get blocked immediately. 

However, it's possible that requests from the same client may arrive at a different Front Door server that has not refreshed the rate limit counter yet. For example, the client may open a new connection for each request and the threshold is low. In this case, the first request to the new Front Door server would pass the rate limit check. A rate limit threshold is usually set high to defend against denial of service attacks from any client IP address. For a very low threshold, you may see additional requests above the threshold get through.

## Next steps

- Learn about [Azure Web Application Firewall](../overview.md).
- Learn more about [Azure Front Door](../../frontdoor/front-door-overview.md).