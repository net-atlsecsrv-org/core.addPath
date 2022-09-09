---
# Mandatory fields. See more on aka.ms/skyeye/meta.
title: Azure Media Services v3 release notes | Microsoft Docs
description: To stay up-to-date with the most recent developments, this article provides you with the latest updates on Azure Media Services v3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''

ms.service: media-services
ms.workload: na
ms.topic: article
ms.date: 10/21/2020
ms.author: inhenkel
---

# Azure Media Services v3 release notes

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

>Get notified about when to revisit this page for updates by copying and pasting this URL: `https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+v3+release+notes%22&locale=en-us` into your RSS feed reader.

To stay up-to-date with the most recent developments, this article provides you with information about:

* The latest releases
* Known issues
* Bug fixes
* Deprecated functionality

## Known issues

> [!NOTE]
> You can use the [Azure portal](https://portal.azure.com/) to manage v3 [live events](live-events-outputs-concept.md), view v3 [assets](assets-concept.md) and jobs, get info about accessing APIs, encrypt content. For all other management tasks (for example, manage transforms and jobs), use the [REST API](/rest/api/media/accountfilters), [CLI](/cli/azure/ams), or one of the supported [SDKs](media-services-apis-overview.md#sdks).
>
> For details, see: [the Azure portal limitations for Media Services v3](frequently-asked-questions.md#what-are-the-azure-portal-limitations-for-media-services-v3).

## October 2020

### Basic Audio Analysis
The Audio Analysis preset now includes a Basic mode pricing tier. The new Basic Audio Analyzer mode provides a low-cost option to extract speech transcription, and format output captions and subtitles. This mode performs speech-to-text transcription and generation of a VTT subtitle/caption file. The output of this mode includes an Insights JSON file including only the keywords, transcription,and timing information. Automatic language detection and speaker diarization are not included in this mode. See the list of [supported languages.](analyzing-video-audio-files-concept.md#built-in-presets)

Customers using Indexer v1 and Indexer v2 should migrate to the Basic Audio Analysis preset.

For more information about the Basic Audio Analyzer mode, see [Analyzing Video and Audio files](analyzing-video-audio-files-concept.md).  To learn to use the Basic Audio Analyzer mode with the REST API, see [How to Create a Basic Audio Transform](how-to-create-basic-audio-transform.md).

## Live Events

Updates to most properties are now allowed when live events are stopped. In addition, users are allowed to specify a prefix for the static hostname for the live event's input and preview URLs. VanityUrl is now called `useStaticHostName` to better reflect the intent of the property.

Live events now have a StandBy state.  See [Live Events and Live Outputs in Media Services](https://docs.microsoft.com/azure/media-services/latest/live-events-outputs-concept).

A live event supports receiving various input aspect ratios. Stretch mode allows customers to specify the stretching behavior for the output.

Live encoding now adds the capability of outputting fixed key frame interval fragments between 0.5 to 20 seconds.

## Accounts

> [!WARNING]
> If you create a Media Services account with the 2020-05-01 API version it won’t work with RESTv2 

## August 2020

### Dynamic Encryption
Support for the legacy PlayReady Protected Interoperable File Format (PIFF 1.1) encryption is now available in the Dynamic Packager. This provides support for legacy Smart TV sets from Samsung and LG that implemented the early drafts of the Common Encryption standard (CENC) published by Microsoft.  The PIFF 1.1 format is also known as the encryption format that was previously supported by the Silverlight client library. Today, the only use case scenario for this encryption format is to target the legacy Smart TV market where there remains a non-trivial number of Smart TVs in some regions that only support Smooth Streaming with PIFF 1.1 encryption. 

To use the new PIFF 1.1 encryption support, change the encryption value to 'piff' in the URL path of the Streaming Locator. For more details, see the [Content Protection overview.](content-protection-overview.md)
For Example: `https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=piff)`|

> [!NOTE]
> PIFF 1.1 support is provided as a backwards compatible solution for Smart TV (Samsung, LG) that implemented the early "Silverlight" version of Common Encryption. It is recommended to only use the PIFF format where needed for support of legacy Samsung or LG Smart TVs shipped between 2009-2015 that supported the PIFF 1.1 version of PlayReady encryption. 

## July 2020

### Live transcriptions

Live Transcriptions now supports 19 languages and 8 regions.

### Protecting your content with Media Services and Azure AD

We published a tutorial called [End-to-End content protection using Azure AD](./azure-ad-content-protection.md).

### High availability

We published a High Availability with Media Services and Video on Demand (VOD) [overview](./media-services-high-availability-encoding.md) and [sample](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/HighAvailabilityEncodingStreaming).

## June 2020

### Live Video Analytics on IoT Edge preview release

The preview of Live Video Analytics on IoT Edge went public. For more information, see [release notes](../live-video-analytics-edge/release-notes.md).

Live Video Analytics on IoT Edge is an expansion to the Media Service family. It enables you to analyze live video with AI models of your choice on your own edge devices, and optionally capture and record that video. You can now build apps with real-time video analytics at the edge without worrying about the complexity of building and operating a live video pipeline.

## May 2020

Azure Media Services is now generally available in the following regions: "Germany North", "Germany West Central", "Switzerland North", and "Switzerland West". Customers can deploy Media Services to these regions using the Azure portal.

## April 2020

### Improvements in documentation

Azure Media Player docs were migrated to the [Azure documentation](../azure-media-player/azure-media-player-overview.md).

## January 2020

### Improvements in media processors

- Improved support for interlaced sources in Video Analysis – such content is now de-interlaced correctly before being sent to inference engines.
- When generating thumbnails with the “Best” mode, the encoder now searches beyond 30 seconds to select a frame that is not monochromatic.

### Azure Government cloud updates

Media Services GA’ed in the following Azure Government regions: *USGov Arizona* and *USGov Texas*.

## December 2019

Added CDN support for *Origin-Assist Prefetch* headers for both live and video on-demand streaming; available for customers who have direct contract with Akamai CDN. Origin-Assist CDN-Prefetch feature involves the following HTTP header exchanges between Akamai CDN and Azure Media Services origin:

|HTTP header|Values|Sender|Receiver|Purpose|
| ---- | ---- | ---- | ---- | ----- |
|CDN-Origin-Assist-Prefetch-Enabled | 1 (default) or 0 |CDN|Origin|To indicate CDN is prefetch enabled|
|CDN-Origin-Assist-Prefetch-Path| Example: <br/>Fragments(video=1400000000,format=mpd-time-cmaf)|Origin|CDN|To provide prefetch path to CDN|
|CDN-Origin-Assist-Prefetch-Request|1 (prefetch request) or 0 (regular request)|CDN|Origin|To indicate the request from CDN is a prefetch|

To see part of the header exchange in action, you can try the following steps:

1. Use Postman or curl to issue a request to Media Services origin for an audio or video segment or fragment. Make sure to add the header CDN-Origin-Assist-Prefetch-Enabled: 1 in the request.
2. In the response, you should see the header CDN-Origin-Assist-Prefetch-Path with a relative path as its value.

## November 2019

### Live transcription Preview

Live transcription is now in public preview and available for use in the West US 2 region.

Live transcription is designed to work in conjunction with live events as an add-on capability.  It is supported on both pass-through and Standard or Premium encoding live events.  When this feature is enabled, the service uses the [Speech-To-Text](../../cognitive-services/speech-service/speech-to-text.md) feature of Cognitive Services to transcribe the spoken words in the incoming audio into text. This text is then made available for delivery along with video and audio in MPEG-DASH and HLS protocols. Billing is based on a new add-on meter that is additional cost to the live event when it is in the "Running" state.  For details on Live transcription and billing, see [Live transcription](live-transcription.md)

> [!NOTE]
> Currently, live transcription is only available as a preview feature in the West US 2 region. It supports transcription of spoken words in English (en-us) only at this time.

### Content protection

The *Token Replay Prevention* feature released in limited regions back in September is now available in all regions.
 Media Services customers can now set a limit on the number of times the same token can be used to request a key or a license. For more information, see [Token Replay Prevention](content-protection-overview.md#token-replay-prevention).

### New recommended live encoder partners

Added support for the following new recommended partner encoders for RTMP live streaming:

- [Cambria Live 4.3](https://www.capellasystems.net/products/cambria-live/)
- [GoPro Hero7/8 and Max action cameras](https://gopro.com/help/articles/block/getting-started-with-live-streaming)
- [Restream.io](https://restream.io/)

### File Encoding enhancements

- A new Content Aware Encoding preset is now available. It produces a set of GOP-aligned MP4s by using content-aware encoding. Given any input content, the service performs an initial lightweight analysis of the input content. It uses those results to determine the optimal number of layers, appropriate bit rate, and resolution settings for delivery by adaptive streaming. This preset is particularly effective for low-complexity and medium-complexity videos, where the output files are at lower bit rates but at a quality that still delivers a good experience to viewers. The output will contain MP4 files with video and audio interleaved. For more information, see the [open API specs](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json).
- Improved performance and multi-threading for the resizer in Standard Encoder. Under specific conditions, customer should see a performance boost between 5-40% VOD encoding. Low complexity content encoded into multiple bit-rates will see the highest performance increases. 
- Standard encoding now maintains a regular GOP cadence for variable frame rate  (VFR) contents during VOD encoding when using the time-based GOP setting.  This means that customer submitting mixed frame rate content that varies between 15-30 fps, for example,  should now see regular GOP distances calculated on output to adaptive bitrate streaming MP4 files. This will improve the ability to switch seamlessly between tracks when delivering over HLS or DASH. 
-  Improved AV sync for variable frame rate (VFR) source content

### Video Indexer, Video analytics

- Keyframes extracted using the VideoAnalyzer preset are now in the original resolution of the video instead of being resized. High-resolution keyframe extraction gives you original quality images and allows you to make use of the image-based artificial intelligence models provided by the Microsoft Computer Vision and Custom Vision services to gain even more insights from your video.

## September 2019

###  Media Services v3  

#### Live linear encoding of live events

Media Services v3 is announcing the preview of 24 hrs x 365 days of live linear encoding of live events.

###  Media Services v2  

#### Deprecation of media processors

We are announcing deprecation of *Azure Media Indexer* and *Azure Media Indexer 2 Preview*. For the retirement dates, see the  [legacy components](../previous/legacy-components.md) article. [Azure Media Services Video Indexer](../video-indexer/index.yml) replaces these legacy media processors.

For more information, see [Migrate from Azure Media Indexer and Azure Media Indexer 2 to Azure Media Services Video Indexer](../previous/migrate-indexer-v1-v2.md).

## August 2019

###  Media Services v3  

#### South Africa regional pair is open for Media Services 

Media Services is now available in South Africa North and South Africa West regions.

For more information, see [Clouds and regions in which Media Services v3 exists](azure-clouds-regions.md).

###  Media Services v2  

#### Deprecation of media processors

We are announcing deprecation of the *Windows Azure Media Encoder* (WAME) and *Azure Media Encoder* (AME) media processors, which are being retired. For the retirement dates, see this [legacy components](../previous/legacy-components.md) article.

For details, see [Migrate WAME to Media Encoder Standard](../previous/migrate-windows-azure-media-encoder.md) and [Migrate AME to Media Encoder Standard](../previous/migrate-azure-media-encoder.md).
 
## July 2019

### Content protection

When streaming content protected with token restriction, end users need to obtain a token that is sent as part of the key delivery request. The *Token Replay Prevention* feature allows Media Services customers to set a limit on how many times the same token can be used to request a key or a license. For more information, see [Token Replay Prevention](content-protection-overview.md#token-replay-prevention).

As of July, the preview feature was only available in US Central and US West Central.

## June 2019

### Video subclipping

You can now trim or subclip a video when encoding it using a [Job](/rest/api/media/jobs). 

This functionality works with any [Transform](/rest/api/media/transforms) that is built using either the [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) presets, or the [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset) presets. 

See examples:

* [Subclip a video with .NET](subclip-video-dotnet-howto.md)
* [Subclip a video with REST](subclip-video-rest-howto.md)

## May 2019

### Azure Monitor support for Media Services diagnostic logs and metrics

You can now use Azure Monitor to view telemetry data emitted by Media Services.

* Use the Azure Monitor diagnostic logs to monitor requests sent by the Media Services Key Delivery endpoint. 
* Monitor metrics emitted by Media Services [Streaming Endpoints](streaming-endpoint-concept.md).   

For details, see [Monitor Media Services metrics and diagnostic logs](media-services-metrics-diagnostic-logs.md).

### Multi audio tracks support in Dynamic Packaging 

When streaming Assets that have multiple audio tracks with multiple codecs and languages, [Dynamic Packaging](dynamic-packaging-overview.md) now supports multi audio tracks for the HLS output (version 4 or above).

### Korea regional pair is open for Media Services 

Media Services is now available in Korea Central and Korea South regions. 

For more information, see [Clouds and regions in which Media Services v3 exists](azure-clouds-regions.md).

### Performance improvements

Added updates that include Media Services performance improvements.

* The maximum file size supported for processing was updated. See, [Quotas, and limits](limits-quotas-constraints.md).
* [Encoding speeds improvements](concept-media-reserved-units.md).

## April 2019

### New presets

* [FaceDetectorPreset](/rest/api/media/transforms/createorupdate#facedetectorpreset) was added to the built-in analyzer presets.
* [ContentAwareEncodingExperimental](/rest/api/media/transforms/createorupdate#encodernamedpreset) was added to the built-in encoder presets. For more information, see [Content-aware encoding](content-aware-encoding.md). 

## March 2019

Dynamic Packaging now supports Dolby Atmos. For more information, see [Audio codecs supported by dynamic packaging](dynamic-packaging-overview.md#audio-codecs-supported-by-dynamic-packaging).

You can now specify a list of asset or account filters, which would apply to your Streaming Locator. For more information, see [Associate filters with Streaming Locator](filters-concept.md#associating-filters-with-streaming-locator).

## February 2019

Media Services v3 is now supported in Azure national clouds. Not all features are available in all clouds yet. For details, see [Clouds and regions in which Azure Media Services v3 exists](azure-clouds-regions.md).

[Microsoft.Media.JobOutputProgress](media-services-event-schemas.md#monitoring-job-output-progress) event was added to the Azure Event Grid schemas for Media Services.

## January 2019

### Media Encoder Standard and MPI files 

When encoding with Media Encoder Standard to produce MP4 file(s), a new .mpi file is generated and added to the output Asset. This MPI file is intended to improve performance for [dynamic packaging](dynamic-packaging-overview.md) and streaming scenarios.

You should not modify or remove the MPI file, or take any dependency in your service on the existence (or not) of such a file.

## December 2018

Updates from the GA release of the V3 API include:
       
* The **PresentationTimeRange** properties are no longer 'required' for **Asset Filters** and **Account Filters**. 
* The $top and $skip query options for **Jobs** and **Transforms** have been removed and $orderby was added. As part of adding the new ordering functionality, it was discovered that the $top and $skip options had accidentally been exposed previously even though they are not implemented.
* Enumeration extensibility was re-enabled. This feature was enabled in the preview versions of the SDK and got accidentally disabled in the GA version.
* Two predefined streaming policies have been renamed. **SecureStreaming** is now **MultiDrmCencStreaming**. **SecureStreamingWithFairPlay** is now **Predefined_MultiDrmStreaming**.

## November 2018

The CLI 2.0 module is now available for [Azure Media Services v3 GA](/cli/azure/ams?view=azure-cli-latest&preserve-view=true) – v 2.0.50.

### New commands

- [az ams account](/cli/azure/ams/account?view=azure-cli-latest&preserve-view=true)
- [az ams account-filter](/cli/azure/ams/account-filter?view=azure-cli-latest&preserve-view=true)
- [az ams asset](/cli/azure/ams/asset?view=azure-cli-latest&preserve-view=true)
- [az ams asset-filter](/cli/azure/ams/asset-filter?view=azure-cli-latest&preserve-view=true)
- [az ams content-key-policy](/cli/azure/ams/content-key-policy?view=azure-cli-latest&preserve-view=true)
- [az ams job](/cli/azure/ams/job?view=azure-cli-latest&preserve-view=true)
- [az ams live-event](/cli/azure/ams/live-event?view=azure-cli-latest&preserve-view=true)
- [az ams live-output](/cli/azure/ams/live-output?view=azure-cli-latest&preserve-view=true)
- [az ams streaming-endpoint](/cli/azure/ams/streaming-endpoint?view=azure-cli-latest&preserve-view=true)
- [az ams streaming-locator](/cli/azure/ams/streaming-locator?view=azure-cli-latest&preserve-view=true)
- [az ams account mru](/cli/azure/ams/account/mru?view=azure-cli-latest&preserve-view=true) - enables you to manage Media Reserved Units. For more information, see [Scale Media Reserved Units](media-reserved-units-cli-how-to.md).

### New features and breaking changes

#### Asset commands

- ```--storage-account``` and ```--container``` arguments added.
- Default values for expiry time (Now+23h) and permissions (Read) in ```az ams asset get-sas-url``` command added.

#### Job commands

- ```--correlation-data``` and ```--label``` arguments added
- ```--output-asset-names``` renamed to ```--output-assets```. Now it accepts a space-separated list of assets in 'assetName=label' format. An asset without label can be sent like this: 'assetName='.

#### Streaming Locator commands

- ```az ams streaming locator``` base command replaced with ```az ams streaming-locator```.
- ```--streaming-locator-id``` and ```--alternative-media-id support``` arguments added.
- ```--content-keys argument``` argument updated.
- ```--content-policy-name``` renamed to ```--content-key-policy-name```.

#### Streaming Policy commands

- ```az ams streaming policy``` base command replaced with ```az ams streaming-policy```.
- Encryption parameters support in ```az ams streaming-policy create``` added.

#### Transform commands

- ```--preset-names``` argument replaced with ```--preset```. Now you can only set 1 output/preset at a time (to add more you have to run ```az ams transform output add```). Also, you can set custom StandardEncoderPreset by passing the path to your custom JSON.
- ```az ams transform output remove``` can be performed by passing the output index to remove.
- ```--relative-priority, --on-error, --audio-language and --insights-to-extract``` arguments added in ```az ams transform create``` and ```az ams transform output add``` commands.

## October 2018 - GA

This section describes Azure Media Services (AMS) October updates.

### REST v3 GA release

The [REST v3 GA release](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01) includes more APIs for Live, Account/Asset level manifest filters, and DRM support.

#### Azure Resource Management 

Support for Azure Resource Management enables unified management and operations API (now everything in one place).

Starting with this release, you can use Resource Manager templates to create Live Events.

#### Improvement of Asset operations 

The following improvements were introduced:

- Ingest from HTTP(s) URLs or Azure Blob Storage SAS URLs.
- Specify you own container names for Assets. 
- Easier output support to create custom workflows with Azure Functions.

#### New Transform object

The new **Transform** object simplifies the Encoding model. The new object makes it easy to create and share encoding Resource Manager templates and presets. 

#### Azure Active Directory authentication and Azure RBAC

Azure AD Authentication and Azure role-based access control (Azure RBAC) enable secure Transforms, LiveEvents, Content Key Policies, or Assets by Role or Users in Azure AD.

#### Client SDKs  

Languages supported in Media Services v3: .NET Core, Java, Node.js, Ruby, Typescript, Python, Go.

#### Live encoding updates

The following live encoding updates are introduced:

- New low latency mode for live (10 seconds end-to-end).
- Improved RTMP support (increased stability and more source encoder support).
- RTMPS secure ingest.

    When you create a Live Event, you now get 4 ingest URLs. The 4 ingest URLs are almost identical, have the same streaming token (AppId), only the port number part is different. Two of the URLs are primary and backup for RTMPS. 
- 24-hour transcoding support. 
- Improved ad-signaling support in RTMP via SCTE35.

#### Improved Event Grid support

You can see the following Event Grid support improvements:

- Azure Event Grid integration for easier development with Logic Apps and Azure Functions. 
- Subscribe for events on Encoding, Live Channels, and more.

### CMAF support

CMAF and 'cbcs' encryption support for Apple HLS (iOS 11+) and MPEG-DASH players that support CMAF.

### Video Indexer

Video Indexer GA release was announced in August. For new information about currently supported features, see [What is Video Indexer](../video-indexer/video-indexer-overview.md?bc=/azure/media-services/video-indexer/breadcrumb/toc.json&toc=/azure/media-services/video-indexer/toc.json). 

### Plans for changes

#### Azure CLI 2.0
 
The Azure CLI 2.0 module that includes operations on all features (including Live, Content Key Policies, Account/Asset Filters, Streaming Policies) is coming soon. 

### Known issues

Only customers that used the preview API for Asset or AccountFilters are impacted by the following issue.

If you created Assets or Account Filters between 09/28 and 10/12 with Media Services v3 CLI or APIs, you need to remove all Asset and AccountFilters and re-create them due to a version conflict. 

## May 2018 - Preview

### .NET SDK

The following features are present in the .NET SDK:

* **Transforms** and **Jobs** to encode or analyze media content. For examples, see [Stream files](stream-files-tutorial-with-api.md) and [Analyze](analyze-videos-tutorial-with-api.md).
* **Streaming Locators** for publishing and streaming content to end-user devices
* **Streaming Policies** and **Content Key Policies** to configure key delivery and content protection (DRM) when delivering content.
* **Live Events** and **Live Outputs** to configure the ingest and archiving of live streaming content.
* **Assets** to store and publish media content in Azure Storage. 
* **Streaming Endpoints** to configure and scale dynamic packaging, encryption, and streaming for both live and on-demand media content.

### Known issues

* When submitting a job, you can specify to ingest your source video using HTTPS URLs, SAS URLs, or paths to files located in Azure Blob storage. Currently, Media Services v3 does not support chunked transfer encoding over HTTPS URLs.

## Ask questions, give feedback, get updates

Check out the [Azure Media Services community](media-services-community.md) article to see different ways you can ask questions, give feedback, and get updates about Media Services.

## See also

[Migration guidance for moving from Media Services v2 to v3](migrate-from-v2-to-v3.md#known-issues).

## Next steps

- [Overview](media-services-overview.md)
- [Media Services v3 Documentation updates](docs-release-notes.md)
- [Media Services v2 release notes](../previous/media-services-release-notes.md)
