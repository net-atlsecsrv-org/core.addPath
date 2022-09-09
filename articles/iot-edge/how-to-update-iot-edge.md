---
title: Update IoT Edge version on devices - Azure IoT Edge | Microsoft Docs 
description: How to update IoT Edge devices to run the latest versions of the security daemon and the IoT Edge runtime
keywords: 
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/20/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
---

# Update the IoT Edge security daemon and runtime

As the IoT Edge service releases new versions, you'll want to update your IoT Edge devices for the latest features and security improvements. This article provides information about how to update your IoT Edge devices when a new version is available.

Two components of an IoT Edge device need to be updated if you want to move to a newer version. The first is the security daemon, which runs on the device and starts the runtime modules when the device starts. Currently, the security daemon can only be updated from the device itself. The second component is the runtime, made up of the IoT Edge hub and IoT Edge agent modules. Depending on how you structure your deployment, the runtime can be updated from the device or remotely.

To find the latest version of Azure IoT Edge, see [Azure IoT Edge releases](https://github.com/Azure/azure-iotedge/releases).

## Update the security daemon

The IoT Edge security daemon is a native component that needs to be updated using the package manager on the IoT Edge device.

Check the version of the security daemon running on your device by using the command `iotedge version`.

# [Linux](#tab/linux)

On Linux x64 devices, use apt-get or your appropriate package manager to update the security daemon to the latest version.

Get the latest repository configuration from Microsoft:

* **Ubuntu Server 18.04**:

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Raspberry Pi OS Stretch**:

   ```bash
   curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
   ```

Copy the generated list.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

Install Microsoft GPG public key.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

Update apt.

   ```bash
   sudo apt-get update
   ```

Check to see which versions of IoT Edge are available.

   ```bash
   apt list -a iotedge
   ```

If you want to update to the most recent version of the security daemon, use the following command which also updates **libiothsm-std** to the latest version:

   ```bash
   sudo apt-get install iotedge
   ```

If you want to update to a specific version of the security daemon, specify the version from the apt list output. Whenever **iotedge** is updated, it automatically tries to update the **libiothsm-std** package to its latest version, which may cause a dependency conflict. If you aren't going to the most recent version, be sure to target both packages for the same version. For example, the following command installs a specific version of the 1.0.9 release:

   ```bash
   sudo apt-get install iotedge=1.0.9-1 libiothsm-std=1.0.9-1
   ```

If the version that you want to install is not available through apt-get, you can use curl to target any version from the [IoT Edge releases](https://github.com/Azure/azure-iotedge/releases) repository. For whichever version you want to install, locate the appropriate **libiothsm-std** and **iotedge** files for your device. For each file, right-click the file link and copy the link address. Use the link address to install the specific versions of those components:

```bash
curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
```

# [Windows](#tab/windows)

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

With IoT Edge for Linux on Windows, IoT Edge runs in a Linux virtual machine hosted on a Windows device. This virtual machine is pre-installed with IoT Edge, and is managed with Microsoft Update to keep the components up to date. Currently, there are no updates available.

::: moniker-end

With IoT Edge for Windows, IoT Edge runs directly on the Windows device. For update instructions using the PowerShell scripts, see [Install and manage Azure IoT Edge for Windows](how-to-install-iot-edge-windows-on-windows.md).

---

## Update the runtime containers

The way that you update the IoT Edge agent and IoT Edge hub containers depends on whether you use rolling tags (like 1.0) or specific tags (like 1.0.7) in your deployment.

Check the version of the IoT Edge agent and IoT Edge hub modules currently on your device using the commands `iotedge logs edgeAgent` or `iotedge logs edgeHub`.

  ![Find container version in logs](./media/how-to-update-iot-edge/container-version.png)

### Understand IoT Edge tags

The IoT Edge agent and IoT Edge hub images are tagged with the IoT Edge version that they are associated with. There are two different ways to use tags with the runtime images:

* **Rolling tags** - Use only the first two values of the version number to get the latest image that matches those digits. For example, 1.0 is updated whenever there's a new release to point to the latest 1.0.x version. If the container runtime on your IoT Edge device pulls the image again, the runtime modules are updated to the latest version. This approach is suggested for development purposes. Deployments from the Azure portal default to rolling tags.

* **Specific tags** - Use all three values of the version number to explicitly set the image version. For example, 1.0.7 won't change after its initial release. You can declare a new version number in the deployment manifest when you're ready to update. This approach is suggested for production purposes.

### Update a rolling tag image

If you use rolling tags in your deployment (for example, mcr.microsoft.com/azureiotedge-hub:**1.0**) then you need to force the container runtime on your device to pull the latest version of the image.

Delete the local version of the image from your IoT Edge device. On Windows machines, uninstalling the security daemon also removes the runtime images, so you don't need to take this step again.

```bash
docker rmi mcr.microsoft.com/azureiotedge-hub:1.0
docker rmi mcr.microsoft.com/azureiotedge-agent:1.0
```

You may need to use the force `-f` flag to remove the images.

The IoT Edge service will pull the latest versions of the runtime images and automatically start them on your device again.

### Update a specific tag image

If you use specific tags in your deployment (for example, mcr.microsoft.com/azureiotedge-hub:**1.0.8**) then all you need to do is update the tag in your deployment manifest and apply the changes to your device.

1. In the IoT Hub in the Azure portal, select your IoT Edge device, and select **Set Modules**.

1. In the **IoT Edge Modules** section, select **Runtime Settings**.

   ![Configure runtime settings](./media/how-to-update-iot-edge/configure-runtime.png)

1. In **Runtime Settings**, update the **Image** value for **Edge Hub** with the desired version. Don't select **Save** yet.

   ![Update Edge Hub Image version](./media/how-to-update-iot-edge/runtime-settings-edgehub.png)

1. Collapse the **Edge Hub** settings, or scroll down, and update the **Image** value for **Edge Agent** with the same desired version.

   ![Update Edge Hub Agent version](./media/how-to-update-iot-edge/runtime-settings-edgeagent.png)

1. Select **Save**.

1. Select **Review + create**, review the deployment, and select **Create**.

## Update to a release candidate version

Azure IoT Edge regularly releases new versions of the IoT Edge service. Before each stable release, there is one or more release candidate (RC) versions. RC versions include all the planned features for the release, but are still going through testing and validation. If you want to test a new feature early, you can install an RC version and provide feedback through GitHub.

Release candidate versions follow the same numbering convention of releases, but have **-rc** plus an incremental number appended to the end. You can see the release candidates in the same list of [Azure IoT Edge releases](https://github.com/Azure/azure-iotedge/releases) as the stable versions. For example, find **1.0.9-rc5** and **1.0.9-rc6**, two of the release candidates that came before **1.0.9**. You can also see that RC versions are marked with **pre-release** labels.

The IoT Edge agent and hub modules have RC versions that are tagged with the same convention. For example, **mcr.microsoft.com/azureiotedge-hub:1.0.9-rc6**.

As previews, release candidate versions aren't included as the latest version that the regular installers target. Instead, you need to manually target the assets for the RC version that you want to test. For the most part, installing or updating to an RC version is the same as targeting any other specific version of IoT Edge.

Use the sections in this article to learn how to update an IoT Edge device to a specific version of the security daemon or runtime modules.

If you're installing IoT Edge, rather than upgrading an existing installation, use the steps in [Offline or specific version installation](how-to-install-iot-edge.md#offline-or-specific-version-installation-optional).

## Next steps

View the latest [Azure IoT Edge releases](https://github.com/Azure/azure-iotedge/releases).

Stay up-to-date with recent updates and announcements in the [Internet of Things blog](https://azure.microsoft.com/blog/topics/internet-of-things/)