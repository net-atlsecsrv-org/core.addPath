---
title: 'Quickstart: Create an Object Anchors model to be used in an app'
description: In this quickstart, you learn how to create an Object Anchors model from a 3D model.
author: craigktreasure
manager: virivera

ms.author: crtreasu
ms.date: 02/22/2021
ms.topic: quickstart
ms.service: azure-object-anchors
---
# Quickstart: Create an Object Anchors model from a 3D model

Azure Object Anchors is a managed cloud service that converts 3D models into AI models that enable object-aware mixed reality experiences for the HoloLens. This quickstart covers how to create an Object Anchors model from a 3D model using the C#/.NET Core SDK.

You'll learn how to:

> [!div class="checklist"]
> * Create an Object Anchors account
> * Convert a 3D model to create an Object Anchors model

## Prerequisites

To complete this quickstart, make sure you have:

* A Windows machine with <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a>.
* <a href="https://git-scm.com" target="_blank">Git for Windows</a>.
* The <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1">.NET Core 3.1 SDK</a>.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## Create an Object Anchors account

First, you need to create an account with the Object Anchors service.

1. Go to the [Azure portal](https://portal.azure.com/) and select **Create a resource**.

   :::image type="content" source="./media/create-aoa-resource-1.png" alt-text="Create a new resource":::

2. Search for the **Object Anchors** resource.

   Search for "Object Anchors".

   :::image type="content" source="./media/create-aoa-resource-2.png" alt-text="Select the Object Anchors Resource":::

   On the **Object Anchors** resource in the search results, select **Create -> Object Anchors**.

   :::image type="content" source="./media/create-aoa-resource-3.png" alt-text="Create an Object Anchors Resource":::

3. In the **Object Anchors Account** dialog box:
    * Enter a unique resource name.
    * Select the subscription you want to attach the resource to.
    * Create or use an existing resource group.
    * Select the region you'd like your resource to exist in.

    :::image type="content" source="./media/create-aoa-resource-4.png" alt-text="Enter Object Anchors resource account details":::

    Select **Create** to begin creating the resource.

4. Once the resource has been created, select **Go to resource**.

   :::image type="content" source="./media/create-aoa-resource-5.png" alt-text="Go to resource":::

5. On the overview page:

   Take note of the **Account Domain**. You'll need it later.

   :::image type="content" source="./media/create-aoa-resource-6.1.png" alt-text="Copy the account domain for your Object Anchors resource":::

   Take note of the **Account ID**. You'll need it later.

   :::image type="content" source="./media/create-aoa-resource-6.2.png" alt-text="Copy the account ID for your Object Anchors resource":::

   Go to the **Access Keys** page and take note of the **Primary key**. You'll need it later.

   :::image type="content" source="./media/create-aoa-resource-7.png" alt-text="Copy the account key for your Object Anchors resource":::

## Get the sample project

[!INCLUDE [Clone Sample Repo](../../../includes/object-anchors-clone-sample-repository.md)]

## Convert a 3D model

Now, you can go ahead and convert your 3D model.

1. Open `quickstarts/conversion/Conversion.sln` in Visual Studio. This solution contains a C# console project.

2. Open the `Configuration.cs` file located in the root of the project and replace the `set-me` values on following fields:

   | Field         | Description                                                         |
   |---------------|---------------------------------------------------------------------|
   | AccountDomain | The **Account Domain** of the Object Anchors account created above. |
   | AccountId     | The **Account ID** of the Object Anchors account created above.     |
   | AccountKey    | The **Primary key** of the Object Anchors account created above     |

   There are four additional fields that need to be verified:

    | Field                    | Description                       |
    | ---                      | ---                               |
    | InputAssetPath           | The absolute path to a 3D model on your local machine. Supported file formats are `fbx`, `ply`, `obj`, `glb`, and `gltf`. |
    | AssetDimensionUnit       | The unit of measurement of your 3D model. All the supported units of measurement can be accessed using the `Azure.MixedReality.ObjectAnchors.Conversion.AssetLengthUnit` enumeration. |
    | Gravity                  | The direction of the gravity vector of the 3D model. This 3D vector gives the downward direction in the coordinate system of your model. For example if negative `y` represents the downward direction in the model's 3D space, this value would be `Vector3(0.0f, -1.0f, 0.0f)`. |

3. Build and run the project to upload your 3D model, register a new conversion job with the service, and wait for it to be completed. Once the job is completed, the Object Anchors model will be downloaded next to the file specified in the `InputAssetPath`. You should see something similar to the following console output:

   ```shell
    Asset   : ***********
    Gravity : ***********
    Unit    : ***********
    Attempting to upload asset...
    Attempting to create asset conversion job...
    Successfully created asset conversion job. Job ID: ***********
    Waiting for job completion...

    Asset conversion job completed successfully.
    Attempting to download result as '***********'...
    Success!
   ```

   Make a note of the **Job ID** for future reference. It may be useful when debugging or troubleshooting.

4. Once the job is completed successfully, you should see a file with the format `<Model-Filename-Without-Extension>_<JobID>.ou` in the specified output location. For example, if your 3D model filename is `chair.ply` and your job ID is `00000000-0000-0000-0000-000000000000` then the filename the service outputs will be `chair_00000000-0000-0000-0000-000000000000.ou`.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## Next steps

In this quickstart, you created an Object Anchors account and converted a 3D model to create an Object Anchors model. To learn how to integrate that model with the Object Anchors SDK in your mixed reality app, continue with any of the following articles:

> [!div class="nextstepaction"]
> [Unity HoloLens](get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [Unity HoloLens with MRTK](get-started-unity-hololens-mrtk.md)

> [!div class="nextstepaction"]
> [HoloLens DirectX](get-started-hololens-directx.md)
