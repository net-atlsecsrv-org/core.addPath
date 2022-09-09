---
title: "Quickstart: QnA Maker client library for Python"
description: This quickstart shows how to get started with the QnA Maker client library for Python.
ms.topic: include
ms.date: 06/18/2020
---

Use the QnA Maker client library for python to:

* Create a knowledgebase
* Update a knowledgebase
* Publish a knowledgebase
* Get prediction runtime endpoint key
* Wait for long-running task
* Download a knowledgebase
* Get answer
* Delete knowledge base

[Reference documentation](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker?view=azure-python) | [Library source code](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-knowledge-qnamaker) | [Package (PyPi)](https://pypi.org/project/azure-cognitiveservices-knowledge-qnamaker/) | [Python samples](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## Prerequisites

* Azure subscription - [Create one for free](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
* Once you have your Azure subscription, create a [QnA Maker resource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) in the Azure portal to get your authoring key and endpoint. After it deploys, select **Go to resource**.
    * You will need the key and endpoint from the resource you create to connect your application to the QnA Maker API. You'll paste your key and endpoint into the code below later in the quickstart.
    * You can use the free pricing tier (`F0`) to try the service, and upgrade later to a paid tier for production.

## Setting up

### Install the client library

After installing Python, you can install the client library with:

```console
pip install azure-cognitiveservices-knowledge-qnamaker
```

### Create a new python application

Create a new Python file named `quickstart-file.py` and import the following libraries.

[!code-python[Dependencies](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=Dependencies&highlight=4,5)]

Create variables for your resource's Azure endpoint and key.

> [!IMPORTANT]
> Go to the Azure portal and find the key and endpoint for the QnA Maker resource you created in the prerequisites. They will be located on the resource's **key and endpoint** page, under **resource management**.
> You need the entire key to create your knowledgebase. You need only the resource name from the endpoint. The format is `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`.
> Remember to remove the key from your code when you're done, and never post it publicly. For production, consider using a secure way of storing and accessing your credentials. For example, [Azure key vault](../../../key-vault/general/overview.md) provides secure key storage.

[!code-python[Resource variables](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=Resourcevariables)]

## Object models

[QnA Maker](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker?view=azure-python) Maker uses two different object models:
* **[QnAMakerClient](#qnamakerclient-object-model)** is the object to create, manage, publish, and download the knowledgebase.
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** is the object to query the knowledge base with the GenerateAnswer API and send new suggested questions using the Train API (as part of [active learning](../concepts/active-learning-suggestions.md)).

[!INCLUDE [Get KBinformation](./quickstart-sdk-cognitive-model.md)]

### QnAMakerClient object model

The authoring QnA Maker client is a [QnAMakerClient](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker?view=azure-python) object that authenticates to Azure using Microsoft.Rest.ServiceClientCredentials, which contains your key.

Once the client is created, use the [Knowledge base](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.knowledgebaseoperations?view=azure-python) property to create, manage, and publish your knowledge base.

Manage your knowledge base by sending a JSON object. For immediate operations, a method usually returns a JSON object indicating status. For long-running operations, the response is the operation ID. Call the [operations.get_details](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.operation(class)?view=azure-python#get-details-operation-id--custom-headers-none--raw-false----operation-config-) method with the operation ID to determine the [status of the request](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.operation(class)?view=azure-python).

### QnAMakerRuntimeClient object model

The prediction QnA Maker client is a [QnAMakerRuntimeClient](/javascript/api/@azure/cognitiveservices-qnamaker-runtime/qnamakerruntimeclient?view=azure-node-latest) object that authenticates to Azure using Microsoft.Rest.ServiceClientCredentials, which contains your prediction runtime key, returned from the authoring client call, [client.EndpointKeysOperations.get_keys](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.endpointkeysoperations?view=azure-python) after the knowledgebase is published.

Use the [generate_answer](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtime.-ctor?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Runtime__ctor_Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerRuntimeClient_#generate-answer-kb-id--generate-answer-payload--custom-headers-none--raw-false----operation-config-) method to get an answer from the query runtime.

## Authenticate the client for authoring the knowledge base

Instantiate a client with your endpoint and key. Create an CognitiveServicesCredentials object with your key, and use it with your endpoint to create an [QnAMakerClient](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-python) object.

[!code-python[Authorization to resource key](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=AuthorizationAuthor)]

## Create a knowledge base

 Use the client object to get a [knowledge base operations](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python) object.

A knowledge base stores question and answer pairs for the [CreateKbDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.create_kb_dto) object from three sources:

* For **editorial content**, use the [QnADTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.qnadto?view=azure-python) object.
    * To use metadata and follow-up prompts, use the editorial context, because this data is added at the individual QnA pair level.
* For **files**, use the [FileDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.file_dto) object. The FileDTO includes the filename as well as the public URL to reach the file.
* For **URLs**, use a list of strings to represent publicly available URLs.

Call the [create](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python) method then pass the returned operation ID to the [Operations.getDetails](#get-status-of-an-operation) method to poll for status.

The final line of the following code returns the knowledge base ID from the response from MonitorOperation.

[!code-python[Create knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=CreateKBMethod&highlight=36,38)]

Make sure the include the [`_monitor_operation`](#get-status-of-an-operation) function, referenced in the above code, in order to successfully create a knowledge base.

## Update a knowledge base

You can update a knowledge base by passing in the knowledge base ID and an [UpdateKbOperationDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto?view=azure-python) containing [add](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd?view=azure-python), [update](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate?view=azure-python), and [delete](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete?view=azure-python) DTO objects to the [update](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebase_operations.knowledgebaseoperations?view=azure-python) method. Use the [Operation.getDetail](#get-status-of-an-operation) method to determine if the update succeeded.

[!code-python[Update a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=UpdateKBMethod&highlight=68,69)]

Make sure the include the [`_monitor_operation`](#get-status-of-an-operation) function, referenced in the above code, in order to successfully update a knowledge base.

## Download a knowledge base

Use the [download](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python) method to download the database as a list of [QnADocumentsDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto?view=azure-python). This is _not_ equivalent to the QnA Maker portal's export from the **Settings** page because the result of this method is not a TSV file.

[!code-python[Download a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=DownloadKB&highlight=2)]

## Publish a knowledge base

Publish the knowledge base using the [publish](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.knowledgebase_operations.knowledgebaseoperations?view=azure-python#publish-kb-id--custom-headers-none--raw-false----operation-config-) method. This takes the current saved and trained model, referenced by the knowledge base ID, and publishes that at an endpoint.

[!code-python[Publish a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=PublishKB&highlight=2)]

## Get query runtime key

Once a knowledgebase is published, you need the query runtime key to query the runtime. This isn't the same key used to create the original client object.

Use the [EndpointKeysOperations.get_keys](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.endpointkeysoperations?view=azure-python) method to get the [EndpointKeysDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.endpointkeysdto?view=azure-python) class.

Use either of the key properties returned in the object to query the knowledgebase.

[!code-python[Get query runtime key](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=GetQueryEndpointKey&highlight=2)]


## Authenticate the runtime for generating an answer

Create a [QnAMakerRuntimeClient](/javascript/api/@azure/cognitiveservices-qnamaker-runtime/qnamakerruntimeclient?view=azure-node-latest) to query the knowledge base to generate an answer or train from active learning.

[!code-python[Authenticate the runtime](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=AuthorizationQuery)]

Use the QnAMakerRuntimeClient to get an answer from the knowledge or to send new suggested questions to the knowledge base for [active learning](../concepts/active-learning-suggestions.md).

## Generate an answer from the knowledge base

Generate an answer from a published knowledge base using the RuntimeClient.runtime.generateAnswer method. This method accepts the knowledge base ID and the QueryDTO. Access additional properties of the QueryDTO, such a Top and Context to use in your chat bot.

[!code-python[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=GenerateAnswer&highlight=5)]

This is a simple example querying the knowledge base. To understand advanced querying scenarios, review [other query examples](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer).

## Delete a knowledge base

Delete the knowledge base using the [delete](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.knowledgebase_operations.knowledgebaseoperations?view=azure-python#delete-kb-id--custom-headers-none--raw-false----operation-config-) method with a parameter of the knowledge base ID.

[!code-python[Delete a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=DeleteKB&highlight=2)]

## Get status of an operation

Some methods, such as create and update, can take enough time that instead of waiting for the process to finish, an [operation](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.operation(class)?view=azure-python) is returned. Use the operation ID from the operation to poll (with retry logic) to determine the status of the original method.

The _setTimeout_ call in the following code block is used to simulate asynchronous code. Replace this with retry logic.

[!code-python[Monitor an operation](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=MonitorOperation&highlight=7)]

## Run the application

Run the application with the python command on your quickstart file.

```console
python quickstart-file.py
```

The source code for this sample can be found on [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/QnAMaker/sdk/quickstart.py).