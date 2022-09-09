---
title: Voice Assistants on Windows - Above Lock Implementation Guidelines
titleSuffix: Azure Cognitive Services
description: The instructions to implement voice activation and above-lock capabilities for a voice agent application.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
---

# Implementing Voice Assistants on Windows

This guide walks through important implementation details for creating a voice assistant on Windows.

## Implementing voice activation

After [setting up your environment](how-to-windows-voice-assistants-get-started.md) and learning [how voice activation works](windows-voice-assistants-overview.md#how-does-voice-activation-work), you can start implementing voice activation for your own voice assistant application.

### Registration

#### Ensure that the microphone is available and accessible, then monitor its state

MVA needs a microphone to be present and accessible to be able to detect a voice activation. Use the [AppCapability](https://docs.microsoft.com/uwp/api/windows.security.authorization.appcapabilityaccess.appcapability?view=winrt-18362), [DeviceWatcher](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.devicewatcher?view=winrt-18362), and [MediaCapture](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacapture?view=winrt-18362) classes to check for microphone privacy access, device presence, and device status (like volume and mute) respectively.

### Register the application with the background service

In order for MVA to launch the application in the background, the application needs to be registered with the Background Service. See a full guide for Background Service registration [here](https://docs.microsoft.com/windows/uwp/launch-resume/register-a-background-task).

### Unlock the Limited Access Feature

Use your Microsoft-provided Limited Access Feature key to unlock the voice assistant feature. Use the [LimitedAccessFeature](https://docs.microsoft.com/uwp/api/windows.applicationmodel.limitedaccessfeatures?view=winrt-18362) class from the Windows SDK to do this.

### Register the keyword for the application

The application needs to register itself, its keyword model, and its language with Windows.

Start by retrieving the keyword detector. In this sample code, we retrieve the first detector, but you can select a particular detector by selecting it from `configurableDetectors`.

```csharp
private static async Task<ActivationSignalDetector> GetFirstEligibleDetectorAsync()
{
    var detectorManager = ConversationalAgentDetectorManager.Default;
    var allDetectors = await detectorManager.GetAllActivationSignalDetectorsAsync();
    var configurableDetectors = allDetectors.Where(candidate => candidate.CanCreateConfigurations
        && candidate.Kind == ActivationSignalDetectorKind.AudioPattern
        && (candidate.SupportedModelDataTypes.Contains("MICROSOFT_KWSGRAPH_V1")));

    if (configurableDetectors.Count() != 1)
    {
        throw new NotSupportedException($"System expects one eligible configurable keyword spotter; actual is {configurableDetectors.Count()}.");
    }

    var detector = configurableDetectors.First();

    return detector;
}
```

After retrieving the ActivationSignalDetector object, call its `ActivationSignalDetector.CreateConfigurationAsync` method with the signal ID, model ID, and display name to register your keyword and retrieve your application's `ActivationSignalDetectionConfiguration`. The signal and model IDs should be guids decided on by the developer and stay consistent for the same keyword.

### Verify that the voice activation setting is enabled

To use voice activation, a user needs to enable voice activation for their system and enable voice activation for their application. You can find the setting under "Voice activation privacy settings" in Windows settings. To check the status of the voice activation setting in your application, use the instance of the `ActivationSignalDetectionConfiguration` from registering the keyword. The [AvailabilityInfo](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-uwp/UWPVoiceAssistantSample/UIAudioStatus.cs#L128) field on the `ActivationSignalDetectionConfiguration` contains an enum value that describes the state of the voice activation setting.

### Retrieve a ConversationalAgentSession to register the app with the MVA system

The `ConversationalAgentSession` is a class in the Windows SDK that allows your app to update Windows with the app state (Idle, Detecting, Listening, Working, Speaking) and receive events, such as activation detection and system state changes such as the screen locking. Retrieving an instance of the AgentSession also serves to register the application with Windows as activatable by voice. It is best practice to maintain one reference to the `ConversationalAgentSession`. To retrieve the session, use the `ConversationalAgentSession.GetCurrentSessionAsync` API.

### Listen to the two activation signals: the OnBackgroundActivated and OnSignalDetected

Windows will signal your app when it detects a keyword in one of two ways. If the app is not active (that is, you do not have a reference to a non-disposed instance of `ConversationalAgentSession`), then it will launch your app and call the OnBackgroundActivated method in the App.xaml.cs file of your application. If the event arguments' `BackgroundActivatedEventArgs.TaskInstance.Task.Name` field matches the string "AgentBackgroundTrigger", then the application startup was triggered by voice activation. The application needs to override this method and retrieve an instance of ConversationalAgentSession to signal to Windows that is now active. When the application is active, Windows will signal the occurrence of a voice activation using the `ConversationalAgentSession.OnSignalDetected` event. Add an event handler to this event as soon as you retrieve the `ConversationalAgentSession`.

## Keyword verification

Once a voice agent application is activated by voice, the next step is to verify that the keyword detection was valid. Windows does not provide a solution for keyword verification, but it does allow voice assistants to access the audio from the hypothesized activation and complete verification on its own.

### Retrieve activation audio

Create an [AudioGraph](https://docs.microsoft.com/uwp/api/windows.media.audio.audiograph) and pass it to the `CreateAudioDeviceInputNodeAsync` of the `ConversationalAgentSession`. This will load the graph's audio buffer with the audio *starting approximately 3 seconds before the keyword was detected*. This additional leading audio is included to accommodate a wide range of keyword lengths and speaker speeds. Then, handle the [QuantumStarted](https://docs.microsoft.com/uwp/api/windows.media.audio.audiograph.quantumstarted?view=winrt-18362) event from the audio graph to retrieve the audio data.

```csharp
var inputNode = await agentSession.CreateAudioDeviceInputNodeAsync(audioGraph);
var outputNode = inputGraph.CreateFrameOutputNode();
inputNode.AddOutgoingConnection(outputNode);
audioGraph.QuantumStarted += OnQuantumStarted;
```

Note: The leading audio included in the audio buffer can cause keyword verification to fail. To fix this issue, trim the beginning of the audio buffer before you send the audio for keyword verification. This initial trim should be tailored to each assistant.

### Launch in the foreground

When keyword verification succeeds, the application needs to move to the foreground to display UI. Call the `ConversationalAgentSession.RequestForegroundActivationAsync` API to move your application to the foreground.

### Transition from compact view to full view

When your application is first activated by voice, it is started in a compact view. Please read the [Design guidance for voice activation preview](windows-voice-assistants-best-practices.md#design-guidance-for-voice-activation-preview) for guidance on the different views and transitions between them for voice assistants on Windows.

To make the transition from compact view to full app view, use the ApplicationView API `TryEnterViewModeAsync`:

```csharp
var appView = ApplicationView.GetForCurrentView();
await appView.TryEnterViewModeAsync(ApplicationViewMode.Default);
```

## Implementing above lock activation

The following steps cover the requirements to enable a voice assistant on Windows to run above lock, including references to example code and guidelines for managing the application lifecycle. For guidance on designing above lock experiences, visit the [best practices guide](windows-voice-assistants-best-practices.md).

### Detecting lock screen transitions

The ConversationalAgent library in the Windows SDK provides an API to make the lock screen state and changes to the lock screen state easily accessible. To detect the current lock screen state, check the `ConversationalAgentSession.IsUserAuthenticated` field. To detect changes in lock state, add an event handler to the `ConversationalAgentSession` object's `SystemStateChanged` event. It will fire whenever the screen changes from unlocked to locked or vice versa. If the value of the event arguments is `ConversationalAgentSystemStateChangeType.UserAuthentication`, then the lock screen state has changed and the application should close.

```csharp
// When the app changes lock state, close the application to prevent duplicates running at once
conversationalAgentSession.SystemStateChanged += (s, e) =>
{
    if (e.SystemStateChangeType == ConversationalAgentSystemStateChangeType.UserAuthentication)
    {
        WindowService.CloseWindow();
    }
};
```

### Detecting above lock activation user preference

The application entry in the Voice Activation Privacy settings page has a toggle for above lock functionality. For your app to be able to launch above lock, the user will need to turn this setting on. The status of above lock permissions is also stored in the ActivationSignalDetectionConfiguration object. The AvailabilityInfo.HasLockScreenPermission status reflects whether the user has given above lock permission. If this setting is disabled, a voice application can prompt the user to navigate to the appropriate settings page at the link "ms-settings:privacy-voiceactivation" with instructions on how to enable above-lock activation for the application.

## Closing the application

To properly close the application programmatically while above or below lock, use the `WindowService.CloseWindow()` API. This triggers all UWP lifecycle methods, including OnSuspend, allowing the application to dispose of its `ConversationalAgentSession` instance before closing.

## Next steps

> [!div class="nextstepaction"]
> [Visit the UWP Voice Assistant Sample app for examples and code walk-throughs](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample)
