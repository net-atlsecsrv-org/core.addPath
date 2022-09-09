---
title: Speech Devices SDK documentation
titleSuffix: Azure Cognitive Services
description: The release notes provide a log of updates, enhancements, bug fixes, and changes to the Speech Devices SDK. This article is updated with each release of the Speech Devices SDK.
services: cognitive-services
author: wsturman
manager: nitinme

ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: wellsi
---

# Release notes: Speech Devices SDK

The following sections list changes in the most recent releases.

## Speech Devices SDK 1.11.0:

- Support for [arbitrary microphone array geometries](how-to-devices-microphone-array-configuration.md) and setting the working angle through a [configuration file](https://aka.ms/sdsdk-micarray-json).
- Support for [Urbetter DDK](http://www.urbetter.com/products_56/278.html).
- Released binaries for the [GGEC Speaker](https://aka.ms/sdsdk-download-speaker) used in our [Voice Assistant sample](https://aka.ms/sdsdk-speaker).
- Released binaries for [Linux ARM32](https://aka.ms/sdsdk-download-linux-arm32) and [Linux ARM 64](https://aka.ms/sdsdk-download-linux-arm64) for Raspberry Pi and similar devices.
- Updated the [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) component to version 1.11.0. For more information, see its [release notes](https://aka.ms/csspeech/whatsnew).

## Speech Devices SDK 1.9.0:

- Initial binaries for [Urbetter DDK](https://aka.ms/sdsdk-download-urbetter) (Linux ARM64) are provided.
- Roobo v1 now uses Maven for the Speech SDK
- Updated the [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) component to version 1.9.0. For more information, see its [release notes](https://aka.ms/csspeech/whatsnew).

## Speech Devices SDK 1.7.0:

- Linux ARM is now supported.
- Initial binaries for [Roobo v2 DDK](https://aka.ms/sdsdk-download-roobov2) are provided (Linux ARM64).
- Windows users can use `AudioConfig.fromDefaultMicrophoneInput()` or `AudioConfig.fromMicrophoneInput(deviceName)` to specify the microphone to be used.
- The library size has been optimized.
- Support for multi-turn recognition using the same speech/intent recognizer object.
- Fix occasional issue where the process would stop responding while stopping recognition.
- Sample apps now contain a sample participants.properties file to demonstrate the format of the file.
- Updated the [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) component to version 1.7.0. For more information, see its [release notes](https://aka.ms/csspeech/whatsnew).

## Speech Devices SDK 1.6.0:

- Support [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/) on Windows and Linux with common [sample application](https://aka.ms/sdsdk-download)
- Updated the [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) component to version 1.6.0. For more information, see its [release notes](https://aka.ms/csspeech/whatsnew).

## Speech Devices SDK 1.5.1:

- Include [Conversation Transcription](conversation-transcription-service.md) in the sample app.
- Updated the [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) component to version 1.5.1. For more information, see its [release notes](https://aka.ms/csspeech/whatsnew).

## Speech Devices SDK 1.5.0: 2019-May release

- Speech Devices SDK is now GA and no longer a gated preview.
- Updated the [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) component to version 1.5.0. For more information, see its [release notes](https://aka.ms/csspeech/whatsnew).
- New keyword technology brings significant quality improvements, see Breaking Changes.
- New audio processing pipeline for improved far-field recognition.

**Breaking changes**

- Due to the new keyword technology all keywords must be re-created at our improved keyword portal. To fully remove old keywords from the device uninstall the old app.
  - adb uninstall com.microsoft.cognitiveservices.speech.samples.sdsdkstarterapp

## Speech Devices SDK 1.4.0: 2019-Apr release

- Updated the [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) component to version 1.4.0. For more information, see its [release notes](https://aka.ms/csspeech/whatsnew).

## Speech Devices SDK 1.3.1: 2019-Mar release

- Updated the [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) component to version 1.3.1. For more information, see its [release notes](https://aka.ms/csspeech/whatsnew).
- Updated keyword handling, see Breaking Changes.
- Sample application adds choice of language for both speech recognition and translation.

**Breaking changes**

- [Installing a keyword](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws) has been simplified, it is now part of the app and does not need separate installation on the device.
- The keyword recognition has changed, and two events are supported.
  - `RecognizingKeyword,` indicates the speech result contains (unverified) keyword text.
  - `RecognizedKeyword`, indicates that keyword recognition completed recognizing the given keyword.

## Speech Devices SDK 1.1.0: 2018-Nov release

- Updated the [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) component to version 1.1.0. For more information, see its [release notes](https://aka.ms/csspeech/whatsnew).
- Far Field Speech recognition accuracy has been improved with our enhanced audio processing algorithm.
- Sample application added Chinese speech recognition support.

## Speech Devices SDK 1.0.1: 2018-Oct release

- Updated the [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) component to version 1.0.1. For more information, see its [release notes](https://aka.ms/csspeech/whatsnew).
- Speech recognition accuracy will be improved with our improved audio processing algorithm
- One continuous recognition audio session bug is fixed.

**Breaking changes**

- With this release a number of breaking changes are introduced. Please check [this page](https://aka.ms/csspeech/breakingchanges_1_0_0) for details relating to the APIs.
- The KWS model files are not compatible with Speech Devices SDK 1.0.1. The existing keyword files will be deleted after the new keyword files are written to the device.

## Speech Devices SDK 0.5.0: 2018-Aug release

- Improved the accuracy of speech recognition by fixing a bug in the audio processing code.
- Updated the [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) component to version 0.5.0. For more information, see its
  [release notes](releasenotes.md#cognitive-services-speech-sdk-050-2018-july-release).

## Speech Devices SDK 0.2.12733: 2018-May release

The first public preview release of the Cognitive Services Speech Devices SDK.
