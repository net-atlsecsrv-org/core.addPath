---
title: include file
description: include file
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''

ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: jmprieur
ms.custom: include file
#Do not delete: File is used in the Azure portal guided setup experience but not rendered as part of docs.microsoft.com
---

## Add the application’s registration information to your app

In this step, you need to add the Application Id to your project.

1. Open `App.xaml.cs` and replace the line containing the `ClientId` with:

```csharp
private static string ClientId = "[Enter the application Id here]";
```
