---
title: 'Quickstart: Speech SDK C++ (Linux) platform setup - Speech service'
titleSuffix: Azure Cognitive Services
description: Use this guide to set up your platform for C++ on Linux with the Speech service SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
---


This guide shows how to install the [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) for Linux

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## System requirements

Linux; see the list of [supported Linux distributions and target architectures](~/articles/cognitive-services/speech-service/speech-sdk.md).

## Prerequisites

To complete this quickstart, you'll need:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)

* Supported Linux platforms will require certain libraries installed (`libssl` for secure sockets layer support and `libasound2` for sound support). Refer to your distribution below for the commands needed to install the correct versions of these libraries.

   * On Ubuntu/Debian:

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.0 libasound2 wget
     ```

     If libssl1.0.0 is not available, install libssl1.0.x (where x is greater than 0) or libssl1.1 instead.

   * On RHEL/CentOS:

     ```sh
     sudo yum update
     sudo yum groupinstall "Development tools"
     sudo yum install alsa-lib openssl wget
     ```

> [!NOTE]
> - On RHEL/CentOS 7, follow the instructions on [how to configure RHEL/CentOS 7 for Speech SDK](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).
> - On RHEL/CentOS 8, follow the instructions on [how to configure OpenSSL for Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

[!INCLUDE [linux-install-sdk](linux-install-sdk.md)]

## Next steps

[!INCLUDE [windows](../quickstart-list.md)]
