---
title: "Quickstart: Computer Vision client library for .NET"
description: In this quickstart, get started with the Computer Vision client library for .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/05/2019
ms.author: pafarley
ms.custom: devx-track-csharp
---

<a name="HOLTop"></a>

[Reference documentation](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/computervision?view=azure-dotnet) | [Library source code](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ComputerVision) | [Package (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) | [Samples](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## Prerequisites

* An Azure subscription - [Create one for free](https://azure.microsoft.com/free/cognitive-services/)
* The [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) or current version of [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Once you have your Azure subscription, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Create a Computer Vision resource"  target="_blank">create a Computer Vision resource <span class="docon docon-navigate-external x-hidden-focus"></span></a> in the Azure portal to get your key and endpoint. After it deploys, click **Go to resource**.
    * You will need the key and endpoint from the resource you create to connect your application to the Computer Vision service. You'll paste your key and endpoint into the code below later in the quickstart.
    * You can use the free pricing tier (`F0`) to try the service, and upgrade later to a paid tier for production.

## Setting up

### Create a new C# application

#### [Visual Studio IDE](#tab/visual-studio)

Using Visual Studio, create a new .NET Core application. 

### Install the client library 

Once you've created a new project, install the client library by right-clicking on the project solution in the **Solution Explorer** and selecting **Manage NuGet Packages**. In the package manager that opens select **Browse**, check **Include prerelease**, and search for `Microsoft.Azure.CognitiveServices.Vision.ComputerVision`. Select version `6.0.0-preview.1`, and then **Install**. 

#### [CLI](#tab/cli)

In a console window (such as cmd, PowerShell, or Bash), use the `dotnet new` command to create a new console app with the name `computer-vision-quickstart`. This command creates a simple "Hello World" C# project with a single source file: *ComputerVisionQuickstart.cs*.

```console
dotnet new console -n (product-name)-quickstart
```

Change your directory to the newly created app folder. You can build the application with:

```console
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

### Install the client library

Within the application directory, install the Computer Vision client library for .NET with the following command:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.ComputerVision --version 6.0.0
```

---

> [!TIP]
> Want to view the whole quickstart code file at once? You can find it on [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs), which contains the code examples in this quickstart.

From the project directory, open the *ComputerVisionQuickstart.cs* file in your preferred editor or IDE. Add the following `using` directives:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_using)]

In the application's **Program** class, create variables for your resource's Azure endpoint and key.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_vars)]

> [!IMPORTANT]
> Go to the Azure portal. If the Computer Vision resource you created in the **Prerequisites** section deployed successfully, click the **Go to Resource** button under **Next Steps**. You can find your key and endpoint in the resource's **key and endpoint** page, under **resource management**. 
>
> Remember to remove the key from your code when you're done, and never post it publicly. For production, consider using a secure way of storing and accessing your credentials. See the Cognitive Services [security](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-security) article for more information.

In the application's `Main` method, add calls for the methods used in this quickstart. You will create these later.


[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_client)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_analyzeinmain)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_extracttextinmain)]

## Object model

The following classes and interfaces handle some of the major features of the Computer Vision .NET SDK.

|Name|Description|
|---|---|
| [ComputerVisionClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-dotnet) | This class is needed for all Computer Vision functionality. You instantiate it with your subscription information, and you use it to do most image operations.|
|[ComputerVisionClientExtensions](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclientextensions?view=azure-dotnet)| This class contains additional methods for the **ComputerVisionClient**.|
|[VisualFeatureTypes](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-dotnet)| This enum defines the different types of image analysis that can be done in a standard Analyze operation. You specify a set of VisualFeatureTypes values depending on your needs. |

## Code examples

These code snippets show you how to do the following tasks with the Computer Vision client library for .NET:

* [Authenticate the client](#authenticate-the-client)
* [Analyze an image](#analyze-an-image)
* [Read printed and handwritten text](#read-printed-and-handwritten-text)

## Authenticate the client

> [!NOTE]
> This quickstart assumes you've [created environment variables](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) for your Computer Vision key and endpoint, named `COMPUTER_VISION_SUBSCRIPTION_KEY` and `COMPUTER_VISION_ENDPOINT` respectively.

In a new method, instantiate a client with your endpoint and key. Create a **[ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.apikeyserviceclientcredentials?view=azure-dotnet)** object with your key, and use it with your endpoint to create a **[ComputerVisionClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-dotnet)** object.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_auth)]



## Analyze an image

The following code defines a method, `AnalyzeImageUrl`, which uses the client object to analyze a remote image and print the results. The method returns a text description, categorization, list of tags, detected faces, adult content flags, main colors, and image type.


### Set up test image

In your **Program** class, save a reference to the URL of the image you want to analyze.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_analyze_url)]

> [!NOTE]
> You can also analyze a local image. See the sample code on [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs) for scenarios involving local images.

### Specify visual features

Define your new method for image analysis. Add the code below, which specifies visual features you'd like to extract in your analysis. See the **[VisualFeatureTypes](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-dotnet)** enum for a complete list.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_visualfeatures)]

Insert any of the following code blocks into your **AnalyzeImageUrl** method to implement their features. Remember to add a closing bracket at the end.

```csharp
}
```

### Analyze

The **AnalyzeImageAsync** method returns an **ImageAnalysis** object that contains all of extracted information.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_analyze_call)]

The following sections show how to parse this information in detail.

### Get image description

The following code gets the list of generated captions for the image. See [Describe images](../../concept-describing-images.md) for more details.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_describe)]

### Get image category

The following code gets the detected category of the image. See [Categorize images](../../concept-categorizing-images.md) for more details.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_categorize)]

### Get image tags

The following code gets the set of detected tags in the image. See [Content tags](../../concept-tagging-images.md) for more details.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_tags)]

### Detect objects

The following code detects common objects in the image and prints them to the console. See [Object detection](../../concept-object-detection.md) for more details.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_objects)]

### Detect brands

The following code detects corporate brands and logos in the image and prints them to the console. See [Brand detection](../../concept-brand-detection.md) for more details.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_brands)]

### Detect faces

The following code returns the detected faces in the image with their rectangle coordinates and select face attributes. See [Face detection](../../concept-detecting-faces.md) for more details.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_faces)]

### Detect adult, racy, or gory content

The following code prints the detected presence of adult content in the image. See [Adult, racy, gory content](../../concept-detecting-adult-content.md) for more details.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_adult)]

### Get image color scheme

The following code prints the detected color attributes in the image, like the dominant colors and accent color. See [Color schemes](../../concept-detecting-color-schemes.md) for more details.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_color)]

### Get domain-specific content

Computer Vision can use specialized models to do further analysis on images. See [Domain-specific content](../../concept-detecting-domain-content.md) for more details. 

The following code parses data about detected celebrities in the image.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_celebs)]

The following code parses data about detected landmarks in the image.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_landmarks)]

### Get the image type

The following code prints information about the type of image&mdash;whether it is clip art or a line drawing.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_type)]

## Read printed and handwritten text

Computer Vision can read visible text in an image and convert it to a character stream. For more information on text recognition, see the [Optical character recognition (OCR)](../../concept-recognizing-text.md#read-api) conceptual doc. The code in this section uses the latest [Computer Vision SDK release for Read 3.0](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/6.0.0-preview.1) and defines a method, `BatchReadFileUrl`, which uses the client object to detect and extract text in the image.


### Set up test image

In your **Program** class, save a reference to the URL of the image you want to extract text from. This snippet includes sample images for both printed and handwritten text.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readtext_url)]

> [!NOTE]
> You can also extract text from a local image. See the sample code on [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs) for scenarios involving local images.

### Call the Read API

Define the new method for reading text. Add the code below, which calls the **ReadAsync** method for the given image. This returns an operation ID and starts an asynchronous process to read the content of the image.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_read_url)]

### Get Read results

Next, get the operation ID returned from the **ReadAsync** call, and use it to query the service for operation results. The following code checks the operation until the results are returned. It then prints the extracted text data to the console.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_read_response)]

### Display Read results

Add the following code to parse and display the retrieved text data, and finish the method definition.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_read_display)]

## Run the application

#### [Visual Studio IDE](#tab/visual-studio)

Run the application by clicking the **Debug** button at the top of the IDE window.

#### [CLI](#tab/cli)

Run the application from your application directory with the `dotnet run` command.

```dotnet
dotnet run
```

---

## Clean up resources

If you want to clean up and remove a Cognitive Services subscription, you can delete the resource or resource group. Deleting the resource group also deletes any other resources associated with it.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## Next steps

> [!div class="nextstepaction"]
>[Computer Vision API reference (.NET)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/computervision?view=azure-dotnet)

* [What is Computer Vision?](../../overview.md)
* The source code for this sample can be found on [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs).
