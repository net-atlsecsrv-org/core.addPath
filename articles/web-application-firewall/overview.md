---
title: Introduction to Azure Web Application Firewall 
description: This article provides an overview of Azure Web Application Firewall (WAF)
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 03/06/2020
ms.author: victorh
ms.topic: overview
---

# What is Azure Web Application Firewall?

Web Application Firewall (WAF) provides centralized protection of your web applications from common exploits and vulnerabilities. Web applications are increasingly targeted by malicious attacks that exploit commonly known vulnerabilities. SQL injection and cross-site scripting are among the most common attacks.

![WAF overview](media/overview/wafoverview.png)

Preventing such attacks in application code is challenging. It can require rigorous maintenance, patching, and monitoring at multiple layers of the application topology. A centralized web application firewall helps make security management much simpler. A WAF also gives application administrators better assurance of protection against threats and intrusions.

A WAF solution can  react to a security threat faster by centrally patching a known vulnerability, instead of securing each individual web application.

## Supported services

WAF can be deployed with [Azure Application Gateway](../application-gateway/overview.md) and [Azure Front Door Service](../frontdoor/front-door-overview.md). Both services are Layer-7 (HTTP/S) load balancers, but Application Gateway is a regional service and Front Door is a global service. WAF has features that are customized for each specific service.

For more information, see the WAF overview for each service.

## Next steps

- For more information about Web Application Firewall on Application Gateway, see [Web Application Firewall on Azure Application Gateway](./ag/ag-overview.md).
- For more information about Web Application Firewall on Azure Front Door Service, see [Web Application Firewall on Azure Front Door Service](./afds/afds-overview.md).
