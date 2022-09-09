---
title: "Quickstart: Publish knowledge base, REST, C# - QnA Maker"
description: This C# REST-based quickstart publishes your knowledge base and creates an endpoint that can be called in your application or chat bot.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: "RESTCURL2020FEB27, devx-track-csharp"
ms.topic: how-to
#Customer intent: As an API or REST developer new to the QnA Maker service, I want to programmatically publish a knowledge base using C#.
---

# Quickstart: Publish a knowledge base in QnA Maker using C#

This REST-based quickstart walks you through programmatically publishing your knowledge base (KB). Publishing pushes the latest version of the knowledge base to a dedicated Azure Cognitive Search index and creates an endpoint that can be called in your application or chat bot.

This quickstart calls QnA Maker APIs:
* [Publish](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) - this API doesn't require any information in the body of the request.

## Prerequisites

* Latest [**Visual Studio Community edition**](https://www.visualstudio.com/downloads/).
* You must have a [QnA Maker service](../How-To/set-up-qnamaker-service-azure.md). To retrieve your key and endpoint (which includes the resource name), select **Quickstart** for your resource in the Azure portal.
* QnA Maker knowledge base (KB) ID found in the URL in the `kbid` query string parameter as shown below.

    ![QnA Maker knowledge base ID](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    If you don't have a knowledge base yet, you can create a sample one to use for this quickstart: [Create a new knowledge base](create-new-kb-csharp.md).

> [!NOTE]
> The complete solution file(s) are available from the [**Azure-Samples/cognitive-services-qnamaker-csharp** GitHub repository](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

## Create knowledge base project

1. Open Visual Studio 2019 Community edition.
1. Create a new **Console App (.NET Core)** project and name the project `QnaMakerQuickstart`. Accept the defaults for the remaining settings.

## Add required dependencies

At the top of Program.cs, replace the single using statement with the following lines to add necessary dependencies to the project:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/publish-kb.cs" id="dependencies":::

## Add required constants

In the **Program** class, add the required constants to access QnA Maker.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/publish-kb.cs" id="constants":::

## Add the Main method to publish the knowledge base

After the required constants, add the following code, which makes an HTTPS request to the QnA Maker API to publish a knowledge base and receives the response:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/publish-kb.cs" id="post":::

The API call returns a 204 status for a successful publish without any content in the body of the response.

## Build and run the program

Build and run the program. It will automatically send the request to the QnA Maker API to publish the knowledge base, then the response is printed to the console window.

Once your knowledge base is published, you can query it from the endpoint with a client application or chat bot.

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## Next steps

After the knowledge base is published, you need the [endpoint URL to generate an answer](./get-answer-from-knowledge-base-csharp.md).

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API Reference](https://go.microsoft.com/fwlink/?linkid=2092179)
