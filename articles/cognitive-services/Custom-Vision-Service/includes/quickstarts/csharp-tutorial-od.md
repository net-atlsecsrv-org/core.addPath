---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 09/15/2020
---

Get started with the Custom Vision client library for .NET. Follow these steps to install the package and try out the example code for building an object detection model. You'll create a project, add tags, train the project on sample images, and use the project's prediction endpoint URL to programmatically test it. Use this example as a template for building your own image recognition app.

> [!NOTE]
> If you want to build and train an object detection model _without_ writing code, see the [browser-based guidance](../../get-started-build-detector.md) instead.

Use the Custom Vision client library for .NET to:

* Create a new Custom Vision project
* Add tags to the project
* Upload and tag images
* Train the project
* Test the prediction endpoint

[Reference documentation](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/customvision?view=azure-dotnet) | Library source code [(training)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.CustomVision.Training) [(prediction)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.CustomVision.Prediction) | Package (NuGet) [(training)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/) [(prediction)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/) | [Samples](https://docs.microsoft.com/samples/browse/?products=azure&term=vision&terms=vision)

## Prerequisites

* Azure subscription - [Create one for free](https://azure.microsoft.com/free/cognitive-services/)
* The [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) or current version of [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Once you have your Azure subscription, <a href="https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision"  title="Create a Custom Vision resource"  target="_blank">create a Custom Vision resource <span class="docon docon-navigate-external x-hidden-focus"></span></a> in the Azure portal to create a training and prediction resource and get your keys and endpoint. Wait for it to deploy and click the **Go to resource** button.
    * You will need the key and endpoint from the resources you create to connect your application to Custom Vision. You'll paste your key and endpoint into the code below later in the quickstart.
    * You can use the free pricing tier (`F0`) to try the service, and upgrade later to a paid tier for production.

## Setting up

### Create a new C# application

#### [Visual Studio IDE](#tab/visual-studio)

Using Visual Studio, create a new .NET Core application. 

### Install the client library 

Once you've created a new project, install the client library by right-clicking on the project solution in the **Solution Explorer** and selecting **Manage NuGet Packages**. In the package manager that opens select **Browse**, check **Include prerelease**, and search for `Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training` and `Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction`. Select the latest version and then **Install**. 

#### [CLI](#tab/cli)

In a console window (such as cmd, PowerShell, or Bash), use the `dotnet new` command to create a new console app with the name `custom-vision-quickstart`. This command creates a simple "Hello World" C# project with a single source file: *program.cs*. 

```console
dotnet new console -n custom-vision-quickstart
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

Within the application directory, install the Custom Vision client library for .NET with the following command:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training --version 2.0.0
dotnet add package Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction --version 2.0.0
```

---

> [!TIP]
> Want to view the whole quickstart code file at once? You can find it on [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs), which contains the code examples in this quickstart.

From the project directory, open the *program.cs* file and add the following `using` directives:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_imports)]

In the application's **Main** method, create variables for your resource's key and endpoint.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_creds)]

> [!IMPORTANT]
> Go to the Azure portal. If the Custom Vision resources you created in the **Prerequisites** section deployed successfully, click the **Go to Resource** button under **Next Steps**. You can find your keys and endpoint in the resources' **key and endpoint** pages, under **resource management**. You'll need to get both your training and prediction keys.
>
> Remember to remove the keys from your code when you're done, and never post them publicly. For production, consider using a secure way of storing and accessing your credentials. See the Cognitive Services [security](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-security) article for more information.

In the application's **Main** method, add calls for the methods used in this quickstart. You will implement these later.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_maincalls)]

## Object model

|Name|Description|
|---|---|
|[CustomVisionTrainingClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient?view=azure-dotnet) | This class handles the creation, training, and publishing of your models. |
|[CustomVisionPredictionClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient?view=azure-dotnet-preview)| This class handles the querying of your models for object detection predictions.|
|[PredictionModel](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.prediction.models.predictionmodel?view=azure-dotnet-preview)| This class defines a single object prediction on a single image. It includes properties for the object ID and name, the bounding box location of the object, and a confidence score.|

## Code examples

These code snippets show you how to do the following tasks with the Custom Vision client library for .NET:

* [Authenticate the client](#authenticate-the-client)
* [Create a new Custom Vision project](#create-a-new-custom-vision-project)
* [Add tags to the project](#add-tags-to-the-project)
* [Upload and tag images](#upload-and-tag-images)
* [Train the project](#train-the-project)
* [Publish the current iteration](#publish-the-current-iteration)
* [Test the prediction endpoint](#test-the-prediction-endpoint)

## Authenticate the client

In a new method, instantiate training and prediction clients using your endpoint and keys.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_auth)]

## Create a new Custom Vision project

This next bit of code creates an object detection project. The created project will show up on the [Custom Vision website](https://customvision.ai/). See the [CreateProject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__&preserve-view=true) method to specify other options when you create your project (explained in the [Build a detector](../../get-started-build-detector.md) web portal guide).  

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_create)]

## Add tags to the project

This method defines the tags that you will train the model on.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_tags)]

## Upload and tag images

First, download the sample images for this project. Save the contents of the [sample Images folder](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ObjectDetection/Images) to your local device.

When you tag images in object detection projects, you need to specify the region of each tagged object using normalized coordinates. The following code associates each of the sample images with its tagged region.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_upload_regions)]

> [!NOTE]
> For your own projects, if you don't have a click-and-drag utility to mark the coordinates of regions, you can use the web UI at the [Custom Vision website](https://www.customvision.ai/). In this example, the coordinates are already provided.

Then, this map of associations is used to upload each sample image with its region coordinates. You can upload up to 64 images in a single batch. You may need to change the `imagePath` value to point to the correct folder locations.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_upload)]

At this point, you've uploaded all the samples images and tagged each one (**fork** or **scissors**) with an associated pixel rectangle.

## Train the project

This method creates the first training iteration in the project. It queries the service until training is completed.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_train)]

> [!TIP]
> Train with selected tags
>
> You can optionally train on only a subset of your applied tags. You may want to do this if you haven't applied enough of certain tags yet, but you do have enough of others. In the [TrainProject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.trainproject?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_TrainProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_Guid_System_String_System_Nullable_System_Int32__System_Nullable_System_Boolean__System_String_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_Models_TrainingParameters_&preserve-view=true) call, use the *trainingParameters* parameter. Construct a [TrainingParameters](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.models.trainingparameters?view=azure-dotnet&preserve-view=true) and set its **SelectedTags** property to a list of IDs of the tags you want to use. The model will train to only recognize the tags on that list.

## Publish the current iteration

This method makes the current iteration of the model available for querying. You can use the model name as a reference to send prediction requests. You need to enter your own value for `predictionResourceId`. You can find the prediction resource ID on the resource's **Overview** tab in the Azure portal, listed as **Subscription ID**.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_publish)]


## Test the prediction endpoint

This part of the script loads the test image, queries the model endpoint, and outputs prediction data to the console.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_prediction)]

## Run the application

#### [Visual Studio IDE](#tab/visual-studio)

Run the application by clicking the **Debug** button at the top of the IDE window.

#### [CLI](#tab/cli)

Run the application from your application directory with the `dotnet run` command.

```dotnet
dotnet run
```

---

As the application runs, it should open a console window and write the following output:

```console
Creating new project:
        Training
Done!

Making a prediction:
        fork: 98.2% [ 0.111609578, 0.184719115, 0.6607002, 0.6637112 ]
        scissors: 1.2% [ 0.112389535, 0.119195729, 0.658031344, 0.7023591 ]
```

You can then verify that the test image (found in **Images/Test/**) is tagged appropriately and that the region of detection is correct. At this point, you can press any key to exit the application.

[!INCLUDE [clean-od-project](../../includes/clean-od-project.md)]

## Next steps

Now you've done every step of the object detection process in code. This sample executes a single training iteration, but often you'll need to train and test your model multiple times in order to make it more accurate. The following guide deals with image classification, but its principles are similar to object detection.

> [!div class="nextstepaction"]
> [Test and retrain a model](../../test-your-model.md)

* [What is Custom Vision?](../../overview.md)
* [SDK reference documentation](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/customvision?view=azure-dotnet)