---
title: What's new in Computer Vision?
titleSuffix: Azure Cognitive Services
description: This article contains news about Computer Vision.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 01/13/2021
ms.author: pafarley
---

# What's new in Computer Vision

Learn what's new in the service. These items may be release notes, videos, blog posts, and other types of information. Bookmark this page to stay up to date with the service.

## March 2021

### Computer Vision 3.2 Public Preview update

The Computer Vision API v3.2 public preview has been updated. The preview release has all Computer Vision features along with updated Read and Analyze APIs.

> [!div class="nextstepaction"]
> [See Computer Vision v3.2 public preview 3](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005)

## February 2021

### Read API v3.2 Public Preview with OCR support for 73 languages
Computer Vision's Read API v3.2 public preview, available as cloud service and Docker container, includes these updates:
* [OCR for 73 languages](./language-support.md#optical-character-recognition-ocr) including Simplified and Traditional Chinese, Japanese, Korean, and Latin languages.
* Natural reading order for the text line output (Latin languages only)
* Handwriting style classification for text lines along with a confidence score (Latin languages only).
* Extract text only for selected pages for a multi-page document.
* Available as a [Distroless container](./computer-vision-how-to-install-containers.md?tabs=version-3-2) for on-premise deployment.

[Learn more](concept-recognizing-text.md) about the Read API.

> [!div class="nextstepaction"]
> [Use the Read API v3.2 Public Preview](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005)


## January 2021

### Spatial analysis container update

A new version of the [spatial analysis container](spatial-analysis-container.md) has been released with a new feature set. This Docker container lets you analyze real-time streaming video to understand spatial relationships between people and their movement through physical environments. 

* [Spatial analysis operations](spatial-analysis-operations.md) can be now configured to detect if a person is wearing a protective face covering such as a mask. 
    * A mask classifier can be enabled for the `personcount`, `personcrossingline` and `personcrossingpolygon` operations by configuring the `ENABLE_FACE_MASK_CLASSIFIER` parameter.
    * The attributes `face_mask` and `face_noMask` will be returned as metadata with confidence score for each person detected in the video stream
* The *personcrossingpolygon* operation has been extended to allow the calculation of the dwell time a person spends in a zone. You can set the `type` parameter in the Zone configuration for the operation to `zonedwelltime` and a new event of type *personZoneDwellTimeEvent* will include the `durationMs` field populated with the number of milliseconds that the person spent in the zone.
* **Breaking change**: The *personZoneEvent* event has been renamed to *personZoneEnterExitEvent*. This event is raised by the *personcrossingpolygon* operation when a person enters or exits the zone and provides directional info with the numbered side of the zone that was crossed.
* Video URL can be provided as "Private Parameter/obfuscated" in all operations. Obfuscation is optional now and it will only work if `KEY` and `IV` are provided as environment variables.
* Calibration is enabled by default for all operations. Set the `do_calibration: false` to disable it.
* Added support for auto recalibration (by default disabled) via the `enable_recalibration` parameter, please refer to [Spatial analysis operations](./spatial-analysis-operations.md) for details
* Camera calibration parameters to the `DETECTOR_NODE_CONFIG`. Refer to [Spatial analysis operations](./spatial-analysis-operations.md) for details.


## October 2020

### Computer Vision API v3.1 GA

The Computer Vision API in General Availability has been upgraded to v3.1.

## September 2020

### Spatial analysis container preview

The [spatial analysis container](spatial-analysis-container.md) is now in preview. The spatial analysis feature of Computer Vision lets you to analyze real-time streaming video to understand spatial relationships between people and their movement through physical environments. Spatial analysis is a Docker container you can use on-premises. 

### Read API v3.1 Public Preview adds OCR for Japanese
Computer Vision's Read API v3.1 public preview adds these capabilities:
* OCR for Japanese language
* For each text line, indicate whether the appearance is Handwriting or Print style, along with a confidence score (Latin languages only).
* For a multi-page document extract text only for selected pages or page range.

* This preview version of the Read API supports English, Dutch, French, German, Italian, Japanese, Portuguese, Simplified Chinese, and Spanish languages.

See the [Read API overview](concept-recognizing-text.md) to learn more.

> [!div class="nextstepaction"]
> [Learn more about Read API v3.1 Public Preview 2](https://westus2.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-2/operations/5d986960601faab4bf452005)

## July 2020

### Read API v3.1 Public Preview with OCR for Simplified Chinese
Computer Vision's Read API v3.1 public preview adds support for Simplified Chinese.

* This preview version of the Read API supports English, Dutch, French, German, Italian, Portuguese, Simplified Chinese, and Spanish languages.

See the [Read API overview](concept-recognizing-text.md) to learn more.

> [!div class="nextstepaction"]
> [Learn more about Read API v3.1 Public Preview 1](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-1/operations/5d986960601faab4bf452005)

## May 2020
Computer Vision API v3.0 entered General Availability, with updates to [Read API](concept-recognizing-text.md):

* Support for English, Dutch, French, German, Italian, Portuguese, and Spanish
* Improved accuracy
* Confidence score for each extracted word
* New output format

## March 2020

* TLS 1.2 is now enforced for all HTTP requests to this service. For more information, see [Azure Cognitive Services security](../cognitive-services-security.md).

## January 2020

### Read API 3.0 Public Preview

You now have the option to use version 3.0 of the Read API to extract printed or handwritten text from images. Compared to earlier versions, 3.0 provides:
* Improved accuracy
* New output format
* Confidence score for each extracted word
* Support for both Spanish and English languages with the additional language parameter

Follow an [Extract text quickstart](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/REST/CSharp-hand-text.md?tabs=version-3) to get starting using the 3.0 API.

## Cognitive Service updates

[Azure update announcements for Cognitive Services](https://azure.microsoft.com/updates/?product=cognitive-services)