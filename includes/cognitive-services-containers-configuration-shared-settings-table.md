---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2019
---

The container has the following configuration settings:

|Required|Setting|Purpose|
|--|--|--|
|Yes|[ApiKey](#apikey-configuration-setting)|Tracks billing information.|
|No|[ApplicationInsights](#applicationinsights-setting)|Enables adding [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) telemetry support to your container.|
|Yes|[Billing](#billing-configuration-setting)|Specifies the endpoint URI of the service resource on Azure.|
|Yes|[Eula](#eula-setting)| Indicates that you've accepted the license for the container.|
|No|[Fluentd](#fluentd-settings)|Writes log and, optionally, metric data to a Fluentd server.|
|No|Http Proxy|Configures an HTTP proxy for making outbound requests.|
|No|[Logging](#logging-settings)|Provides ASP.NET Core logging support for your container. |
|No|[Mounts](#mount-settings)|Reads and writes data from the host computer to the container and from the container back to the host computer.|
