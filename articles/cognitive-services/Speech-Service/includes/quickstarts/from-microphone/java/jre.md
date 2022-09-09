---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
---

## Prerequisites

Before you get started:

> [!div class="checklist"]
> * [Create an Azure Speech Resource](../../../../get-started.md)
> * [Setup your development environment and create an empty project](../../../../quickstarts/setup-platform.md?tabs=jre)
> * Make sure that you have access to a microphone for audio capture

## Add sample code

1. To add a new empty class to your Java project, select **File** > **New** > **Class**.

1. In the **New Java Class** window, enter **speechsdk.quickstart** into the **Package** field, and **Main** into the **Name** field.

   ![Screenshot of New Java Class window](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-06-create-main-java.png)

1. Replace all code in `Main.java` with the following snippet:

   [!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-microphone/src/speechsdk/quickstart/Main.java#code)]

1. Replace the string `YourSubscriptionKey` with your subscription key.

1. Replace the string `YourServiceRegion` with the **Region identifier** from [region](https://aka.ms/speech/sdkregion) associated with your subscription (for example, `westus` for the free trial subscription).

1. Save changes to the project.

> [!NOTE]
> The Speech SDK will default to recognizing using en-us for the language, see [Specify source language for speech to text](../../../../how-to-specify-source-language.md) for information on choosing the source language.

## Build and run the app

Press F11, or select **Run** > **Debug**.
The next 15 seconds of speech input from your microphone will be recognized and logged in the console window.

![Screenshot of console output after successful recognition](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-07-console-output.png)

## Next steps

[!INCLUDE [footer](./footer.md)]
