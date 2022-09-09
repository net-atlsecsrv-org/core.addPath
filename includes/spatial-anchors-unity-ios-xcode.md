---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 08/14/2020
ms.author: rgarcia
---
Select **Build**. In the dialog box that opens, select a folder to export the Xcode project to.

When the export is complete, a folder that contains the exported Xcode project will appear.

> [!NOTE]
> If a window asking you if you want to replace or append appears, we recommend that you select **Append** because it's faster. You should only need to select **Replace**
> if you're changing assets in your scene. (For example, if you're adding, removing, or changing parent/child relationships, or if you're adding, removing, or changing properties.) If you're only
> making source code changes, **Append** should be enough.

## Open the Xcode project

Now you can open `Unity-iPhone.xcodeproj` in Xcode. You can either launch Xcode and open the exported `Unity-iPhone.xcodeproj` project or launch the project in Xcode by running the following command from the location you exported the project:

```bash
open ./Unity-iPhone.xcodeproj
```

Select the root **Unity-iPhone** node to view the project settings, and then select the **General** tab.

Under **Signing**, make sure **Automatically manage signing** is enabled. If it's not, enable it, and then select **Enable Automatic** in the dialog box that appears to reset the build settings.

Under **Deployment Info**, make sure the **Deployment Target** is set to `11.0`.

## Deploy the app to your iOS device

Connect the iOS device to the Mac and set the **active scheme** to your iOS device.

![Select the device](./media/spatial-anchors-unity/select-device.png)

Select **Build and then run the current scheme**.

![Deploy and run](./media/spatial-anchors-unity/deploy-run.png)
