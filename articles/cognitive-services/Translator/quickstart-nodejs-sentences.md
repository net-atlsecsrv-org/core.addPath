---
title: "Quickstart: Get sentence lengths, Node.js - Translator Text API"
titleSuffix: Azure Cognitive Services
description: In this quickstart, you'll learn how to determine sentence lengths (in characters) using Node.js and the Translator Text REST API.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 02/21/2019
ms.author: erhopf
---

# Quickstart: Use the Translator Text API to determine sentence length with Node.js

In this quickstart, you'll learn how to determine sentence lengths (in characters) using Node.js and the Translator Text REST API.

This quickstart requires an [Azure Cognitive Services account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) with a Translator Text resource. If you don't have an account, you can use the [free trial](https://azure.microsoft.com/try/cognitive-services/) to get a subscription key.

## Prerequisites

This quickstart requires:

* [Node 8.12.x or later](https://nodejs.org/en/)
* An Azure subscription key for Translator Text

## Create a project and import required modules

Create a new project using your favorite IDE or editor. Then copy this code snippet into your project in a file named `sentence-length.js`.

```javascript
const request = require('request');
const uuidv4 = require('uuid/v4');
```

> [!NOTE]
> If you haven't used these modules you'll need to install them before running your program. To install these packages, run: `npm install request uuidv4`.

These modules are required to construct the HTTP request, and create a unique identifier for the `'X-ClientTraceId'` header.

## Set the subscription key

This code will try to read your Translator Text subscription key from the environment variable `TRANSLATOR_TEXT_KEY`. If you're not familiar with environment variables, you can set `subscriptionKey` as a string and comment out the conditional statement.

Copy this code into your project:

```javascript
/* Checks to see if the subscription key is available
as an environment variable. If you are setting your subscription key as a
string, then comment these lines out.

If you want to set your subscription key as a string, replace the value for
the Ocp-Apim-Subscription-Key header as a string. */
const subscriptionKey = process.env.TRANSLATOR_TEXT_KEY;
if (!subscriptionKey) {
  throw new Error('Environment variable for your subscription key is not set.')
};
```

## Configure the request

The `request()` method, made available through the request module, allows us to pass the HTTP method, URL, request params, headers, and the JSON body as an `options` object. In this code snippet, we'll configure the request:

>[!NOTE]
> For more information about endpoints, routes, and request parameters, see [Translator Text API 3.0: Break Sentence](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-break-sentence).

```javascript
let options = {
    method: 'POST',
    baseUrl: 'https://api.cognitive.microsofttranslator.com/',
    url: 'breaksentence',
    qs: {
      'api-version': '3.0',
    },
    headers: {
      'Ocp-Apim-Subscription-Key': subscriptionKey,
      'Content-type': 'application/json',
      'X-ClientTraceId': uuidv4().toString()
    },
    body: [{
          'text': 'How are you? I am fine. What did you do today?'
    }],
    json: true,
};
```

### Authentication

The easiest way to authenticate a request is to pass in your subscription key as an
`Ocp-Apim-Subscription-Key` header, which is what we use in this sample. As an alternative, you can exchange your subscription key for an access token, and pass the access token along as an `Authorization` header to validate your request. For more information, see [Authentication](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## Make the request and print the response

Next, we'll create the request using the `request()` method. It takes the `options` object that we created in the previous section as the first argument, then prints the prettified JSON response.

```javascript
request(options, function(err, res, body){
    console.log(JSON.stringify(body, null, 4));
});
```

>[!NOTE]
> In this sample, we're defining the HTTP request in the `options` object. However, the request module also supports convenience methods, like `.post` and `.get`. For more information, see [convenience methods](https://github.com/request/request#convenience-methods).

## Put it all together

That's it, you've put together a simple program that will call the Translator Text API and return a JSON response. Now it's time to run your program:

```console
node sentence-length.js
```

If you'd like to compare your code against ours, the complete sample is available on [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-NodeJS).

## Sample response

```json
[
    {
        "sentLen": [
            13,
            11,
            22
        ]
    }
]
```

## Clean up resources

If you've hardcoded your subscription key into your program, make sure to remove the subscription key when you're finished with this quickstart.

## Next steps

> [!div class="nextstepaction"]
> [Explore Node.js examples on GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-NodeJS)

## See also

In addition to language detection, learn how to use the Translator Text API to:

* [Translate text](quickstart-nodejs-translate.md)
* [Transliterate text](quickstart-nodejs-transliterate.md)
* [Identify the language by input](quickstart-nodejs-detect.md)
* [Get alternate translations](quickstart-nodejs-dictionary.md)
* [Get a list of supported languages](quickstart-nodejs-languages.md)
