---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 08/14/2020
ms.author: rgarcia
---
Select **Build**. On the pane that opens, select a folder to export the Xcode project to.

   When the export is complete, a folder that contains the exported Xcode project appears.

   > [!NOTE]
   > If a window appears with a message asking whether you want to replace or append, we recommend that you select **Append**, because it's faster. You should select **Replace** only if you're changing assets in your scene. For example, you might be adding, removing, or changing parent/child relationships, or you might be adding, removing, or changing properties. If you're only making source code changes, **Append** should be enough.

## Open the Xcode project

Now you can open your `Unity-iPhone.xcodeproj` project in Xcode. 

You can either launch Xcode and open the exported `Unity-iPhone.xcodeproj` project or launch the project in Xcode by running the following command from the location where you exported the project:

 ```bash
open ./Unity-iPhone.xcodeproj
```

Select the root **Unity-iPhone** node to view the project settings, and then select the **General** tab.

Under **Signing**, make sure that **Automatically manage signing** is enabled. If it's not, enable it, and then reset the build settings by selecting **Enable Automatic** on the pane that appears.

Under **Deployment Info**, make sure that **Deployment Target** is set to **11.0**.

## Deploy the app to your iOS device

Connect the iOS device to the Mac, and set the **active scheme** to your iOS device.

   ![Screenshot of the My iPhone button for selecting the device.](./media/spatial-anchors-unity/select-device.png)

Select **Build and then run the current scheme**.

   ![Screenshot of the "Deploy and run" arrow button.](./media/spatial-anchors-unity/deploy-run.png)
