---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/04/2020
ms.author: trbye
---

## Prerequisites

Before you get started, make sure to:

> [!div class="checklist"]
> * [Create an Azure Speech resource](../../../../get-started.md)
> * [Setup your development environment and create an empty project](../../../../quickstarts/setup-platform.md?tabs=windows&pivots=programming-language-cpp)

[!INCLUDE [Audio input format](~/articles/cognitive-services/speech-service/includes/audio-input-format-chart.md)]

## Add sample code

1. Open the source file **helloworld.cpp**.

1. Replace all the code with the following snippet:
   
   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-file/helloworld/helloworld.cpp#code)]

1. In the same file, replace the string `YourSubscriptionKey` with your subscription key.

1. Replace the string `YourServiceRegion` with the **Region identifier** from [region](https://aka.ms/speech/sdkregion) associated with your subscription.

1. Replace the string `whatstheweatherlike.wav` with your own filename.

1. From the menu bar, choose **File** > **Save All**.

> [!NOTE]
> The Speech SDK will default to recognizing using en-us for the language, see [Specify source language for speech to text](../../../../how-to-specify-source-language.md) for information on choosing the source language.

## Build and run the application

1. From the menu bar, select **Build** > **Build Solution** to build the application. The code should compile without errors now.

1. Choose **Debug** > **Start Debugging** (or press **F5**) to start the **helloworld** application.

1. Your audio file is transmitted to the Speech service and the first utterance in the file is transcribed to text, which appears in the same window.

   ```text
   Recognizing first result...
   We recognized: What's the weather like?
   ```

## Next steps

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]