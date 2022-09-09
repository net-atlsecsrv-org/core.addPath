---
title: Create your first function in the Azure portal
description: Learn how to create your first Azure Function for serverless execution using the Azure portal.
ms.topic: how-to
ms.date: 03/26/2020
ms.custom: "devx-track-csharp, mvc, devcenter, cc996988-fb4f-47"
---

# Create your first function in the Azure portal

Azure Functions lets you run your code in a serverless environment without having to first create a virtual machine (VM) or publish a web application. In this article, you learn how to use Azure Functions to create a "hello world" HTTP trigger function in the Azure portal.

[!INCLUDE [functions-in-portal-editing-note](../../includes/functions-in-portal-editing-note.md)] 

We instead recommend that you [develop your functions locally](functions-develop-local.md) and publish to a function app in Azure.  
Use one of the following links to get started with your chosen local development environment and language:

| Visual Studio Code | Terminal/command prompt | Visual Studio |
| --- | --- | --- |
|  &bull;&nbsp;[Get started with C#](./create-first-function-vs-code-csharp.md)<br/>&bull;&nbsp;[Get started with Java](./create-first-function-vs-code-java.md)<br/>&bull;&nbsp;[Get started with JavaScript](./create-first-function-vs-code-node.md)<br/>&bull;&nbsp;[Get started with PowerShell](./create-first-function-vs-code-powershell.md)<br/>&bull;&nbsp;[Get started with Python](./create-first-function-vs-code-python.md) |&bull;&nbsp;[Get started with C#](./create-first-function-cli-csharp.md)<br/>&bull;&nbsp;[Get started with Java](./create-first-function-cli-java.md)<br/>&bull;&nbsp;[Get started with JavaScript](./create-first-function-cli-node.md)<br/>&bull;&nbsp;[Get started with PowerShell](./create-first-function-cli-powershell.md)<br/>&bull;&nbsp;[Get started with Python](./create-first-function-cli-python.md) | [Get started with C#](functions-create-your-first-function-visual-studio.md) |

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## Sign in to Azure

Sign in to the [Azure portal](https://portal.azure.com) with your Azure account.

## Create a function app

You must have a function app to host the execution of your functions. A function app lets you group functions as a logical unit for easier management, deployment, scaling, and sharing of resources.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Next, create a function in the new function app.

## <a name="create-function"></a>Create an HTTP trigger function

1. From the left menu of the **Functions** window, select **Functions**, then select **Add** from the top menu. 
 
1. From the **Add Function** window, select the **Http trigger** template.

    ![Choose HTTP trigger function](./media/functions-create-first-azure-function/function-app-select-http-trigger.png)

1. Under **Template details** use `HttpExample` for **New Function**, choose **Anonymous** from the **[Authorization level](functions-bindings-http-webhook-trigger.md#authorization-keys)** drop-down list, and then select **Add**.

    Azure creates the HTTP trigger function. Now, you can run the new function by sending an HTTP request.

## Test the function

1. In your new HTTP trigger function, select **Code + Test** from the left menu, then select **Get function URL** from the top menu.

    ![Select Get function URL](./media/functions-create-first-azure-function/function-app-select-get-function-url.png)

1. In the **Get function URL** dialog box, select **default** from the drop-down list, and then select the **Copy to clipboard** icon. 

    ![Copy the function URL from the Azure portal](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

1. Paste the function URL into your browser's address bar. Add the query string value `?name=<your_name>` to the end of this URL and press Enter to run the request. 

    The following example shows the response in the browser:

    ![Function response in the browser.](./media/functions-create-first-azure-function/function-app-browser-testing.png)

    If the request URL included an [access key](functions-bindings-http-webhook-trigger.md#authorization-keys) (`?code=...`), it means you choose **Function** instead of **Anonymous** access level when creating the function. In this case, you should instead append `&name=<your_name>`.

1. When your function runs, trace information is written to the logs. To see the trace output, return to the **Code + Test** page in the portal and expand the **Logs** arrow at the bottom of the page.

   ![Functions log viewer in the Azure portal.](./media/functions-create-first-azure-function/function-view-logs.png)

## Clean up resources

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## Next steps

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
