---
title: Azure Communication Services calling client library overview
titleSuffix: An Azure Communication Services concept document
description: Provides an overview of the calling client library.
author: mikben
manager: jken
services: azure-communication-services

ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
---
# Calling client library overview

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

There are two separate families of Calling client libraries, for *clients* and *services.* Currently available client libraries are intended for end-user experiences: websites and native apps.

The Service client libraries are not yet available, and provide access to the raw voice and video data planes, suitable for integration with bots and other services.

## Calling client library capabilities

The following list presents the set of features which are currently available in the Azure Communication Services Calling client libraries.

| Group of features | Capability                                                                                                          | JS  | Java (Android) | Objective-C (iOS) 
| ----------------- | ------------------------------------------------------------------------------------------------------------------- | ---  | -------------- | -------------
| Core Capabilities | Place a one-to-one call between two users                                                                           | ✔️   | ✔️            | ✔️  
|                   | Place a group call with more than two users (up to 350 users)                                                       | ✔️   | ✔️            | ✔️ 
|                   | Promote a one-to-one call with two users into a group call with more than two users                                 | ✔️   | ✔️            | ✔️ 
|                   | Join a group call after it has started                                                                              | ✔️   | ✔️            | ✔️ 
|                   | Invite another VoIP participant to join an ongoing group call                                                       | ✔️   | ✔️            | ✔️
|                   | Turn your video on/off                                                         | ✔️   | ✔️            | ✔️ 
|                   | Mute/Unmute mic                                                                                                     | ✔️   | ✔️            | ✔️         
|                   | Switch between cameras                                                                                              | ✔️   | ✔️            | ✔️           
|                   | Local hold/un-hold                                                                                                  | ✔️   | ✔️            | ✔️           
|                   | Active speaker                                                                                                      | ✔️   | ✔️            | ✔️           
|                   | Choose speaker for calls                                                                                            | ✔️   | ✔️            | ✔️           
|                   | Choose microphone for calls                                                                                         | ✔️   | ✔️            | ✔️           
|                   | Show state of a participant<br/>*Idle, Early media, Connecting, Connected, On hold, In Lobby, Disconnected*         | ✔️   | ✔️            | ✔️           
|                   | Show state of a call<br/>*Early Media, Incoming, Connecting, Ringing, Connected, Hold, Disconnecting, Disconnected* | ✔️   | ✔️            | ✔️           
|                   | Show if a participant is muted                                                                                      | ✔️   | ✔️            | ✔️           
|                   | Show the reason why a participant left a call                                                                       | ✔️   | ✔️            | ✔️     
| Screen sharing    | Share the entire screen from within the application                                                                 | ✔️   | ❌            | ❌           
|                   | Share a specific application (from the list of running applications)                                                | ✔️   | ❌            | ❌           
|                   | Share a web browser tab from the list of open tabs                                                                  | ✔️   | ❌            | ❌           
|                   | Participant can view remote screen share                                                                            | ✔️   | ✔️            | ✔️         
| Roster            | List participants                                                                                                   | ✔️   | ✔️            | ✔️           
|                   | Remove a participant                                                                                                | ✔️   | ✔️            | ✔️         
| PSTN              | Place a one-to-one call with a PSTN participant                                                                     | ✔️   | ✔️            | ✔️   
|                   | Place a group call with PSTN participants                                                                           | ✔️   | ✔️            | ✔️
|                   | Promote a one-to-one call with a PSTN participant into a group call                                                 | ✔️   | ✔️            | ✔️
|                   | Dial-out from a group call as a PSTN participant                                                                    | ✔️   | ✔️            | ✔️   
| General           | Test your mic, speaker, and camera with an audio testing service (available by calling 8:echo123)                   |  ✔️  | ✔️            | ✔️   

## Calling client library browser support

The following table represents the set of supported browsers and versions which are currently available.

|                                  | Windows          | macOS          | Ubuntu | Linux  | Android | iOS    |
| -------------------------------- | ---------------- | -------------- | ------- | ------ | ------ | ------ |
| **Calling client library** | Chrome*, new Edge | Chrome*, Safari** | Chrome*  | Chrome* | Chrome* | Safari** |


*Note that the latest version of Chrome is supported in addition to the previous two releases.<br/>

**Note that Safari versions 13.1+ are supported. Outgoing video for Safari macOS is not yet supported, but it is supported on iOS. Outgoing screen sharing is only supported on desktop iOS. 1:1 and group calls currently are not available on Safari.

## Calling client - browser security model

### User WebRTC over HTTPS

WebRTC APIs like `getUserMedia` require that the app that calls these APIs is served over HTTPS.

For local development, you can use `http://localhost`.

### Embed the Communication Services Calling SDK in an iframe

A new [permissions policy (also called a feature policy)](https://www.w3.org/TR/permissions-policy-1/#iframe-allow-attribute) is being adopted by various browsers. This policy affects calling scenarios by controlling how applications can access a device's camera and microphone through a cross-origin iframe element.

If you want to use an iframe to host part of the app from a different domain, you must add the `allow` attribute with the correct value to your iframe.

For example, this iframe allows both camera and microphone access:

```html
<iframe allow="camera *; microphone *">
```

## Calling client library streaming support
The Communication Services calling client library supports the following streaming configurations:

|           |Web | Android/iOS|
|-----------|----|------------|
|# of outgoing streams that can be sent simultaneously |1 video + 1 screen sharing | 1 video + 1 screen sharing|
|# of incoming streams that can be rendered simultaneously |1 video + 1 screen sharing| 6 video + 1 screen sharing |


## Next steps

> [!div class="nextstepaction"]
> [Get started with calling](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

For more information, see the following articles:
- Familiarize yourself with general [call flows](../call-flows.md)
- Learn about [call types](../voice-video-calling/about-call-types.md)
- [Plan your PSTN solution](../telephony-sms/plan-solution.md)
