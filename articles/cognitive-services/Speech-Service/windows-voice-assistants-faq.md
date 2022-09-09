---
title: Voice Assistants on Windows - FAQ
titleSuffix: Azure Cognitive Services
description: Common questions that frequently come up during Windows voice agent development.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
---

# Samples and FAQs

## The UWP Voice Assistant Sample

The UWP Voice Assistant Sample is a voice assistant on Windows that serves to

- demonstrate the use of the Windows ConversationalAgent APIs
- display best practices for a voice agent on Windows
- provide an adaptable app and reusable components for your MVP agent application
- show how Direct Line Speech, along with Bot Framework or Custom Commands, can be used together with the Windows ConversationalAgent APIs for an end-to-end voice agent experience

The documentation provided with the sample app walks through the code path of voice activation and agent management, from the prerequisites of startup through proper cleanup.

> [!div class="nextstepaction"]
> [Visit the GitHub repo for the UWP Sample](https://aka.ms/MVA/sample)

## Frequently asked questions

### How do I contact Microsoft for resources like Limited Access Feature tokens and keyword model files?

Contact winvoiceassistants@microsoft.com to request these resources.

### My app is showing in a small window when I activate it by voice. How can I transition from the compact view to a full application window?

When your application is first activated by voice, it is started in a compact view. Please read the [Design guidance for voice activation preview](windows-voice-assistants-best-practices.md#design-guidance-for-voice-activation-preview) for guidance on the different views and transitions between them for voice assistants on Windows.

To make the transition from compact view to full app view, use the appView API `TryEnterViewModeAsync`:

`var appView = ApplicationView.GetForCurrentView();
 await appView.TryEnterViewModeAsync(ApplicationViewMode.Default);`

### Why are voice assistant features on Windows only enabled for UWP applications?

Given the privacy risks associated with features like voice activation, the features of the UWP platform are necessary allow the voice assistant features on Windows to be sufficiently secure.

### The UWP Voice Assistant Sample uses Direct Line Speech. Do I have to use Direct Line Speech for my voice assistant on Windows?

The UWP Sample Application was developed using Direct Line Speech and the Speech Services SDK as a demonstration of how to use a dialog service with the Windows Conversational Agent capability. However, you can use any service for local and cloud keyword verification, speech-to-text conversion, bot dialog, and text-to-speech conversion.