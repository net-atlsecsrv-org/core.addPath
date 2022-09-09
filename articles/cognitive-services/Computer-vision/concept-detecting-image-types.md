---
title: Image type detection - Computer Vision
titleSuffix: Azure Cognitive Services
description: Concepts related to the image type detection feature of the Computer Vision API.
services: cognitive-services
author: PatrickFarley
manager: nitinme

ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: pafarley
ms.custom: seodec18
---

# Detecting image types with Computer Vision

With the [Analyze Image](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API, Computer Vision can analyze the content type of images, indicating whether an image is clip art or a line drawing.

## Detecting clip art

Computer Vision analyzes an image and rates the likelihood of the image being clip art on a scale of 0 to 3, as described in the following table.

| Value | Meaning |
|-------|---------|
| 0 | Non-clip-art |
| 1 | Ambiguous |
| 2 | Normal-clip-art |
| 3 | Good-clip-art |

### Clip art detection examples

The following JSON responses illustrates what Computer Vision returns when rating the likelihood of the example images being clip art.

![A clip art image of a slice of cheese](./Images/cheese_clipart.png)

```json
{
    "imageType": {
        "clipArtType": 3,
        "lineDrawingType": 0
    },
    "requestId": "88c48d8c-80f3-449f-878f-6947f3b35a27",
    "metadata": {
        "height": 225,
        "width": 300,
        "format": "Jpeg"
    }
}
```

![A blue house and the front yard](./Images/house_yard.png)

```json
{
    "imageType": {
        "clipArtType": 0,
        "lineDrawingType": 0
    },
    "requestId": "a9c8490a-2740-4e04-923b-e8f4830d0e47",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## Detecting line drawings

Computer Vision analyzes an image and returns a boolean value indicating whether the image is a line drawing.

### Line drawing detection examples

The following JSON responses illustrates what Computer Vision returns when indicating whether the example images are line drawings.

![A line drawing image of a lion](./Images/lion_drawing.png)

```json
{
    "imageType": {
        "clipArtType": 2,
        "lineDrawingType": 1
    },
    "requestId": "6442dc22-476a-41c4-aa3d-9ceb15172f01",
    "metadata": {
        "height": 268,
        "width": 300,
        "format": "Jpeg"
    }
}
```

![A white flower with a green background](./Images/flower.png)

```json
{
    "imageType": {
        "clipArtType": 0,
        "lineDrawingType": 0
    },
    "requestId": "98437d65-1b05-4ab7-b439-7098b5dfdcbf",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## Next steps

See the [Analyze Image](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) reference documentation to learn how to detect image types.
