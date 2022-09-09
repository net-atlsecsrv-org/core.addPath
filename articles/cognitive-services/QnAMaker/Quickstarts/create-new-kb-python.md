---
title:  "Quickstart: Create knowledge base - REST, Python - QnA Maker"
description: This Python REST-based quickstart walks you through creating a sample QnA Maker knowledge base, programmatically, that will appear in your Azure Dashboard of your Cognitive Services API account.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 12/16/2019
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27, devx-track-python
ms.topic: how-to
---

# Quickstart: Create a knowledge base in QnA Maker using Python

This quickstart walks you through programmatically creating and publishing a sample QnA Maker knowledge base. QnA Maker automatically extracts questions and answers from semi-structured content, like FAQs, from [data sources](../index.yml). The model for the knowledge base is defined in the JSON sent in the body of the API request.

This quickstart calls QnA Maker APIs:
* [Create KB](/rest/api/cognitiveservices/qnamaker/knowledgebase/create)
* [Get Operation Details](/rest/api/cognitiveservices/qnamaker/operations/getdetails)

[Reference documentation](/rest/api/cognitiveservices/qnamaker/knowledgebase) | [Python Sample](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## Prerequisites

* [Python 3.7](https://www.python.org/downloads/)
* You must have a [QnA Maker service](../How-To/set-up-qnamaker-service-azure.md). To retrieve your key and endpoint (which includes the resource name), select **Quickstart** for your resource in the Azure portal.

## Create a knowledge base Python file

Create a file named `create-new-knowledge-base-3x.py`.

## Add the required dependencies

At the top of `create-new-knowledge-base-3x.py`, add the following lines to add necessary dependencies to the project:

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/create-kb.py" id="dependencies":::

## Add the required constants
After the preceding required dependencies, add the required constants to access QnA Maker. Replace the value of the `<your-qna-maker-subscription-key>` and `<your-resource-name>` with your own QnA Maker key and resource name.

At the top of the Program class, add the required constants to access QnA Maker.

Set the following values:

* `<your-qna-maker-subscription-key>` - The **key** is a 32 character string and is available in the Azure portal, on the QnA Maker resource, on the Quickstart page. This is not the same as the prediction endpoint key.
* `<your-resource-name>` - Your **resource name** is used to construct the authoring endpoint URL for authoring, in the format of `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`. This is not the same URL used to query the prediction endpoint.

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/create-kb.py" id="constants":::

## Add the KB model definition

After the constants, add the following KB model definition. The model is converting into a string after the definition.

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/create-kb.py" id="model":::

## Add supporting function

Add the following function to print out JSON in a readable format:

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/create-kb.py" id="pretty":::

## Add function to create KB

Add the following function to make an HTTP POST request to create the knowledge base.
This API call returns a JSON response that includes the operation ID in the header field **Location**. Use the operation ID to determine if the KB is successfully created. The `Ocp-Apim-Subscription-Key` is the QnA Maker service key, used for authentication.

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/create-kb.py" id="create_kb":::

This API call returns a JSON response that includes the operation ID. Use the operation ID to determine if the KB is successfully created.

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:19:01Z",
  "lastActionTimestamp": "2018-09-26T05:19:01Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "8dfb6a82-ae58-4bcb-95b7-d1239ae25681"
}
```

## Add function to check creation status

The following function checks the creation status sending in the operation ID at the end of the URL route. The call to `check_status` is inside the main _while_ loop.

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/create-kb.py" id="get_status":::

This API call returns a JSON response that includes the operation status:

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:22:53Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

Repeat the call until success or failure:

```JSON
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:23:08Z",
  "resourceLocation": "/knowledgebases/XXX7892b-10cf-47e2-a3ae-e40683adb714",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

## Add main code block
The following loop polls for the creation operation status periodically until the operation is complete.

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/create-kb.py" id="main":::

## Build and run the program

Enter the following command at a command-line to run the program. It will send the request to the QnA Maker API to create the KB, then it will poll for the results every 30 seconds. Each response is printed to the console window.

```bash
python create-new-knowledge-base-3x.py
```

Once your knowledge base is created, you can view it in your QnA Maker Portal, [My knowledge bases](https://www.qnamaker.ai/Home/MyServices) page. Select your knowledge base name, for example QnA Maker FAQ, to view.

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## Next steps

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API Reference](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase)