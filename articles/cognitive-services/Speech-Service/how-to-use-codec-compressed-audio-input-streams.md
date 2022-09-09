---
title: Stream codec compressed audio with the Speech SDK - Speech service
titleSuffix: Azure Cognitive Services
description: Learn how to stream compressed audio to the Speech service with the Speech SDK. Available for C++, C#, and Java for Linux, Java in Android and Objective-C in iOS.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: amishu
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-set-twenty-two
---

# Use codec compressed audio input with the Speech SDK

The Speech service SDK **Compressed Audio Input Stream** API provides a way to stream compressed audio to the Speech service using either a `PullStream` or `PushStream`.

Platform | Languages | Supported GStreamer version
| :--- | ---: | :---:
Windows (excluding UWP)  | C++, C#, Java, Python | [1.15.1](https://gstreamer.freedesktop.org/data/pkg/windows/1.15.1/)
Linux  | C++, C#, Java, Python | [supported Linux distributions and target architectures](~/articles/cognitive-services/speech-service/speech-sdk.md)
Android  | Java | [1.14.4](https://gstreamer.freedesktop.org/data/pkg/android/1.14.4/)

## Speech SDK version required for compressed audio input
* Speech SDK version 1.10.0 or later is required for RHEL 8 and CentOS 8
* Speech SDK version 1.11.0 or later is required for for Windows.

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

## Prerequisites

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/python/prerequisites.md)]
::: zone-end

## Example code using codec compressed audio input

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/examples.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/examples.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/examples.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/python/examples.md)]
::: zone-end

## Next steps

> [!div class="nextstepaction"]
> [Learn how to recognize speech](./get-started-speech-to-text.md)