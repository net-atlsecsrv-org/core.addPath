---
title: Metrics Advisor client libraries REST API
titleSuffix: Azure Cognitive Services
description: Use this quickstart to connect your applications to the Metrics Advisor API from Azure Cognitive Services.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: quickstart
ms.date: 10/14/2020
ms.author: mbullwin
zone_pivot_groups: programming-languages-metrics-monitor
---

# Quickstart: Use the client libraries or REST APIs to customize your solution

Get started with the the Metrics Advisor REST API or client libraries. Follow these steps to install the package and try out the example code for basic tasks.

Use Metrics Advisor to perform:

* Add a data feed from a data source
* Check ingestion status
* Configure detection and alerts 
* Query the anomaly detection results
* Diagnose anomalies


::: zone pivot="programming-language-csharp"

[!INCLUDE [REST API quickstart](../includes/quickstarts/csharp.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [REST API quickstart](../includes/quickstarts/java.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [REST API quickstart](../includes/quickstarts/javascript.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [REST API quickstart](../includes/quickstarts/python.md)]

::: zone-end

::: zone pivot="programming-language-rest-api"

[!INCLUDE [REST API quickstart](../includes/quickstarts/rest-api.md)]

::: zone-end

## Clean up resources

If you want to clean up and remove a Cognitive Services subscription, you can delete the resource or resource group. Deleting the resource group also deletes any other resources associated with it.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## Next steps

- [Use the web portal](web-portal.md)
- [Onboard your data feeds](../how-tos/onboard-your-data.md)
    - [Manage data feeds](../how-tos/manage-data-feeds.md)
    - [Configurations for different data sources](../data-feeds-from-different-sources.md)
- [Configure metrics and fine tune detecting configuration](../how-tos/configure-metrics.md)
- [Adjust anomaly detection using feedback](../how-tos/anomaly-feedback.md)