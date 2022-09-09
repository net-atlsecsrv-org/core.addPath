---
title: Connect Azure Functions to Azure Storage using command line tools 
description: Learn how to connect Azure Functions to an Azure Storage queue by adding an output binding to your command line project. 
ms.date: 02/07/2020
ms.topic: quickstart
zone_pivot_groups: programming-languages-set-functions
---

# Connect Azure Functions to Azure Storage using command line tools

In this article, you integrate an Azure Storage queue with the function and storage account you created in [the previous quickstart](functions-create-first-azure-function-azure-cli.md). You achieve this integration by using an *output binding* that writes data from an HTTP request to a message in the queue. Completing this article incurs no additional costs beyond the few USD cents of the previous quickstart. To learn more about bindings, see [Azure Functions triggers and bindings concepts](functions-triggers-bindings.md).

## Configure your local environment

Before you begin, you must complete the article, [Quickstart: Create an Azure Functions project from the command line](functions-create-first-azure-function-azure-cli.md). If you already cleaned up resources at the end of that article, go through the steps again to recreate the function app and related resources in Azure.

## Retrieve the Azure Storage connection string

When you created a function app in Azure in the previous quickstart, you also created a Storage account. The connection string for this account is stored securely in app settings in Azure. By downloading the setting into the *local.settings.json* file, you can use that connection write to a Storage queue in the same account when running the function locally. 

1. From the root of the project, run the following command, replacing `<APP_NAME>` with the name of your function app from the previous quickstart. This command will overwrite any existing values in the file.

    ```
    func azure functionapp fetch-app-settings <APP_NAME>
    ```
    
1. Open *local.settings.json* and locate the value named `AzureWebJobsStorage`, which is the Storage account connection string. You use the name `AzureWebJobsStorage` and the connection string in other sections of this article.

> [!IMPORTANT]
> Because *local.settings.json* contains secrets downloaded from Azure, always exclude this file from source control. The *.gitignore* file created with a local functions project excludes the file by default.

[!INCLUDE [functions-register-storage-binding-extension-csharp](../../includes/functions-register-storage-binding-extension-csharp.md)]

## Add an output binding definition to the function

Although a function can have only one trigger, it can have multiple input and output bindings, which let you connect to other Azure services and resources without writing custom integration code. 

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
You declare these bindings in the *function.json* file in your function folder. From the previous quickstart, your *function.json* file in the *HttpExample* folder contains two bindings in the `bindings` collection:  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json" range="2-18":::  
::: zone-end

::: zone pivot="programming-language-python"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json" range="2-18":::  
::: zone-end

::: zone pivot="programming-language-powershell"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json" range="2-18":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript, programming-language-powershell, programming-language-typescript"  
Each binding has at least a type, a direction, and a name. In the example above, the first binding is of type `httpTrigger` with the direction `in`. For the `in` direction, `name` specifies the name of an input parameter that's sent to the function when invoked by the trigger.  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
The second binding in the collection is named `res`. This `http` binding is an output binding (`out`) that is used to write the HTTP response. 

To write to an Azure Storage queue from this function, add an `out` binding of type `queue` with the name `msg`, as shown in the code below:

:::code language="json" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python"  
The second binding in the collection is of type `http` with the direction `out`, in which case the special `name` of `$return` indicates that this binding uses the function's return value rather than providing an input parameter.

To write to an Azure Storage queue from this function, add an `out` binding of type `queue` with the name `msg`, as shown in the code below:

:::code language="json" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-powershell"  
The second binding in the collection is named `res`. This `http` binding is an output binding (`out`) that is used to write the HTTP response. 

To write to an Azure Storage queue from this function, add an `out` binding of type `queue` with the name `msg`, as shown in the code below:

:::code language="json" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
In this case, `msg` is given to the function as an output argument. For a `queue` type, you must also specify the name of the queue in `queueName` and provide the *name* of the Azure Storage connection (from *local.settings.json*) in `connection`. 
::: zone-end  

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]  
::: zone-end  

For more information on the details of bindings, see [Azure Functions triggers and bindings concepts](functions-triggers-bindings.md) and [queue output configuration](functions-bindings-storage-queue-output.md#configuration).

## Add code to use the output binding

With the queue binding specified in *function.json*, you can now update your function to receive the `msg` output parameter and write messages to the queue.

::: zone pivot="programming-language-python"     
[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]
::: zone-end  

::: zone pivot="programming-language-javascript"  
[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]
::: zone-end  

::: zone pivot="programming-language-typescript"  
[!INCLUDE [functions-add-output-binding-ts](../../includes/functions-add-output-binding-ts.md)]
::: zone-end  

::: zone pivot="programming-language-powershell"  
[!INCLUDE [functions-add-output-binding-powershell](../../includes/functions-add-output-binding-powershell.md)]  
::: zone-end

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]
::: zone-end 

Observe that you *don't* need to write any code for authentication, getting a queue reference, or writing data. All these integration tasks are conveniently handled in the Azure Functions runtime and queue output binding.

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

[!INCLUDE [functions-extension-bundles-info](../../includes/functions-extension-bundles-info.md)]

## View the message in the Azure Storage queue

You can view the queue in the [Azure portal](../storage/queues/storage-quickstart-queues-portal.md) or in the  [Microsoft Azure Storage Explorer](https://storageexplorer.com/). You can also view the queue in the Azure CLI, as described in the following steps:

1. Open the function project's *local.setting.json* file and copy the connection string value. In a terminal or command window, run the following command to create an environment variable named `AZURE_STORAGE_CONNECTION_STRING`, pasting your specific connection string in place of  `<MY_CONNECTION_STRING>`. (This environment variable means you don't need to supply the connection string to each subsequent command using the `--connection-string` argument.)

    # [bash](#tab/bash)
    
    ```bash
    AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    # [PowerShell](#tab/powershell)
    
    ```powershell
    $env:AZURE_STORAGE_CONNECTION_STRING = "<MY_CONNECTION_STRING>"
    ```
    
    # [Azure CLI](#tab/cmd)
    
    ```azurecli
    set AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    ---
    
1. (Optional) Use the [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) command to view the Storage queues in your account. The output from this command should include a queue named `outqueue`, which was created when the function wrote its first message to that queue.
    
    ```azurecli
    az storage queue list --output tsv
    ```

1. Use the [`az storage message get`](/cli/azure/storage/message#az-storage-message-get) command to read the message from this queue, which should be the first name you used when testing the function earlier. The command reads and removes the first message from the queue. 

    # [bash](#tab/bash)
    
    ```bash
    echo `echo $(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
    ```
    
    # [PowerShell](#tab/powershell)
    
    ```powershell
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}')))
    ```
    
    # [Azure CLI](#tab/cmd)
    
    ```azurecli
    az storage message get --queue-name outqueue -o tsv --query [].{Message:content} > %TEMP%out.b64 && certutil -decode -f %TEMP%out.b64 %TEMP%out.txt > NUL && type %TEMP%out.txt && del %TEMP%out.b64 %TEMP%out.txt /q
    ```

    This script uses certutil to decode the base64-encoded message collection from a local temp file. If there's no output, try removing `> NUL` from the script to stop suppressing certutil output, in case there's an error. 
    
    ---
    
    Because the message body is stored [base64 encoded](functions-bindings-storage-queue-trigger.md#encoding), the message must be decoded before it's displayed. After you execute `az storage message get`, the message is removed from the queue. If there was only one message in `outqueue`, you won't retrieve a message when you run this command a second time and instead get an error.

## Redeploy the project to Azure

Now that you've verified locally that the function wrote a message to the Azure Storage queue, you can redeploy your project to update the endpoint running on Azure.

1. In the *LocalFunctionsProj* folder, use the [`func azure functionapp publish`](functions-run-local.md#project-file-deployment) command to redeploy the project, replacing`<APP_NAME>` with the name of your app.

    ```
    func azure functionapp publish <APP_NAME>
    ```
    
1. As in the previous quickstart, use a browser or CURL to test the redeployed function.

    # [Browser](#tab/browser)
    
    Copy the complete **Invoke URL** shown in the output of the publish command into a browser address bar, appending the query parameter `&name=Functions`. The browser should display similar output as when you ran the function locally.

    ![The output of the function runs on Azure in a browser](./media/functions-add-output-binding-storage-queue-cli/function-test-cloud-browser.png)

    # [curl](#tab/curl)
    
    Run [`curl`](https://curl.haxx.se/) with the **Invoke URL**, appending the parameter `&name=Functions`. The output of the command should be the text, "Hello Functions."
    
    ![The output of the function runs on Azure using CURL](./media/functions-add-output-binding-storage-queue-cli/function-test-cloud-curl.png)

    --- 

1. Examine the Storage queue again, as described in the previous section, to verify that it contains the new message written to the queue.

## Clean up resources

After you've finished, use the following command to delete the resource group and all its contained resources to avoid incurring further costs.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## Next steps

You've updated your HTTP triggered function to write data to a Storage queue. Now you can learn more about developing Functions from the command line using Core Tools and Azure CLI:

+ [Work with Azure Functions Core Tools](functions-run-local.md)  

::: zone pivot="programming-language-csharp"  
+ [Examples of complete Function projects in C#](/samples/browse/?products=azure-functions&languages=csharp).

+ [Azure Functions C# developer reference](functions-dotnet-class-library.md)  
::: zone-end 
::: zone pivot="programming-language-javascript"  
+ [Examples of complete Function projects in JavaScript](/samples/browse/?products=azure-functions&languages=javascript).

+ [Azure Functions JavaScript developer guide](functions-reference-node.md)  
::: zone-end  
::: zone pivot="programming-language-typescript"  
+ [Examples of complete Function projects in TypeScript](/samples/browse/?products=azure-functions&languages=typescript).

+ [Azure Functions TypeScript developer guide](functions-reference-node.md#typescript)  
::: zone-end  
::: zone pivot="programming-language-python"  
+ [Examples of complete Function projects in Python](/samples/browse/?products=azure-functions&languages=python).

+ [Azure Functions Python developer guide](functions-reference-python.md)  
::: zone-end  
::: zone pivot="programming-language-powershell"  
+ [Examples of complete Function projects in PowerShell](/samples/browse/?products=azure-functions&languages=azurepowershell).

+ [Azure Functions PowerShell developer guide](functions-reference-powershell.md) 
::: zone-end
+ [Azure Functions triggers and bindings](functions-triggers-bindings.md)

+ [Functions pricing page](https://azure.microsoft.com/pricing/details/functions/)

+ [Estimating Consumption plan costs](functions-consumption-costs.md) 
