---
title: 'Quickstart: Recognize speech, Java (Android) - Speech Services'
titleSuffix: Azure Cognitive Services
description: Learn how to recognize speech in Java on Android by using the Speech SDK
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 2/20/2019
ms.author: wolfma
---

# Quickstart: Recognize speech in Java on Android by using the Speech SDK

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

In this article, you'll learn how to develop a Java application for Android using the Cognitive Services Speech SDK to transcribe speech to text.
The application is based on the Speech SDK Maven Package, version 1.5.1, and Android Studio 3.3.
The Speech SDK is currently compatible with Android devices having 32/64-bit ARM and Intel x86/x64 compatible processors.

> [!NOTE]
> For the Speech Devices SDK and the Roobo device, see [Speech Devices SDK](speech-devices-sdk.md).

## Prerequisites

You need a Speech Services subscription key to complete this Quickstart. You can get one for free. See [Try the Speech Services for free](get-started.md) for details.

## Create and configure a project

1. Launch Android Studio, and choose **Start a new Android Studio project** in the Welcome window.

    ![Screenshot of Android Studio Welcome window](media/sdk/qs-java-android-01-start-new-android-studio-project.png)

1. The **Choose your project** wizard appears, select **Phone and Tablet** and **Empty Activity** in the activity selection box. Select **Next**.

   ![Screenshot of Choose your project wizard](media/sdk/qs-java-android-02-target-android-devices.png)

1. In the **Configure your project** screen, enter **Quickstart** as **Name**, **samples.speech.cognitiveservices.microsoft.com** as **Package name**, and choose a project directory. For **Minimum API level** pick **API 23: Android 6.0 (Marshmallow)**, leave all other checkboxes unchecked, and select **Finish**.

   ![Screenshot of Configure your project wizard](media/sdk/qs-java-android-03-create-android-project.png)

Android Studio takes a moment to prepare your new Android project. Next, configure the project to know about the Speech SDK and to use Java 8.

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

The current version of the Cognitive Services Speech SDK is `1.5.1`.

The Speech SDK for Android is packaged as an [AAR (Android Library)](https://developer.android.com/studio/projects/android-library), which includes the necessary libraries and required Android permissions.
It is hosted in a Maven repository at https:\//csspeechstorage.blob.core.windows.net/maven/.

Set up your project to use the Speech SDK. Open the Project Structure window by choosing **File** > **Project Structure** from the Android Studio menu bar. In the Project Structure window, make the following changes:

1. In the list on the left side of the window, select **Project**. Edit the **Default Library Repository** settings by appending a comma and our Maven repository URL enclosed in single quotes. 'https:\//csspeechstorage.blob.core.windows.net/maven/'

   ![Screenshot of Project Structure window](media/sdk/qs-java-android-06-add-maven-repository.png)

1. In the same screen, on the left side, select **app**. Then select the **Dependencies** tab at the top of the window. Select the green plus sign (+), and choose **Library dependency** from the drop-down menu.

   ![Screenshot of Project Structure window](media/sdk/qs-java-android-07-add-module-dependency.png)

1. In the window that comes up, enter the name and version of our Speech SDK for Android, `com.microsoft.cognitiveservices.speech:client-sdk:1.5.1`. Then select **OK**.
   The Speech SDK should be added to the list of dependencies now, as shown below:

   ![Screenshot of Project Structure window](media/sdk/qs-java-android-08-dependency-added-1.0.0.png)

1. Select the **Properties** tab. For both **Source Compatibility** and **Target Compatibility**, select **1.8**.

   ![](media/sdk/qs-java-android-09-dependency-added.png)

1. Select **OK** to close the Project Structure window and apply your changes to the project.

## Create user interface

We will create a basic user interface for the application. Edit the layout for your main activity, `activity_main.xml`. Initially, the layout includes a title bar with your application's name, and a TextView containing the text "Hello World!".

* Click the TextView element. Change its ID attribute in the upper-right corner to `hello`.

* From the Palette in the upper left of the `activity_main.xml` window, drag a button into the empty space above the text.

* In the button's attributes on the right, in the value for the `onClick` attribute, enter `onSpeechButtonClicked`. We'll write a method with this name to handle the button event.  Change its ID attribute in the upper-right corner to `button`.

* Use the magic wand icon at the top of the designer to infer layout constraints.

  ![Screenshot of magic wand icon](media/sdk/qs-java-android-10-infer-layout-constraints.png)

The text and graphical representation of your UI should now look like this:

![](media/sdk/qs-java-android-11-gui.png)

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/res/layout/activity_main.xml)]

## Add sample code

1. Open the source file `MainActivity.java`. Replace all the code in this file with the following.

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * The `onCreate` method includes code that requests microphone and internet permissions, and initializes the native platform binding. Configuring the native platform bindings is only required once. It should be done early during application initialization.

   * The method `onSpeechButtonClicked` is, as noted earlier, the button click handler. A button press triggers speech to text transcription.

1. In the same file, replace the string `YourSubscriptionKey` with your subscription key.

1. Also replace the string `YourServiceRegion` with the [region](regions.md) associated with your subscription (for example, `westus` for the free trial subscription).

## Build and run the app

1. Connect your Android device to your development PC. Make sure you have enabled [development mode and USB debugging](https://developer.android.com/studio/debug/dev-options) on the device.

1. To build the application, press Ctrl+F9, or choose **Build** > **Make Project** from the menu bar.

1. To launch the application, press Shift+F10, or choose **Run** > **Run 'app'**.

1. In the deployment target window that appears, choose your Android device.

   ![Screenshot of Select Deployment Target window](media/sdk/qs-java-android-12-deploy.png)

Press the button in the application to begin a speech recognition section. The next 15 seconds of English speech will be sent to the Speech Services and transcribed. The result appears in the Android application, and in the logcat window in Android Studio.

![Screenshot of the Android application](media/sdk/qs-java-android-13-gui-on-device.png)

## Next steps

> [!div class="nextstepaction"]
> [Explore Java samples on GitHub](https://aka.ms/csspeech/samples)

## See also

- [Customize acoustic models](how-to-customize-acoustic-models.md)
- [Customize language models](how-to-customize-language-model.md)
