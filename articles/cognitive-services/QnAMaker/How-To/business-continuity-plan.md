---
title: Business continuity plan - QnA Maker
titleSuffix: Azure Cognitive Services
description: The primary objective of the business continuity plan is to create a resilient knowledge base endpoint, which would ensure no down time for the Bot or the application consuming it.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: diberry
---

# Create a business continuity plan for your QnA Maker service

The primary objective of the business continuity plan is to create a resilient knowledge base endpoint, which would ensure no down time for the Bot or the application consuming it.

![QnA Maker bcp plan](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

The high-level idea as represented above is as follows:

1. Set up two parallel [QnA Maker services](../How-To/set-up-qnamaker-service-azure.md) in [Azure paired regions](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

2. Keep the primary and secondary Azure search indexes in sync. Use the GitHub sample [here](https://github.com/pchoudhari/QnAMakerBackupRestore) to see how to backup-restore Azure indexes.

3. Back up the Application Insights using [continuous export](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry).

4. Once the primary and secondary stacks have been set up, use [traffic manager](https://docs.microsoft.com/azure/traffic-manager/) to configure the two endpoints and set up a routing method.

5. You would need to create an SSL certificate for your traffic manager endpoint. [Bind the SSL certificate](https://docs.microsoft.com/azure/app-service/configure-ssl-bindings) in your App services.

6. Finally, use the traffic manager endpoint in your Bot or App.

## Next steps

> [!div class="nextstepaction"]
> [Choose capacity for your QnA Maker deployment](../Tutorials/choosing-capacity-qnamaker-deployment.md)
