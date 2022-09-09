---
title: Text-to-speech with Azure Speech Services
titleSuffix: Azure Cognitive Services
description: Text-to-speech from Azure Speech Services is a service that enables your applications, tools, or devices to convert text into natural human-like synthesized speech. Choose from standard and neural voices, or create your own custom voice unique to your product or brand. 75+ standard voices are available in more than 45 languages and locales, and 5 neural voices are available in 4 languages and locales.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: erhopf
ms.custom: seodec18
---

# What is text-to-speech?

Text-to-speech from Azure Speech Services is a service that enables your applications, tools, or devices to convert text into natural human-like synthesized speech. Choose from standard and neural voices, or create your own custom voice unique to your product or brand. 75+ standard voices are available in more than 45 languages and locales, and 5 neural voices are available in 4 languages and locales. For a full list, see [supported languages](language-support.md#text-to-speech).

Text-to-speech technology allows content creators to interact with their users in different ways. Text-to-speech can improve accessibility by providing users with an option to interact with content audibly. Whether the user has a visual impairment, a learning disability, or requires navigation information while driving, text-to-speech can improve an existing experience. Text-to-speech is also a valuable add-on for voice bots and virtual assistants.

### Standard voices

Standard voices are created using Statistical Parametric Synthesis and/or Concatenation Synthesis techniques. These voices are highly intelligible and sound quite natural. You can easily enable your applications to speak in more than 45 languages, with a wide range of voice options. These voices provide high pronunciation accuracy, including support for abbreviations, acronym expansions, date/time interpretations, polyphones, and more. Use standard voice to improve accessibility for your applications and services by allowing users to interact with your content audibly.

### Neural voices

Neural voices use deep neural networks to overcome the limits of traditional text-to-speech systems in matching the patterns of stress and intonation in spoken language, and in synthesizing the units of speech into a computer voice. Standard text-to-speech breaks down prosody into separate linguistic analysis and acoustic prediction steps that are governed by independent models. That can result in muffled, buzzy voice synthesis. Our neural capability does prosody prediction and voice synthesis simultaneously, which results in a more fluid and natural-sounding voice.

Neural voices can be used to make interactions with chatbots and virtual assistants more natural and engaging, convert digital texts such as e-books into audiobooks and enhance in-car navigation systems. With the human-like natural prosody and clear articulation of words, Neural voices significantly reduce listening fatigue when you interact with AI systems. 

Neural voices support different styles, such as neutral and cheerful. For example, the Jessa (en-US) voice can speak cheerfully, which is optimized for warm, happy conversation. You can adjust the voice output, like tone, pitch, and speed using [Speech Synthesis Markup Language](speech-synthesis-markup.md). For a full list of available voices, see [supported languages](language-support.md#text-to-speech).

To learn more about the benefits of neural voices, see [Microsoft’s new neural text-to-speech service helps machines speak like people](https://azure.microsoft.com/blog/microsoft-s-new-neural-text-to-speech-service-helps-machines-speak-like-people/).

### Custom voices

Voice customization lets you create a recognizable, one-of-a-kind voice for your brand. To create your custom voice font, you make a studio recording and upload the associated scripts as the training data. The service then creates a unique voice model tuned to your recording. You can use this custom voice font to synthesize speech. For more information, see [custom voices](how-to-customize-voice-font.md).

## Core features

This table lists the core features for text-to-speech:

| Use case | SDK | REST |
|----------|-----|------|
| Convert text to speech. | Yes | Yes |
| Upload datasets for voice adaptation. | No | Yes\* |
| Create and manage voice font models. | No | Yes\* |
| Create and manage voice font deployments. | No | Yes\* |
| Create and manage voice font tests. | No | Yes\* |
| Manage subscriptions. | No | Yes\* |

\* *These services are available using the cris.ai endpoint. See [Swagger reference](https://westus.cris.ai/swagger/ui/index). These custom voice training and management APIs implement throttling that limits requests to 25 per 5 seconds, while the speech synthesis API itself implements throttling that allows 200 requests per second as the highest. When throttling occurs, you'll be notified via message headers.*

## Get started with text to speech

We offer quickstarts designed to have you running code in less than 10 minutes. This table includes a list of text-to-speech quickstarts organized by language.

### SDK quickstarts

| Quickstart (SDK) | Platform | API reference |
|------------|----------|---------------|
| [C#, .NET Framework](quickstart-text-to-speech-dotnet-windows.md) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C++](quickstart-text-to-speech-cpp-windows.md) | Windows | [Browse](https://aka.ms/csspeech/cppref) |
| [C++](quickstart-text-to-speech-cpp-linux.md) | Linux | [Browse](https://aka.ms/csspeech/cppref) |

### REST quickstarts

| Quickstart (REST) | Platform | API reference |
|------------|----------|---------------|
| [C#, .NET Core](quickstart-dotnet-text-to-speech.md) | Windows, macOS, Linux | [Browse](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Node.js](quickstart-nodejs-text-to-speech.md) | Window, macOS, Linux | [Browse](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Python](quickstart-python-text-to-speech.md) | Window, macOS, Linux | [Browse](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |

## Sample code

Sample code for text-to-speech is available on GitHub. These samples cover text-to-speech conversion in most popular programming languages.

* [Text-to-speech samples (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [Text-to-speech samples (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## Reference docs

* [Speech SDK](speech-sdk-reference.md)
* [Speech Devices SDK](speech-devices-sdk.md)
* [REST API: Speech-to-text](rest-speech-to-text.md)
* [REST API: Text-to-speech](rest-text-to-speech.md)
* [REST API: Batch transcription and customization](https://westus.cris.ai/swagger/ui/index)

## Next steps

* [Get a free Speech Services subscription](get-started.md)
* [Create custom voice fonts](how-to-customize-voice-font.md)
