---
title: 'Quickstart: Create a HoloLens app with Unity'
description: In this quickstart, you learn how to build a HoloLens Unity app using Object Anchors.
author: craigktreasure
manager: virivera

ms.author: crtreasu
ms.date: 03/02/2021
ms.topic: quickstart
ms.service: azure-object-anchors
---
# Quickstart: Create a HoloLens app with Azure Object Anchors, in Unity

In this quickstart, you'll create a Unity HoloLens app that uses [Azure Object Anchors](../overview.md). Azure
Object Anchors is a managed cloud service that converts 3D assets into AI models that enable object-aware mixed
reality experiences for the HoloLens. When you're finished, you'll have a HoloLens app built with Unity that can detect
objects in the physical world.

You'll learn how to:

> [!div class="checklist"]
> * Prepare Unity build settings.
> * Export the HoloLens Visual Studio project.
> * Deploy the app and run it on a HoloLens 2 device.

[!INCLUDE [Unity quickstart prerequisites](../../../includes/object-anchors-quickstart-unity-prerequisites.md)]

[!INCLUDE [Unity device setup](../../../includes/object-anchors-quickstart-unity-device-setup.md)]

## Open the sample project

[!INCLUDE [Clone Sample Repo](../../../includes/object-anchors-clone-sample-repository.md)]

[!INCLUDE [Download Unity Package](../../../includes/object-anchors-quickstart-unity-download-package.md)]

In Unity, open the `quickstarts/apps/unity/basic` project.

[!INCLUDE [Import Unity Package](../../../includes/object-anchors-quickstart-unity-import-package.md)]

[!INCLUDE [Unity build sample scene 1](../../../includes/object-anchors-quickstart-unity-build-sample-scene-1.md)]

[!INCLUDE [Unity build sample scene 2](../../../includes/object-anchors-quickstart-unity-build-sample-scene-2.md)]

[!INCLUDE [Unity build and deploy](../../../includes/object-anchors-quickstart-unity-build-deploy.md)]

### Run the sample app

Turn on the device, select **All Apps**, then locate and start the app. After the Unity splash screen, you'll see a message indicating that the Object Observer has been initialized. However, you'll need to add your model to the app.

[!INCLUDE [Unity setup Windows Device Portal](../../../includes/object-anchors-quickstart-unity-setup-device-portal.md)]

[!INCLUDE [Unity upload your model](../../../includes/object-anchors-quickstart-unity-upload-model.md)]

The app looks for objects in the current field of view and then tracks them once detected. An instance will be removed when it's 6 meters away from the user's location. The debug text shows details about an instance, like ID, updated timestamp and surface coverage ratio.

[!INCLUDE [Unity troubleshooting](../../../includes/object-anchors-quickstart-unity-troubleshooting.md)]

## Next steps

> [!div class="nextstepaction"]
> [Unity HoloLens with MRTK](get-started-unity-hololens-mrtk.md)

> [!div class="nextstepaction"]
> [Concepts: SDK overview](../concepts/sdk-overview.md)

> [!div class="nextstepaction"]
> [FAQ](../faq.md)
