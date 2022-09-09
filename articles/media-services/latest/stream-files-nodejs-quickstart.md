---
title: Stream video files with Azure Media Services - Node.js | Microsoft Docs
description: Follow the steps of this tutorial to create a new Azure Media Services account, encode a file, and stream it to Azure Media Player.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
keywords: azure media services, stream

ms.service: media-services
ms.workload: media
ms.topic: tutorial
ms.custom: mvc, devx-track-javascript
ms.date: 08/19/2019
ms.author: juliako
#Customer intent: As a developer, I want to create a Media Services account so that I can store, encrypt, encode, manage, and stream media content in Azure.
---

# Tutorial: Encode a remote file based on URL and stream the video - Node.js

This tutorial shows you how easy it is to encode and start streaming videos on a wide variety of browsers and devices using Azure Media Services. An input content can be specified using HTTPS URLs, SAS URLs, or paths to files located in Azure Blob storage.

The sample in this article encodes content that you make accessible via an HTTPS URL. Note that currently, AMS v3 does not support chunked transfer encoding over HTTPS URLs.

By the end of the tutorial you will be able to stream a video.  

![Play the video](./media/stream-files-nodejs-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## Prerequisites

- Install [Node.js](https://nodejs.org/en/download/)
- [Create a Media Services account](./create-account-howto.md).<br/>Make sure to remember the values that you used for the resource group name and Media Services account name.
- Follow the steps in [Access Azure Media Services API with the Azure CLI](./access-api-howto.md) and save the credentials. You will need to use them to access the API.

## Download and configure the sample

Clone a GitHub repository that contains the streaming Node.js sample to your machine using the following command:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
 ```

The sample is located in the [StreamFilesSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/master/AMSv3Samples/StreamFilesSample) folder.

Open [index.js](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.js#L25) in you downloaded project. Replace the `endpoint config` values with credentials that you got from [accessing APIs](./access-api-howto.md).

The sample performs the following actions:

1. Creates a **Transform** (first, checks if the specified Transform exists). 
2. Creates an output **Asset** that is used as the encoding **Job**'s output.
3. Creates the **Job**'s input that is based on an HTTPS URL.
4. Submits the encoding **Job** using the input and output that was created earlier.
5. Checks the Job's status.
6. Creates a **Streaming Locator**.
7. Builds streaming URLs.

## Run the sample app

1. The app downloads encoded files. Create a folder where you want for the output files to go and update the value of the **outputFolder** variable in the [index.js](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.js#L39) file.
1. Open **command prompt**, browse to the sample's directory, and execute the following commands.

    ```
    npm install 
    node index.js
    ```

After it is done running, you should see similar output:

![Run](./media/stream-files-nodejs-quickstart/run.png)

## Test with Azure Media Player

To test the stream, this article uses Azure Media Player. 

> [!NOTE]
> If a player is hosted on an https site, make sure to update the URL to "https".

1. Open a web browser and navigate to [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. In the **URL:** box, paste one of the streaming URL values you got when you ran the application. 
 
     You can paste the URL in HLS, Dash, or Smooth format and Azure Media Player will switch to an appropriate streaming protocol for playback on your device automatically.
3. Press **Update Player**.

Azure Media Player can be used for testing but should not be used in a production environment. 

## Clean up resources

If you no longer need any of the resources in your resource group, including the Media Services and storage accounts you created for this tutorial, delete the resource group.

Execute the following CLI command:

```azurecli
az group delete --name amsResourceGroup
```

## See also

[Job error codes](/rest/api/media/jobs/get#joberrorcode).

## Next steps

> [!div class="nextstepaction"]
> [Media Services concepts](concepts-overview.md)
