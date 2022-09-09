---
title: Getting started with Teams interop on Azure Communication Services
titleSuffix: An Azure Communication Services quickstart
description: In this quickstart, you'll learn how to join a Teams meeting with the Azure Communication Chat client library
author: askaur
ms.author: askaur
ms.date: 12/08/2020
ms.topic: quickstart
ms.service: azure-communication-services

---

# Quickstart: Join your chat app to a Teams meeting

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Get started with Azure Communication Services by connecting your chat solution to Microsoft Teams using the JavaScript client library. 

## Prerequisites 

1. A [Teams deployment](https://docs.microsoft.com/deployoffice/teams-install). 
2. A working [chat app](./get-started.md). 

## Enable Teams interoperability 

A Communication Services user that joins a Teams meeting as a guest user can access the meeting's chat only when they've joined the Teams meeting call. See the [Teams interop](../voice-video-calling/get-started-teams-interop.md) documentation to learn how to add a Communication Services user to a Teams meeting call.

The Teams interoperability feature is currently in private preview. To enable this feature for your Communication Services resource, please email acsfeedback@microsoft.com with: 
1. The Subscription ID of the Azure subscription that contains your Communication Services resource. 
2. Your Teams tenant ID. The easiest way to obtain this is to obtain and share a link to the Team. 

You must be a member of the owning organization of both entities to use this feature. 

[!INCLUDE [Join Teams meetings](./includes/meeting-interop-javascript.md)]

## Clean up resources

If you want to clean up and remove a Communication Services subscription, you can delete the resource or resource group. Deleting the resource group also deletes any other resources associated with it. Learn more about [cleaning up resources](../create-communication-resource.md#clean-up-resources).

## Next steps

For more information, see the following articles:

- Check out our [chat hero sample](../../samples/chat-hero-sample.md)
- Learn more about [how chat works](../../concepts/chat/concepts.md)
