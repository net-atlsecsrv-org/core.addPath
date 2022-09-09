---
title: Speech translation overview - Speech service
titleSuffix: Azure Cognitive Services
description: Speech translation allows you to add end-to-end, real-time, multi-language translation of speech to your applications, tools, and devices. The same API can be used for both speech-to-speech and speech-to-text translation. This article is an overview of the benefits and capabilities of the speech translation service.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: erhopf
ms.custom: devx-track-csharp, cog-serv-seo-aug-2020
keywords: speech translation
---

# What is speech translation?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

In this overview, you learn about the benefits and capabilities of the speech translation service, which enables real-time, multi-language speech-to-speech and speech-to-text translation of audio streams. With the Speech SDK, your applications, tools, and devices have access to source transcriptions and translation outputs for provided audio. Interim transcription and translation results are returned as speech is detected, and final results can be converted into synthesized speech.

Microsoft's translation engine is powered by two different approaches: statistical machine translation (SMT) and neural machine translation (NMT). SMT uses advanced statistical analysis to estimate the best possible translations given the context of a few words. With NMT, neural networks are used to provide more accurate, natural-sounding translations by using the full context of sentences to translate words.

Today, Microsoft uses NMT for translation to most popular languages. All [languages available for speech-to-speech translation](language-support.md#speech-translation) are powered by NMT. Speech-to-text translation may use SMT or NMT depending on the language pair. When the target language is supported by NMT, the full translation is NMT-powered. When the target language isn't supported by NMT, the translation is a hybrid of NMT and SMT, using English as a "pivot" between the two languages.

## Core features

* Speech-to-text translation with recognition results.
* Speech-to-speech translation.
* Support for translation to multiple target languages.
* Interim recognition and translation results.

## Get started 

See the [quickstart](get-started-speech-translation.md) to get started with speech translation. The speech translation service is available via the [Speech SDK](speech-sdk.md) and the [Speech CLI](spx-overview.md).

## Sample code

Sample code for the Speech SDK is available on GitHub. These samples cover common scenarios like reading audio from a file or stream, continuous and single-shot recognition/translation, and working with custom models.

* [Speech-to-text and translation samples (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## Migration guides

If your applications, tools, or products are using the [Translator Speech API](https://docs.microsoft.com/azure/cognitive-services/translator-speech/overview), we've created guides to help you migrate to the Speech service.

* [Migrate from the Translator Speech API to the Speech service](how-to-migrate-from-translator-speech-api.md)

## Reference docs

* [Speech SDK](speech-sdk-reference.md)
* [Speech Devices SDK](speech-devices-sdk.md)
* [REST API: Speech-to-text](rest-speech-to-text.md)
* [REST API: Text-to-speech](rest-text-to-speech.md)
* [REST API: Batch transcription and customization](https://westus.cris.ai/swagger/ui/index)

## Next steps

* Complete the speech translation [quickstart](get-started-speech-translation.md)
* [Get a Speech service subscription key for free](overview.md#try-the-speech-service-for-free)
* [Get the Speech SDK](speech-sdk.md)
