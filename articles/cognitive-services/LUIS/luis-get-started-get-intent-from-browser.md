---
title: "Quickstart: Get intent with browser - LUIS"
titleSuffix: Azure Cognitive Services
description: In this quickstart, use an available public LUIS app to determine a user's intention from conversational text in a browser.  
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 10/17/2019
ms.author: diberry
#Customer intent: As an developer familiar with how to use a browser but new to the LUIS service, I want to query the LUIS endpoint of a published model so that I can see the JSON prediction response.
---

# Quickstart: Get intent with a browser

To understand what a LUIS prediction endpoint returns, view a prediction result in a web browser. 

## Prerequisites

In order to query a public app, you need:

* Your own Language Understanding (LUIS) key. If you do not already have a subscription to create a key, you can register for a [free account](https://azure.microsoft.com/free/). The LUIS authoring key will not work. 
* The public app's ID: `df67dcdb-c37d-46af-88e1-8b97951ca1c2`. 

## Use the browser to see predictions

1. Open a web browser. 
1. Use the complete URLs below, replacing `YOUR-KEY` with your own LUIS key. The requests are GET requests and include the authorization, with your LUIS key, as a query string parameter.

    #### [V3 prediction request](#tab/V3-1-1)
    
    
    The format of the V3 URL for a **GET** endpoint (by slots) request is:
    
    `
    https://westus.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-KEY
    `

    #### [V2 prediction request](#tab/V2-1-2)
    
    The format of the V2 URL for a **GET** endpoint request is:
    
    `
    https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=YOUR-KEY&q=turn on all lights
    `

1. Paste the URL into a browser window and press Enter. The browser displays a JSON result that indicates that LUIS detects the `HomeAutomation.TurnOn` intent as the top intent and the `HomeAutomation.Operation` entity with the value `on`.

    #### [V3 prediction response](#tab/V3-2-1)

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ]
            }
        }
    }
    ```

    #### [V2 prediction response](#tab/V2-2-2)

    ```json
    {
        "query": "turn on all lights",
        "topScoringIntent": {
            "intent": "HomeAutomation.TurnOn",
            "score": 0.5375382
        },
        "entities": [
            {
                "entity": "on",
                "type": "HomeAutomation.Operation",
                "startIndex": 5,
                "endIndex": 6,
                "score": 0.724984169
            }
        ]
    }
    ```

    * * *

1. To see all the intents, add the appropriate query string parameter. 

    #### [V3 prediction endpoint](#tab/V3-3-1)

    Add `show-all-intents=true` to the end of the querystring to **show all intents**:

    `
    https://westus.api.cognitive.microsoft.com/luis/predict/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-KEY&show-all-intents=true
    `

    ```JSON
    {
        "query": "turn off the living room light",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                },
                "None": {
                    "score": 0.08687421
                },
                "HomeAutomation.TurnOff": {
                    "score": 0.0207554
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ]
            }
        }
    }
    ```

    #### [V2 prediction endpoint](#tab/V2)

    Add `verbose=true` to the end of the querystring to **show all intents**:

    `
    https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?q=turn on all lights&subscription-key={your-key}&verbose=true
    `

    ```json
    {
        "query": "turn on all lights",
        "topScoringIntent": {
            "intent": "HomeAutomation.TurnOn",
            "score": 0.5375382
        },
        "intents": [
            {
                "intent": "HomeAutomation.TurnOn",
                "score": 0.5375382
            },
            {
                "intent": "None",
                "score": 0.08687421
            },
            {
                "intent": "HomeAutomation.TurnOff",
                "score": 0.0207554
            }
        ],
        "entities": [
            {
                "entity": "on",
                "type": "HomeAutomation.Operation",
                "startIndex": 5,
                "endIndex": 6,
                "score": 0.724984169
            }
        ]
    }
    ```


<!-- FIX - is the public app getting updated for the new prebuilt domain with entities? -->   

## Next steps

Learn more about the [V3 prediction endpoint](luis-migration-api-v3.md).

> [!div class="nextstepaction"]
> [Create an app in the LUIS portal](get-started-portal-build-app.md)