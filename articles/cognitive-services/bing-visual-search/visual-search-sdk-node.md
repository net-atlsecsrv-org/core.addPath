---
title: "Quickstart: Get image insights using the SDK for Node.js - Bing Visual Search"
titleSuffix: Azure Cognitive Services
description: Use this quickstart to begin getting image insights from the Bing Visual Search service, using the Node.js SDK.
services: cognitive-services
author: aahill
manager: nitinme

ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: aahi
---

# Quickstart: Get image insights using the Bing Visual Search SDK for Node.js

Use this quickstart to begin getting image insights from the Bing Visual Search service, using the Node.js SDK. While Bing Visual Search has a REST API compatible with most programming languages, the SDK provides an easy way to integrate the service into your applications. The source code for this sample can be found on [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/visualSearch.js). 

## Prerequisites
* [Node.js](https://www.nodejs.org/)
* The Bing Visual Search SDK for Node.js
    * To set up a console application using the Bing Visual Search SDK, run the following commands:
        1. `npm install ms-rest-azure`
        2. `npm install azure-cognitiveservices-visualsearch`.


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

<a name="client"></a>

## Create and initialize the application

1. Create a new JavaScript file in your favorite IDE or editor, and add the following requirements. Then create variables for your subscription key, Custom Configuration ID, and file path to the image you want to upload. 

    ```javascript
    const os = require("os");
    const async = require('async');
    const fs = require('fs');
    const Search = require('azure-cognitiveservices-visualsearch');
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    
    let keyVar = 'YOUR-VISUAL-SEARCH-ACCESS-KEY';
    let credentials = new CognitiveServicesCredentials(keyVar);
    let filePath = "../Data/image.jpg";
    ```

2. Instantiate the client.

    ```javascript
    let visualSearchClient = new Search.VisualSearchClient(credentials);
    ```

## Search for images

1. Use `fs.createReadStream()` to read in your image file, and create variables for your search request and results. Then use the client to search images.

    ```javascript
    let fileStream = fs.createReadStream(filePath);
    let visualSearchRequest = JSON.stringify({});
    let visualSearchResults;
    try {
        visualSearchResults = await visualSearchClient.images.visualSearch({
            image: fileStream,
            knowledgeRequest: visualSearchRequest
        });
        console.log("Search visual search request with binary of image");
    } catch (err) {
        console.log("Encountered exception. " + err.message);
    }
    ```

2. Parse the results of the previous query:

    ```javascript
    // Visual Search results
    if (visualSearchResults.image.imageInsightsToken) {
        console.log(`Uploaded image insights token: ${visualSearchResults.image.imageInsightsToken}`);
    }
    else {
        console.log("Couldn't find image insights token!");
    }
    
    // List of tags
    if (visualSearchResults.tags.length > 0) {
        let firstTagResult = visualSearchResults.tags[0];
        console.log(`Visual search tag count: ${visualSearchResults.tags.length}`);
    
        // List of actions in first tag
        if (firstTagResult.actions.length > 0) {
            let firstActionResult = firstTagResult.actions[0];
            console.log(`First tag action count: ${firstTagResult.actions.length}`);
            console.log(`First tag action type: ${firstActionResult.actionType}`);
        }
        else {
            console.log("Couldn't find tag actions!");
        }
    
    }
    else {
        console.log("Couldn't find image tags!");
    }
    
    ```

## Next steps

> [!div class="nextstepaction"]
> [Build a single-page web app](tutorial-bing-visual-search-single-page-app.md)
