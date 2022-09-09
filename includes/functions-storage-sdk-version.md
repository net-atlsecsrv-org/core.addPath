---
title: include file
description: include file
services: functions
author: tdykstra
manager: cfowler
ms.service: functions
ms.topic: include
ms.date: 05/17/2018
ms.author: tdykstra
ms.custom: include file
---

### Azure Storage SDK version in Functions 1.x

In Functions 1.x, the Storage triggers and bindings use version 7.2.1 of the Azure Storage SDK ([WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1) NuGet package). If you reference a different version of the Storage SDK, and you bind to a Storage SDK type in your function signature, the Functions runtime may report that it can't bind to that type. The solution is to make sure your project references [WindowsAzure.Storage 7.2.1](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1).
