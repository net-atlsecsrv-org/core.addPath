---
title: "Quickstart: Generate a thumbnail - REST, Python"
titleSuffix: "Azure Cognitive Services"
description: In this quickstart, you generate a thumbnail from an image using the Computer Vision API with Python.
services: cognitive-services
author: PatrickFarley
manager: nitinme

ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 02/21/2019
ms.author: pafarley
ms.custom: seodec18
---
# Quickstart: Generate a thumbnail using the REST API and Python in Computer Vision

In this quickstart, you will generate a thumbnail from an image using Computer Vision's REST API. With the [Get Thumbnail](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) method, you can specify the desired height and width, and Computer Vision uses smart cropping to intelligently identify the area of interest and generate cropping coordinates based on that region.

If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/try/cognitive-services/) before you begin.

## Prerequisites

- You must have a subscription key for Computer Vision. You can get a free trial key from [Try Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Or, follow the instructions in [Create a Cognitive Services account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) to subscribe to Computer Vision and get your key.
- A code editor such as [Visual Studio Code](https://code.visualstudio.com/download)

## Create and run the sample

To create and run the sample, copy the following code into the code editor. 

```python
import requests
# If you are using a Jupyter notebook, uncomment the following line.
# %matplotlib inline
import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

# Replace <Subscription Key> with your valid subscription key.
subscription_key = "<Subscription Key>"
assert subscription_key

# You must use the same region in your REST call as you used to get your
# subscription keys. For example, if you got your subscription keys from
# westus, replace "westcentralus" in the URI below with "westus".
#
# Free trial subscription keys are generated in the "westus" region.
# If you use a free trial subscription key, you shouldn't need to change
# this region.
vision_base_url = "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/"

thumbnail_url = vision_base_url + "generateThumbnail"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg"

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params = {'width': '50', 'height': '50', 'smartCropping': 'true'}
data = {'url': image_url}
response = requests.post(thumbnail_url, headers=headers,
                         params=params, json=data)
response.raise_for_status()

thumbnail = Image.open(BytesIO(response.content))

# Display the thumbnail.
plt.imshow(thumbnail)
plt.axis("off")

# Verify the thumbnail size.
print("Thumbnail is {0}-by-{1}".format(*thumbnail.size))
```

Next, do the following:
1. Replace the value of `subscription_key` with your subscription key.
1. Replace the value of `vision_base_url` with the endpoint URL for the Computer Vision resource in the Azure region where you obtained your subscription keys, if necessary.
1. Optionally, replace the value of `image_url` with the URL of a different image for which you want to generate a thumbnail.
1. Save the code as a file with an `.py` extension. For example, `get-thumbnail.py`.
1. Open a command prompt window.
1. At the prompt, use the `python` command to run the sample. For example, `python get-thumbnail.py`.

## Examine the response

A successful response is returned as binary data which represents the image data for the thumbnail. The sample should display this image. If the request fails, the response is displayed in the command prompt window and should contain an error code.

## Run in Jupyter (optional)

You can optionally run this quickstart in a step-by step fashion using a Jupyter notebook on [MyBinder](https://mybinder.org). To launch Binder, select the following button:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

## Next steps

Next, learn more detailed information about the thumbnail generation feature.

> [!div class="nextstepaction"]
> [Generating thumbnails](../concept-generating-thumbnails.md)
