---
title: "Quickstart: Publish knowledge base, REST, Go - QnA Maker"
description: This Go REST-based quickstart publishes your knowledge base and creates an endpoint that can be called in your application or chat bot.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: how-to
---

# Quickstart: Publish a knowledge base in QnA Maker using Go

This REST-based quickstart walks you through programmatically publishing your knowledge base (KB). Publishing pushes the latest version of the knowledge base to a dedicated Azure Cognitive Search index and creates an endpoint that can be called in your application or chat bot.

This quickstart calls QnA Maker APIs:
* [Publish](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) - this API doesn't require any information in the body of the request.

## Prerequisites

* [Go 1.10.1](https://golang.org/dl/)
* You must have a [QnA Maker service](../How-To/set-up-qnamaker-service-azure.md). To retrieve your key and endpoint (which includes the resource name), select **Quickstart** for your resource in the Azure portal.

* QnA Maker knowledge base (KB) ID found in the URL in the `kbid` query string parameter as shown below.

    ![QnA Maker knowledge base ID](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    If you don't have a knowledge base yet, you can create a sample one to use for this quickstart: [Create a new knowledge base](create-new-kb-csharp.md).

> [!NOTE]
> The complete solution file(s) are available from the [**Azure-Samples/cognitive-services-qnamaker-go** GitHub repository](https://github.com/Azure-Samples/cognitive-services-qnamaker-go/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

## Create a Go file

Open VSCode and create a new file named `publish-kb.go`.

## Add the required dependencies

At the top of `publish-kb.go`, add the following lines to add necessary dependencies to the project:

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/publish-kb.go" id="dependencies":::

## Create the main function

After the required dependencies, add the following class:

```Go
package main

func main() {

}
```

## Add POST request to publish KB

Add the following code, which makes an HTTPS request to the QnA Maker API to publish a knowledge base and receives the response:

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/publish-kb.go" id="main":::

The API call returns a 204 status for a successful publish without any content in the body of the response. The code adds content for 204 responses.

For any other response, that response is returned unaltered.

## Build and run the program

Enter the following command to compile the file. The command prompt does not return any information for a successful build.

```bash
go build publish-kb.go
```

Enter the following command at a command-line to run the program. It will send the request to the QnA Maker API to publish the KB, then print out 204 for success or errors.

```bash
./publish-kb
```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## Next steps

After the knowledge base is published, you need the [endpoint URL to generate an answer](./get-answer-from-knowledge-base-go.md).

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API Reference](https://go.microsoft.com/fwlink/?linkid=2092179)
