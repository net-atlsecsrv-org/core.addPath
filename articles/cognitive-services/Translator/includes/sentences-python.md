---
author: erhopf
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
---

[!INCLUDE [Prerequisites](prerequisites-python.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## Create a project and import required modules

Create a new Python project using your favorite IDE or editor. Then copy this code snippet into your project in a file named `sentence-length.py`.

```python
# -*- coding: utf-8 -*-
import os, requests, uuid, json
```

> [!NOTE]
> If you haven't used these modules you'll need to install them before running your program. To install these packages, run: `pip install requests uuid`.

The first comment tells your Python interpreter to use UTF-8 encoding. Then required modules are imported to read your subscription key from an environment variable, construct the http request, create a unique identifier, and handle the JSON response returned by the Translator.

## Set the subscription key, endpoint, and path

This sample will try to read your Translator subscription key and endpoint from the environment variables: `TRANSLATOR_TEXT_KEY` and `TRANSLATOR_TEXT_ENDPOINT`. If you're not familiar with environment variables, you can set `subscription_key` and `endpoint` as a strings and comment out the conditional statements.

Copy this code into your project:

```python
key_var_name = 'TRANSLATOR_TEXT_SUBSCRIPTION_KEY'
if not key_var_name in os.environ:
    raise Exception('Please set/export the environment variable: {}'.format(key_var_name))
subscription_key = os.environ[key_var_name]

endpoint_var_name = 'TRANSLATOR_TEXT_ENDPOINT'
if not endpoint_var_name in os.environ:
    raise Exception('Please set/export the environment variable: {}'.format(endpoint_var_name))
endpoint = os.environ[endpoint_var_name]
```

The Translator global endpoint is set as the `endpoint`. `path` sets the `breaksentence` route and identifies that we want to hit version 3 of the API.

The `params` in this sample are used to set the language of the provided text. `params` aren't required for the `breaksentence` route. If left out of the request, the API will try to detect the language of the provided text, and provide this information along with a confidence score in the response.

>[!NOTE]
> For more information about endpoints, routes, and request parameters, see [Translator 3.0: Languages](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-break-sentence).

```python
path = '/breaksentence?api-version=3.0'
params = '&language=en'
constructed_url = endpoint + path + params
```

## Add headers

The easiest way to authenticate a request is to pass in your subscription key as an
`Ocp-Apim-Subscription-Key` header, which is what we use in this sample. As an alternative, you can exchange your subscription key for an access token, and pass the access token along as an `Authorization` header to validate your request. For more information, see [Authentication](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

Copy this code snippet into your project:

```python
headers = {
    'Ocp-Apim-Subscription-Key': subscription_key,
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}
```

If you are using a Cognitive Services multi-service subscription, you must also include the `Ocp-Apim-Subscription-Region` in your request parameters. [Learn more about authenticating with the multi-service subscription](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## Create a request to determine sentence length

Define the sentence (or sentences) that you want to determine the length of:

```python
# You can pass more than one object in body.
body = [{
    'text': 'How are you? I am fine. What did you do today?'
}]
```

Next, we'll create a POST request using the `requests` module. It takes three arguments: the concatenated URL, the request headers, and the request body:

```python
request = requests.post(constructed_url, headers=headers, json=body)
response = request.json()
```

## Print the response

The last step is to print the results. This code snippet prettifies the results by sorting the keys, setting indentation, and declaring item and key separators.

```python
print(json.dumps(response, sort_keys=True, indent=4,
                 ensure_ascii=False, separators=(',', ': ')))
```

## Put it all together

That's it, you've put together a simple program that will call the Translator and return a JSON response. Now it's time to run your program:

```console
python sentence-length.py
```

If you'd like to compare your code against ours, the complete sample is available on [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python).

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

Take a look at the API reference to understand everything you can do with the Translator.

> [!div class="nextstepaction"]
> [API reference](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
