---
title: Develop video player applications
description: The topic provides links to Player Frameworks and plugins that you can use to develop your own client applications that can consume streaming media from Media Services.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''

ms.assetid: 55e419fc-4c39-4902-9c62-f41cfcd86c6c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako

---
# Develop video player applications
## Overview
Azure Media Services provides the tools you need to create rich, dynamic client player applications for most platforms including: iOS Devices, Android Devices, Windows, Windows Phone, Xbox, and Set-top boxes. This topic also provides links to SDKs and Player Frameworks that you can use to develop your own client applications that can consume streaming media from Azure Media Services.

>[!NOTE]
>When your AMS account is created a **default** streaming endpoint is added to your account in the **Stopped** state. To start streaming your content and take advantage of dynamic packaging and dynamic encryption, the streaming endpoint from which you want to stream content has to be in the **Running** state. 
 
## Azure Media Player
[Azure Media Player](https://aka.ms/ampinfo) is a web video player built to play back media content from Microsoft Azure Media Services on a wide variety of browsers and devices. Azure Media Player utilizes industry standards, such as HTML5, Media Source Extensions (MSE), and Encrypted Media Extensions (EME) to provide an enriched adaptive streaming experience. When these standards are not available on a device or in a browser, Azure Media Player uses Flash and Silverlight as fallback technology. Regardless of the playback technology used, developers will have a unified JavaScript interface to access APIs. This allows for content served by Azure Media Services to be played across a wide-range of devices and browsers without any extra effort.

Microsoft Azure Media Services allows for content to be served up with DASH, Smooth Streaming, and HLS streaming formats to play back content. Azure Media Player takes into account these various formats and automatically plays the best link based on the platform/browser capabilities. Microsoft Azure Media Services also allows for dynamic encryption of assets with PlayReady encryption or AES-128 bit envelope encryption. Azure Media Player allows for decryption of PlayReady and AES-128 bit encrypted content when appropriately configured. 

For more information:

* [Azure Media Player](https://aka.ms/ampinfo)
* [Azure Media Player Documentation](https://aka.ms/ampdocs) 
* [Azure Media Player Getting Started Blog](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/)
* [Sign up to stay up to date with the latest from Azure Media Player](https://aka.ms/ampsignup)
* [Add new feature requests, ideas, feedback](https://aka.ms/ampuservoice) 

## Other Tools for Creating Player Applications
You can also use any of the following SDKs:

* [Smooth Streaming Client SDK](https://www.iis.net/downloads/microsoft/smooth-streaming) 
* [Smooth Streaming Windows Store App](media-services-build-smooth-streaming-apps.md)
* [Microsoft Media Platform: Player Framework](https://playerframework.codeplex.com/) 
* [HTML5 Player Framework Documentation](https://playerframework.codeplex.com/wikipage?title=HTML5%20Player&referringTitle=Documentation) 
* [Microsoft Smooth Streaming Plugin for OSMF](https://www.microsoft.com/download/details.aspx?id=36057) 
* [Licensing Microsoft® Smooth Streaming Client Porting Kit](https://aka.ms/sspk) 
* [XBOX Video Application Development](https://xbox.create.msdn.com/) 

## Advertising
Azure Media Services provides support for ad insertion through the Windows Media Platform: Player Frameworks. Player frameworks with ad support are available for Windows 8, Silverlight, Windows Phone 8, and iOS devices. Each player framework contains sample code that shows you how to implement a player application. There are three different kinds of ads you can insert into your media:

Linear – full frame ads that pause the main video

Nonlinear – overlay ads that are displayed as the main video is playing, usually a logo or other static image placed within the player

Companion – ads that are displayed outside of the player

Ads can be placed at any point in the main video’s time line. You must tell the player when to play the ad and which ads to play. This is done using a set of standard XML-based files: Video Ad Service Template (VAST), Digital Video Multiple Ad Playlist (VMAP), Media Abstract Sequencing Template (MAST), and Digital Video Player Ad Interface Definition (VPAID). VAST files specify what ads to display. VMAP files specify when to play various ads and contain VAST XML. MAST files are another way to sequence ads which also can contain VAST XML. VPAID files define an interface between the video player and the ad or ad server. For more information, see [Inserting Ads](https://msdn.microsoft.com/library/dn387398.aspx).

For information about closed captioning and ads support in Live streaming videos, see [Supported Closed Captioning and Ad Insertion Standards](https://msdn.microsoft.com/library/c49e0b4d-357e-4cca-95e5-2288924d1ff3#caption_ad).

## Media Services learning paths
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## Provide feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## See Also
[Embedding a MPEG-DASH Adaptive Streaming Video in an HTML5 Application with DASH.js](media-services-embed-mpeg-dash-in-html5.md)

[GitHub dash.js repository](https://github.com/Dash-Industry-Forum/dash.js)

