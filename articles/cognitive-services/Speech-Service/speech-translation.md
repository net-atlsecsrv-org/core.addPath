---
title: Speech translation with Azure Speech Services
titlesuffix: Azure Cognitive Services
description: The Speech Services let you add end-to-end, real-time, multi-language translation of speech to your applications, tools, and devices. The same API can be used for both speech-to-speech and speech-to-text translation.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: erhopf
ms.custom: seodec18
---

# What is speech translation?

Speech translation from Azure Speech Services, enables real-time, multi-language speech-to-speech and speech-to-text translation of audio streams. With the Speech SDK, your applications, tools, and devices have access to source transcriptions and translation outputs for provided audio. Interim transcription and translation results are returned as speech is detected, and finals results can be converted into synthesized speech.

Microsoft's translation engine is powered by two different approaches: statistical machine translation (SMT) and neural machine translation (NMT). SMT uses advanced statistical analysis to estimate the best possible translations given the context of a few words. With NMT, neural networks are used to provide more accurate, natural-sounding translations by using the full context of sentences to translate words.

Today, Microsoft uses NMT for translation to most popular languages. All [languages available for speech-to-speech translation](language-support.md#speech-translation) are powered by NMT. Speech-to-text translation may use SMT or NMT depending on the language pair. When the target language is supported by NMT, the full translation is NMT-powered. When the target language isn't supported by NMT, the translation is a hybrid of NMT and SMT, using English as a "pivot" between the two languages.

## Core features

Here are the features available via the Speech SDK and REST APIs:

| Use case | SDK | REST |
|----------|-----|------|
| Speech-to-text translation with recognition results. | Yes | No |
| Speech-to-speech translation. | Yes | No |
| Interim recognition and translation results. | Yes | No |

## Get started with speech translation

We offer quickstarts designed to have you running code in less than 10 minutes. This table includes a list of speech translation quickstarts organized by language.

| Quickstart | Platform | API reference |
|------------|----------|---------------|
| [C#, .NET Core](quickstart-translate-speech-dotnetcore-windows.md) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#, .NET Framework](quickstart-translate-speech-dotnetframework-windows.md) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#, UWP](quickstart-translate-speech-uwp.md) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C++](quickstart-translate-speech-cpp-windows.md) | Windows | [Browse](https://aka.ms/csspeech/cppref)|
| [Java](quickstart-translate-speech-java-jre.md) | Windows | [Browse](https://aka.ms/csspeech/javaref) |

## Sample code

Sample code for the Speech SDK is available on GitHub. These samples cover common scenarios like reading audio from a file or stream, continuous and single-shot recognition/translation, and working with custom models.

* [Speech-to-text and translation samples (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## Migration guides

> [!WARNING]
> Translator Speech will be decommissioned on October 15, 2019.

If your applications, tools, or products are using Translator Speech, we've created guides to help you migrate to the Speech Services.

* [Migrate from the Translator Speech API to the Speech Services](how-to-migrate-from-translator-speech-api.md)

## Reference docs

* [Speech SDK](speech-sdk-reference.md)
* [Speech Devices SDK](speech-devices-sdk.md)
* [REST API: Speech-to-text](rest-speech-to-text.md)
* [REST API: Text-to-speech](rest-text-to-speech.md)
* [REST API: Batch transcription and customization](https://westus.cris.ai/swagger/ui/index)

## Next steps

* [Get a Speech Services subscription key for free](get-started.md)
* [Get the Speech SDK](speech-sdk.md)
