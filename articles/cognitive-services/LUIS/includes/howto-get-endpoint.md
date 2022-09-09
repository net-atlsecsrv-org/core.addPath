---
title: include file
description: include file 
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 11/05/2019
ms.author: diberry
---

In the **Manage** section (top right menu), on the **Azure Resources** page (left menu), copy the **Example Query** URL then paste into a new browser tab. 

The endpoint URL looks like the following format, with your own app ID and endpoint key replacing APP-ID and KEY-ID:

```console
https://westus.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/production/predict?subscription-key=KEY-ID&verbose=true&show-all-intents=true&log=true&query=YOUR_QUERY_HERE
```