---
title: include file
description: include file
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 07/28/2020
ms.topic: include
ms.custom: include file
ms.author: diberry
---


Use the Language Understanding (LUIS) prediction client library for .NET to:

* Get prediction by slot
* Prediction by Version

[Reference documentation](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet) | [Library source code](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Runtime) | [Prediction runtime Package (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/) | [C# Samples](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/LanguageUnderstanding/predict-with-sdk-3x)

## Prerequisites

* Language Understanding (LUIS) portal account - [Create one for free](https://www.luis.ai)
* The current version of [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* A LUIS app ID - use the public IoT app ID of `df67dcdb-c37d-46af-88e1-8b97951ca1c2`. The user query used in the quickstart code is specific to that app.

## Setting up

### Create a new C# application

Create a new .NET Core application in your preferred editor or IDE.

1. In a console window (such as cmd, PowerShell, or Bash), use the dotnet `new` command to create a new console app with the name `language-understanding-quickstart`. This command creates a simple "Hello World" C# project with a single source file: `Program.cs`.

    ```dotnetcli
    dotnet new console -n language-understanding-quickstart
    ```

1. Change your directory to the newly created app folder.

1. You can build the application with:

    ```dotnetcli
    dotnet build
    ```

    The build output should contain no warnings or errors.

    ```console
    ...
    Build succeeded.
     0 Warning(s)
     0 Error(s)
    ...
    ```

### Install the SDK

Within the application directory, install the Language Understanding (LUIS) prediction runtime client library for .NET with the following command:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime --version 3.0.0
```

If you're using the Visual Studio IDE, the client library is available as a downloadable NuGet package.

## Object model

The Language Understanding (LUIS) prediction runtime client is a [LUISRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-dotnet) object that authenticates to Azure, which contains your resource key.

Once the client is created, use this client to access functionality including:

* Prediction by [staging or product slot](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getslotpredictionasync?view=azure-dotnet)
* Prediction by [version](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getversionpredictionasync?view=azure-dotnet)


## Code examples

These code snippets show you how to do the following with the Language Understanding (LUIS) prediction runtime client library for .NET:

* [Prediction by slot](#get-prediction-from-runtime)

## Add the dependencies

From the project directory, open the *Program.cs* file in your preferred editor or IDE. Replace the existing `using` code with the following `using` directives:

[!code-csharp[Using statements](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_using)]

## Authenticate the client

1. Create variables for the key, resource name, app ID, and publishing slot. Set the app ID to the public IoT app:

    **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`**

    [!code-csharp[Create variables](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_variables)]

1. Create an [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.apikeyserviceclientcredentials?view=azure-dotnet) object with your key, and use it with your endpoint to create an [LUISRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-dotnet) object.

    [!code-csharp[Create LUIS client object](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_create_client)]

## Get prediction from runtime

Add the following method to create the request to the prediction runtime.

The user utterance is part of the [PredictionRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.models.predictionrequest?view=azure-dotnet) object.

The **GetSlotPredictionAsync** method needs several parameters such as the app ID, the slot name, the prediction request object to fulfill the request. The other options such as verbose, show all intents, and log are optional.

[!code-csharp[Create method to get prediction runtime](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_maintask)]

## Main code for the prediction

Use the following main method to tie the variables and methods together to get the prediction.

[!code-csharp[Create method to get prediction runtime](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_main)]

## Run the application

Run the application with the `dotnet run` command from your application directory.

```dotnetcli
dotnet run
```

## Clean up resources

When you are done with your predictions, clean up the work from this quickstart by deleting the program.cs file and its subdirectories.
