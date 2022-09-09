---
title: Add and run code in Azure Logic Apps with Azure Functions
description: Add and run code in Azure Logic Apps with Azure Functions
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.topic: article
ms.date: 08/20/2018
ms.reviewer: klam, LADocs
---

# Add and run code by using Azure Functions in Azure Logic Apps

When you want to run code that performs a specific job in your logic apps, 
you can create your own functions with [Azure Functions](../azure-functions/functions-overview.md). 
This service helps you create Node.js, C#, and F# code so you don't 
have to build a complete app or the infrastructure for running your code. 
You can also [call logic apps from inside Azure functions](#call-logic-app).
Azure Functions provides serverless computing in the cloud 
and is useful for performing tasks such as these examples:

* Extend your logic app's behavior with functions in Node.js or C#.
* Perform calculations in your logic app workflow.
* Apply advanced formatting or compute fields in your logic apps.

To run code snippets without creating Azure functions, 
learn how to [add and run inline code](../logic-apps/logic-apps-add-run-inline-code.md).

## Prerequisites

* An Azure subscription. If you don't have an Azure subscription, 
[sign up for a free Azure account](https://azure.microsoft.com/free/).

* An Azure function app, which is a container for Azure functions, 
and your Azure function. If you don't have a function app, 
[create your function app first](../azure-functions/functions-create-first-azure-function.md). 
You can then create your function either [separately outside your logic app](#create-function-external), 
or [from inside your logic app](#create-function-designer) in the Logic App Designer.

  Both existing and new function apps and functions 
  have the same requirements for working with logic apps:

  * Your function app must have the same Azure subscription as your logic app.

  * Your function uses an HTTP trigger, for example, 
  the **HTTP trigger** function template for **JavaScript** or **C#**. 

    The HTTP trigger template can accept content that has 
    `application/json` type from your logic app. 
    When you add an Azure function to your logic app, 
    the Logic App Designer shows custom functions created 
    from this template within your Azure subscription. 

  * Your function doesn't use custom routes unless you've defined an 
  [OpenAPI definition](../azure-functions/functions-openapi-definition.md), 
  formerly known as a [Swagger file](https://swagger.io/). 
  
  * If you've defined an OpenAPI definition for your function, 
  the Logic Apps Designer gives you a richer experience for 
  working with function parameters. Before your logic app can 
  find and access functions that have OpenAPI definitions, 
  [set up your function app by following these steps](#function-swagger).

* The logic app where you want to add the function, including a 
[trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) 
as the first step in your logic app 

  Before you can add actions that can run functions, 
  your logic app must start with a trigger.

  If you're new to logic apps, review 
  [What is Azure Logic Apps](../logic-apps/logic-apps-overview.md) 
  and [Quickstart: Create your first logic app](../logic-apps/quickstart-create-first-logic-app-workflow.md).

> [!NOTE]
> Logic apps integration with Functions does not work when slots (preview) are enabled.

<a name="create-function-external"></a>

## Create functions outside logic apps

In the [Azure portal](https://portal.azure.com), 
create your Azure function app, which must have the same Azure subscription 
as your logic app, and then create your Azure function.
If you're new to creating Azure functions, learn how to 
[create your first function in the Azure portal](../azure-functions/functions-create-first-azure-function.md), 
but note these requirements for creating functions that you can call from logic apps:

* Make sure you select the **HTTP trigger** function 
template for either **JavaScript** or **C#**.

  ![HTTP trigger - JavaScript or C#](./media/logic-apps-azure-functions/http-trigger-function.png)

<a name="function-swagger"></a>

* Optionally, if you [generate an API definition](../azure-functions/functions-openapi-definition.md), 
formerly known as a [Swagger file](https://swagger.io/), for your function, 
you can get a richer experience when you work with function parameters 
in the Logic Apps Designer. To set up your function app so your logic 
app can find and use functions that have Swagger descriptions, 
follow these steps:

  1. Make sure your function app is actively running.

  2. In your function app, set up [Cross-Origin Resource Sharing (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) so all 
  origins are permitted by following these steps:

     1. From the **Function Apps** list, 
     select your function app > **Platform features** > **CORS**.

        ![Select your function app > "Platform features" > "CORS"](./media/logic-apps-azure-functions/function-platform-features-cors.png)

     2. Under **CORS**, add the `*` wildcard character, 
     but remove all the other origins in the list, and choose **Save**.

        ![Set "CORS* to the wildcard character "*"](./media/logic-apps-azure-functions/function-platform-features-cors-origins.png)

### Access property values inside HTTP requests

Webhook functions can accept HTTP requests as inputs and pass those 
requests to other functions. For example, although Logic Apps has 
[functions that convert DateTime values](../logic-apps/workflow-definition-language-functions-reference.md), 
this basic sample JavaScript function shows how you can access a property 
inside a request object that's passed to the function and perform operations 
on that property value. To access properties inside objects, this example uses the 
[dot (.) operator](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/Property_accessors): 

```javascript
function convertToDateString(request, response){
   var data = request.body;
   response = {
      body: data.date.ToDateString();
   }
}
```

Here's what happens inside this function:

1. The function creates a `data` variable and assigns the 
`body` object inside the `request` object to that variable. 
The function uses the dot (.) operator to reference the 
`body` object inside the `request` object: 

   ```javascript
   var data = request.body;
   ```

2. The function can now access the `date` property 
through the `data` variable, and convert that property 
value from DateTime type to DateString type by calling 
the `ToDateString()` function. The function also returns 
the result through the `body` property in the function's response: 

   ```javascript
   body: data.date.ToDateString();
   ```

Now that you've created your Azure function, 
follow the steps for how to 
[add functions to logic apps](#add-function-logic-app).

<a name="create-function-designer"></a>

## Create functions inside logic apps

Before you can create an Azure function starting 
from inside your logic app in the Logic App Designer, 
you must first have an Azure function app, 
which is a container for your functions. 
If you don't have a function app, 
create that function app first. See 
[Create your first function in the Azure portal](../azure-functions/functions-create-first-azure-function.md). 

1. In the [Azure portal](https://portal.azure.com), 
open your logic app in the Logic App Designer. 

2. To create and add your function, 
follow the step that applies to your scenario:

   * Under the last step in your logic app's workflow, choose **New step**.

   * Between existing steps in your logic app's workflow, 
   move your mouse over the arrow, choose the plus (+) sign, 
   and then select **Add an action**.

3. In the search box, enter "azure functions" as your filter.
From the actions list, select this action: 
**Choose an Azure function - Azure Functions** 

   ![Find "Azure functions"](./media/logic-apps-azure-functions/find-azure-functions-action.png)

4. From the function apps list, select your function app. 
After the actions list opens, select this action: 
**Azure Functions - Create New Function**

   ![Select your function app](./media/logic-apps-azure-functions/select-function-app-create-function.png)

5. In the function definition editor, define your function:

   1. In the **Function name** box, 
   provide a name for your function. 

   2. In the **Code** box, add your code to the function template, 
   including the response and payload you want returned to your 
   logic app after your function finishes running. 

      ![Define your function](./media/logic-apps-azure-functions/function-definition.png)

      In the template's code, the *`context` object* refers 
      to the message that your logic app sends through 
      the **Request Body** field in a later step. 
      To access the `context` object's properties 
      from inside your function, use this syntax: 

      `context.body.<property-name>`

      For example, to reference the `content` property 
      inside the `context` object, use this syntax: 

      `context.body.content`

      The template code also includes an `input` variable, 
      which stores the value from the `data` parameter 
      so your function can perform operations on that value. 
      Inside JavaScript functions, the `data` variable 
      is also a shortcut for `context.body`.

      > [!NOTE]
      > The `body` property here applies to the `context` object and 
      > isn't the same as the **Body** token from an action's output, 
      > which you might also pass to your function. 
 
   3. When you're done, choose **Create**.

6. In the **Request Body** box, provide your function's input, 
which must be formatted as a JavaScript Object Notation (JSON) object. 

   This input is the *context object* or message that your logic app 
   sends to your function. When you click in the **Request Body** field, 
   the dynamic content list appears so you can select tokens for outputs 
   from previous steps. This example specifies that the context payload 
   contains a property named `content` that has the **From** token's 
   value from the email trigger:

   !["Request Body" example - context object payload](./media/logic-apps-azure-functions/function-request-body-example.png)

   Here, the context object isn't cast as a string, so the object's 
   content gets added directly to the JSON payload. However, when the 
   context object isn't a JSON token that passes a string, a JSON object, 
   or a JSON array, you get an error. So, if this example used the 
   **Received Time** token instead, you can cast the context 
   object as a string by adding double-quotation marks:  

   ![Cast object as string](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

7. To specify other details such as the method to use, 
request headers, or query parameters, choose **Show advanced options**.

<a name="add-function-logic-app"></a>

## Add existing functions to logic apps

To call existing Azure functions from your logic apps, 
you can add Azure functions like any other action 
in the Logic App Designer. 

1. In the [Azure portal](https://portal.azure.com), 
open your logic app in the Logic App Designer. 

2. Under the step where you want to add the function, 
choose **New step** > **Add an action**. 

3. In the search box, enter "azure functions" as your filter.
From the actions list, select this action: 
**Choose an Azure function - Azure Functions** 

   ![Find "Azure functions"](./media/logic-apps-azure-functions/find-azure-functions-action.png)

4. From the function apps list, select your function app. 
After the functions list appears, select your function. 

   ![Select your function app and Azure function](./media/logic-apps-azure-functions/select-function-app-existing-function.png)

   For functions that have API definitions (Swagger descriptions) and are 
   [set up so your logic app can find and access those functions](#function-swagger), 
   you can select **Swagger actions**:

   ![Select your function app, "Swagger actions"", and your Azure function](./media/logic-apps-azure-functions/select-function-app-existing-function-swagger.png)

5. In the **Request Body** box, provide your function's input, 
which must be formatted as a JavaScript Object Notation (JSON) object. 

   This input is the *context object* or message that your logic app 
   sends to your function. When you click in the **Request Body** field, 
   the dynamic content list appears so you can select tokens for outputs 
   from previous steps. This example specifies that the context payload 
   contains a property named `content` that has the **From** token's 
   value from the email trigger:

   !["Request Body" example - context object payload](./media/logic-apps-azure-functions/function-request-body-example.png)

   Here, the context object isn't cast as a string, so the object's 
   content gets added directly to the JSON payload. However, when the 
   context object isn't a JSON token that passes a string, a JSON object, 
   or a JSON array, you get an error. So, if this example used the 
   **Received Time** token instead, you can cast the context 
   object as a string by adding double-quotation marks: 

   ![Cast object as string](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

6. To specify other details such as the method to use, 
request headers, or query parameters, choose **Show advanced options**.

<a name="call-logic-app"></a>

## Call logic apps from functions

When you want to trigger a logic app from inside an Azure function, 
the logic app must start with a trigger that provides a callable endpoint. 
For example, you can start the logic app with the **HTTP**, **Request**, 
**Azure Queues**, or **Event Grid** trigger. Inside your function, 
send an HTTP POST request to the trigger's URL, and include the 
payload you want that logic app to process. For more information, see 
[Call, trigger, or nest logic apps](../logic-apps/logic-apps-http-endpoint.md). 

## Get support

* For questions, visit the [Azure Logic Apps forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* To submit or vote on feature ideas, visit the [Logic Apps user feedback site](https://aka.ms/logicapps-wish).

## Next steps

* Learn about [Logic Apps connectors](../connectors/apis-list.md)
