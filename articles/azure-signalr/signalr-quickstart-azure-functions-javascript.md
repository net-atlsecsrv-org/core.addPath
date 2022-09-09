---
title: Azure SignalR Service serverless quickstart - JavaScript
description: A quickstart for using Azure SignalR Service and Azure Functions to create a chat room.
author: sffamily
ms.service: signalr
ms.devlang: javascript
ms.topic: quickstart
ms.date: 12/14/2019
ms.author: zhshang
---
# Quickstart: Create a chat room with Azure Functions and SignalR Service using JavaScript

Azure SignalR Service lets you easily add real-time functionality to your application. Azure Functions is a serverless platform that lets you run your code without managing any infrastructure. In this quickstart, learn how to use SignalR Service and Functions to build a serverless, real-time chat application.

## Prerequisites

This quickstart can be run on macOS, Windows, or Linux.

Make sure you have a code editor such as [Visual Studio Code](https://code.visualstudio.com/) installed.

Install the [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing) (version 2 or higher) to run Azure Function apps locally.

This quickstart uses [Node.js](https://nodejs.org/en/download/) 10.x but should work with other versions. Refer to the [Azure Functions runtime versions documentation](../azure-functions/functions-versions.md#languages) for more information on supported Node.js versions.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## Log in to Azure

Sign in to the Azure portal at <https://portal.azure.com/> with your Azure account.

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]

## Configure and run the Azure Function app

1. In the browser where the Azure portal is opened, confirm the SignalR Service instance you deployed earlier was successfully created by searching for its name in the search box at the top of the portal. Select the instance to open it.

    ![Search for the SignalR Service instance](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. Select **Keys** to view the connection strings for the SignalR Service instance.

1. Select and copy the primary connection string.

    ![Create SignalR Service](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

1. In your code editor, open the *src/chat/javascript* folder in the cloned repository.

1. Rename *local.settings.sample.json* to *local.settings.json*.

1. In **local.settings.json**, paste the connection string into the value of the **AzureSignalRConnectionString** setting. Save the file.

1. JavaScript functions are organized into folders. In each folder are two files: *function.json* defines the bindings that are used in the function, and *index.js* is the body of the function. There are two HTTP triggered functions in this function app:

    - **negotiate** - Uses the *SignalRConnectionInfo* input binding to generate and return valid connection information.
    - **messages** - Receives a chat message in the request body and uses the *SignalR* output binding to broadcast the message to all connected client applications.

1. In the terminal, ensure that you are in the *src/chat/javascript* folder. Run the function app.

    ```bash
    func start
    ```

    ![Create SignalR Service](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-run-application.png)

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

## Next steps

In this quickstart, you built and ran a real-time serverless application in VS Code. Next, learn more about how to deploy Azure Functions from VS Code.

> [!div class="nextstepaction"]
> [Deploy Azure Functions with VS Code](/azure/javascript/tutorial-vscode-serverless-node-01)
