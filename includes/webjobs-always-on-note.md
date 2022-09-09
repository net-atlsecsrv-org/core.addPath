---
title: include file
description: include file
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 06/26/2020
ms.author: glenga
ms.custom: include file
---

> [!NOTE]
> A web app can time out after 20 minutes of inactivity. and only requests to the actual web app can reset the timer. Viewing the app's configuration in the Azure portal or making requests to the advanced tools site (`https://<app_name>.scm.azurewebsites.net`) doesn't reset the timer. If you set your web app to run continuous or scheduled (timer-trigger) WebJobs, enable the **Always on** setting on your web app's Azure **Configuration** page to ensure that the WebJobs run reliably. This feature is available only in the Basic, Standard, and Premium [pricing tiers](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
