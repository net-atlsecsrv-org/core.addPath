---
title: What are the Speech Services?
titleSuffix: Azure Cognitive Services
description: The Speech Services are the unification of speech-to-text, text-to-speech, and speech translation into a single Azure subscription. It's easy to add speech your applications, tools, and devices with the Speech SDK, Speech Devices SDK, or REST APIs. Add speech functionality to an existing chat bot, convert text-to-speech in a translation application, or transcribe large volumes of call center data.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 07/05/2019
ms.author: erhopf
---

# What are the Speech Services?

The Speech Services are the unification of speech-to-text, text-to-speech, and speech-translation into a single Azure subscription. It's easy to speech enable your applications, tools, and devices with the [Speech SDK](speech-sdk-reference.md), [Speech Devices SDK](https://aka.ms/sdsdk-quickstart), or [REST APIs](rest-apis.md).

> [!IMPORTANT]
> Speech Services have replaced Bing Speech API, Translator Speech, and Custom Speech. See *How-to guides > Migration* for migration instructions.

These features make up the Azure Speech Services. Use the links in this table to learn more about common use cases for each feature or browse the API reference.

| Service | Feature | Description | SDK | REST |
|---------|---------|-------------|-----|------|
| [Speech-to-Text](speech-to-text.md) | Speech-to-text | Speech-to-text transcribes audio streams to text in real time that your applications, tools, or devices can consume or display. Use speech-to-text with [Language Understanding (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/) to derive user intents from transcribed speech and act on voice commands. | [Yes](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Yes](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Batch Transcription](batch-transcription.md) | Batch transcription enables asynchronous speech-to-text transcription of large volumes of data. This is a REST-based service, which uses same endpoint as customization and model management. | No | [Yes](https://westus.cris.ai/swagger/ui/index) |
| | [Conversation Transcription](conversation-transcription-service.md) | Enables real-time speech recognition, speaker identification, and diarization. It's perfect for transcribing in-person meetings with the ability to distinguish speakers. | Yes | No |
| | [Create Custom Speech Models](#customize-your-speech-experience) | If you are using speech-to-text for recognition and transcription in a unique environment, you can create and train custom acoustic, language, and pronunciation models to address ambient noise or industry-specific vocabulary. | No | [Yes](https://westus.cris.ai/swagger/ui/index) |
| [Text-to-Speech](text-to-speech.md) | Text-to-speech | Text-to-speech converts input text into human-like synthesized speech using [Speech Synthesis Markup Language (SSML)](text-to-speech.md#speech-synthesis-markup-language-ssml). Choose from standard voices and neural voices (see [Language support](language-support.md)). | [Yes](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Yes](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Create Custom Voices](#customize-your-speech-experience) | Create custom voice fonts unique to your brand or product. | No | [Yes](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Speech Translation](speech-translation.md) | Speech translation | Speech translation enables real-time, multi-language translation of speech to your applications, tools, and devices. Use this service for speech-to-speech and speech-to-text translation. | [Yes](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | No |
| [Voice-first Virtual Assistants](voice-first-virtual-assistants.md) | Voice-first virtual assistants | Custom virtual assistants using Azure Speech Services empower developers to create natural, human-like conversational interfaces for their applications and experiences. The Bot Framework's Direct Line Speech channel enhances these capabilities by providing a coordinated, orchestrated entry point to a compatible bot that enables voice in, voice out interaction with low latency and high reliability. | [Yes](voice-first-virtual-assistants.md) | No |

## News and updates

Learn what's new with the Azure Speech Services.

* September 2019
  * Released Speech SDK 1.7.0. For a full list of updates, enhancements, and known issues, see [Release notes](releasenotes.md).
* August 2019
  * **New tutorial**: [Voice enable your bot with the Speech SDK, C#](tutorial-voice-enable-your-bot-speech-sdk.md)
  * Added a new speaking style, [`chat`](speech-synthesis-markup.md#adjust-speaking-styles), for the `en-US-JessaNeural` voice. 
* June 2019
  * Released Speech SDK 1.6.0. For a full list of updates, enhancements, and known issues, see [Release notes](releasenotes.md).
* May 2019 - Documentation is now available for [Conversation Transcription](conversation-transcription-service.md), [Call Center Transcription](call-center-transcription.md), and [Voice-first Virtual Assistants](voice-first-virtual-assistants.md).
* May 2019
  * Released Speech SDK 1.5.1. For a full list of updates, enhancements, and known issues, see [Release notes](releasenotes.md).
  * Released Speech SDK 1.5.0. For a full list of updates, enhancements, and known issues, see [Release notes](releasenotes.md).

## Try Speech Services

We offer quickstarts in most popular programming languages, each designed to have you running code in less than 10 minutes. This table contains the most popular quickstarts for each feature. Use the left-hand navigation to explore additional languages and platforms.

| Speech-to-text (SDK) | Text-to-Speech (SDK) | Translation (SDK) |
|----------------------|----------------------|-------------------|
| [C#, .NET Core (Windows)](quickstart-csharp-dotnet-windows.md) | [C#, .NET Framework (Windows)](quickstart-text-to-speech-dotnet-windows.md) | [Java (Windows, Linux)](quickstart-translate-speech-java-jre.md) |
| [JavaScript (Browser)](quickstart-js-browser.md) | [C++ (Windows)](quickstart-text-to-speech-cpp-windows.md) | [C#, .NET Core (Windows)](quickstart-translate-speech-dotnetcore-windows.md) |
| [Python (Windows, Linux, macOS)](quickstart-python.md) | [C++ (Linux)](quickstart-text-to-speech-cpp-linux.md) | [C#, .NET Framework (Windows)](quickstart-translate-speech-dotnetframework-windows.md) |
| [Java (Windows, Linux)](quickstart-java-jre.md) | | [C++ (Windows)](quickstart-translate-speech-cpp-windows.md) |

> [!NOTE]
> Speech-to-text and text-to-speech also have REST endpoints and associated quickstarts.

After you've had a chance to use the Speech Services, try our tutorial that teaches you how to recognize intents from speech using the Speech SDK and LUIS.

* [Tutorial: Recognize intents from speech with the Speech SDK and LUIS, C#](how-to-recognize-intents-from-speech-csharp.md)
* [Tutorial: Voice enable your bot with the Speech SDK, C#](tutorial-voice-enable-your-bot-speech-sdk.md)
* [Tutorial: Build a Flask app to translate text, analyze sentiment, and synthesize translated text to speech, REST](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json&bc=%2fazure%2fcognitive-services%2fspeech-service%2fbreadcrumb%2ftoc.json&toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fspeech-service%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)

## Get sample code

Sample code is available on GitHub for each of the Azure Speech Services. These samples cover common scenarios like reading audio from a file or stream, continuous and single-shot recognition, and working with custom models. Use these links to view SDK and REST samples:

* [Speech-to-text, text-to-speech, and speech translation samples (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [Batch transcription samples (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
* [Text-to-speech samples (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)
* [Voice-first virtual assistant samples (SDK)](https://aka.ms/csspeech/samples)

## Customize your speech experience

Azure Speech Services works well with built-in models, however, you may want to further customize and tune the experience for your product or environment. Customization options range from acoustic model tuning to unique voice fonts for your brand. After you've built a custom model, you can use it with any of the Azure Speech Services.

| Speech Service | Platform | Description |
|----------------|-------------|-------------|
| Speech-to-Text | [Custom Speech](https://aka.ms/customspeech) | Customize speech recognition models to your needs and available data. Overcome speech recognition barriers such as speaking style, vocabulary and background noise. |
| Text-to-Speech | [Custom Voice](https://aka.ms/customvoice) | Build a recognizable, one-of-a-kind voice for your Text-to-Speech apps with your speaking data available. You can further fine-tune the voice outputs by adjusting a set of voice parameters. |

## Reference docs

* [Speech SDK](speech-sdk-reference.md)
* [Speech Devices SDK](speech-devices-sdk.md)
* [REST API: Speech-to-text](rest-speech-to-text.md)
* [REST API: Text-to-speech](rest-text-to-speech.md)
* [REST API: Batch transcription and customization](https://westus.cris.ai/swagger/ui/index)

## Next steps

> [!div class="nextstepaction"]
> [Get a Speech Services subscription key for free](get-started.md)
