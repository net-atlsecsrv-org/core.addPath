---
title: Create an Android app on Azure App Service Mobile Apps | Microsoft Docs
description: Follow this tutorial to get started with using Azure mobile app backends for Android development
services: app-service\mobile
documentationcenter: android
author: conceptdev
manager: crdun
editor: ''

ms.assetid: 355f0959-aa7f-472c-a6c7-9eecea3a34a9
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: conceptual
ms.date: 5/6/2019
ms.author: crdun

---
# Create an Android app
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## Overview
This tutorial shows you how to add a cloud-based backend service to an Android mobile app by using an Azure mobile app backend.  You will create both a new mobile app backend and a simple *Todo list* Android app that stores app data in Azure.

Completing this tutorial is a prerequisite for all other Android tutorials about using the Mobile Apps feature in Azure App Service.

## Prerequisites
To complete this tutorial, you need the following:

* [Android Developer Tools](https://developer.android.com/sdk/index.html), which includes the Android Studio integrated development environment, and the latest Android platform.
* Azure Mobile Android SDK.
* An [active Azure account](https://azure.microsoft.com/pricing/free-trial/).

## Create a new Azure mobile app backend
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## Create a database connection and configure the client and server project
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## Run the Android app
[!INCLUDE [app-service-mobile-android-run-app](../../includes/app-service-mobile-android-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203
