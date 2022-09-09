---
title: "Quickstart: Computer Vision client library for Java"
description: In this quickstart, get started with the Computer Vision client library for Java.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 10/13/2019
ms.custom: devx-track-java
ms.author: pafarley
---

<a name="HOLTop"></a>

[Reference documentation](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/computervision?view=azure-java-stable) | [Library source code](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-computervision) |[Artifact (Maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-computervision) | [Samples](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## Prerequisites

* An Azure subscription - [Create one for free](https://azure.microsoft.com/free/cognitive-services/)
* The current version of the [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* The [Gradle build tool](https://gradle.org/install/), or another dependency manager.
* Once you have your Azure subscription, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Create a Computer Vision resource"  target="_blank">create a Computer Vision resource <span class="docon docon-navigate-external x-hidden-focus"></span></a> in the Azure portal to get your key and endpoint. After it deploys, click **Go to resource**.
    * You will need the key and endpoint from the resource you create to connect your application to the Computer Vision service. You'll paste your key and endpoint into the code below later in the quickstart.
    * You can use the free pricing tier (`F0`) to try the service, and upgrade later to a paid tier for production.

## Setting up

### Create a new Gradle project

In a console window (such as cmd, PowerShell, or Bash), create a new directory for your app, and navigate to it. 

```console
mkdir myapp && cd myapp
```

Run the `gradle init` command from your working directory. This command will create essential build files for Gradle, including *build.gradle.kts*, which is used at runtime to create and configure your application.

```console
gradle init --type basic
```

When prompted to choose a **DSL**, select **Kotlin**.

### Install the client library

This quickstart uses the Gradle dependency manager. You can find the client library and information for other dependency managers on the [Maven Central Repository](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-computervision).

Locate *build.gradle.kts* and open it with your preferred IDE or text editor. Then copy in the following build configuration. This configuration defines the project as a Java application whose entry point is the class **ComputerVisionQuickstarts**. It imports the Computer Vision library.

```kotlin
plugins {
    java
    application
}
application { 
    mainClassName = "ComputerVisionQuickstarts"
}
repositories {
    mavenCentral()
}
dependencies {
    compile(group = "com.microsoft.azure.cognitiveservices", name = "azure-cognitiveservices-computervision", version = "1.0.4-beta")
}
```

### Create a Java file

From your working directory, run the following command to create a project source folder:

```console
mkdir -p src/main/java
```

Navigate to the new folder and create a file called *ComputerVisionQuickstarts.java*. Open it in your preferred editor or IDE and add the following `import` statements:

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_imports)]

> [!TIP]
> Want to view the whole quickstart code file at once? You can find it on [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java), which contains the code examples in this quickstart.

In the application's **ComputerVisionQuickstarts** class, create variables for your resource's key and endpoint.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_creds)]


> [!IMPORTANT]
> Go to the Azure portal. If the [Product name] resource you created in the **Prerequisites** section deployed successfully, click the **Go to Resource** button under **Next Steps**. You can find your key and endpoint in the resource's **key and endpoint** page, under **resource management**. 
>
> Remember to remove the key from your code when you're done, and never post it publicly. For production, consider using a secure way of storing and accessing your credentials. See the Cognitive Services [security](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-security) article for more information.

In the application's **main** method, add calls for the methods used in this quickstart. You'll define these later.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_maincalls)]


## Object model

The following classes and interfaces handle some of the major features of the Computer Vision Java SDK.

|Name|Description|
|---|---|
| [ComputerVisionClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-java-stable) | This class is needed for all Computer Vision functionality. You instantiate it with your subscription information, and you use it to produce instances of other classes.|
|[ComputerVision](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervision?view=azure-java-stable)| This class comes from the client object and directly handles all of the image operations, such as image analysis, text detection, and thumbnail generation.|
|[VisualFeatureTypes](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-java-stable)| This enum defines the different types of image analysis that can be done in a standard Analyze operation. You specify a set of VisualFeatureTypes values depending on your needs. |

## Code examples

These code snippets show you how to do the following tasks with the Computer Vision client library for Java:

* [Authenticate the client](#authenticate-the-client)
* [Analyze an image](#analyze-an-image)
* [Read printed and handwritten text](#read-printed-and-handwritten-text)

## Authenticate the client


In a new method, instantiate a [ComputerVisionClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-java-stable) object with your endpoint and key.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_auth)]


## Analyze an image

The following code defines a method, `AnalyzeLocalImage`, which uses the client object to analyze a local image and print the results. The method returns a text description, categorization, list of tags, detected faces, adult content flags, main colors, and image type.

> [!TIP]
> You can also analyze a remote image using its URL. See the [ComputerVision](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervision?view=azure-java-stable) methods, such as **AnalyzeImage**. Or, see the sample code on [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java) for scenarios involving remote images.

### Set up test image

First, create a **resources/** folder in the **src/main/** folder of your project, and add an image you'd like to analyze. Then add the following method definition to your **ComputerVisionQuickstarts** class. Change the value of the `pathToLocalImage` to match your image file. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_refs)]

### Specify visual features

Next, specify which visual features you'd like to extract in your analysis. See the [VisualFeatureTypes](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-java-stable) enum for a complete list.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_features)]

### Analyze
This method prints detailed results to the console for each scope of image analysis. We recommend you surround this method call in a Try/Catch block. The **analyzeImageInStream** method returns an **ImageAnalysis** object that contains all of extracted information.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_analyze)]

The following sections show how to parse this information in detail.

### Get image description

The following code gets the list of generated captions for the image. For more information, see [Describe images](../../concept-describing-images.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_captions)]

### Get image category

The following code gets the detected category of the image. For more information, see [Categorize images](../../concept-categorizing-images.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_category)]

### Get image tags

The following code gets the set of detected tags in the image. For more information, see [Content tags](../../concept-tagging-images.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_tags)]

### Detect faces

The following code returns the detected faces in the image with their rectangle coordinates and selects face attributes. For more information, see [Face detection](../../concept-detecting-faces.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_faces)]

### Detect adult, racy, or gory content

The following code prints the detected presence of adult content in the image. For more information, see [Adult, racy, gory content](../../concept-detecting-adult-content.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_adult)]

### Get image color scheme

The following code prints the detected color attributes in the image, like the dominant colors and accent color. For more information, see [Color schemes](../../concept-detecting-color-schemes.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_colors)]

### Get domain-specific content

Computer Vision can use specialized model to do further analysis on images. For more information, see [Domain-specific content](../../concept-detecting-domain-content.md). 

The following code parses data about detected celebrities in the image.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_celebrities)]

The following code parses data about detected landmarks in the image.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_landmarks)]

### Get the image type

The following code prints information about the type of image&mdash;whether it is clip art or line drawing.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_imagetype)]

## Read printed and handwritten text

Computer Vision can read visible text in an image and convert it to a character stream. This section defines a method, `ReadFromFile`, that takes a local file path and prints the image's text to the console.

> [!TIP]
> You can also read text in a remote image referenced by URL. See the [ComputerVision](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervision?view=azure-java-stable) methods, such as **read**. Or, see the sample code on [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java) for scenarios involving remote images.

### Set up test image

Create a **resources/** folder in the **src/main/** folder of your project, and add an image you'd like to read text from. You can download a [sample image](https://raw.githubusercontent.com/MicrosoftDocs/azure-docs/master/articles/cognitive-services/Computer-vision/Images/readsample.jpg) to use here.

Then add the following method definition to your **ComputerVisionQuickstarts** class. Change the value of the `localFilePath` to match your image file. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_setup)]

### Call the Read API

Then, add the following code to call the **readInStreamWithServiceResponseAsync** method for the given image.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_call)]


The following block of code extracts the operation ID from the response of the Read call. It uses this ID with a helper method to print the text read results to the console. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_response)]

Close out the try/catch block and the method definition.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_catch)]

### Get Read results

Then, add a definition for the helper method. This method uses the operation ID from the previous step to query the read operation and get OCR results when they're available.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_result_helper_call)]

The rest of the method parses the OCR results and prints them to the console.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_result_helper_print)]

Finally, add the other helper method used above, which extracts the operation ID from the initial response.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_opid_extract)]

## Run the application

You can build the app with:

```console
gradle build
```

Run the application with the `gradle run` command:

```console
gradle run
```

## Clean up resources

If you want to clean up and remove a Cognitive Services subscription, you can delete the resource or resource group. Deleting the resource group also deletes any other resources associated with it.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## Next steps

In this quickstart, you learned how to use the Computer Vision Java library to do basis tasks. Next, explore the reference documentation to learn more about the library.

> [!div class="nextstepaction"]
>[Computer Vision reference (Java)](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/computervision?view=azure-java-stable)

* [What is Computer Vision?](../../overview.md)
* The source code for this sample can be found on [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java).
