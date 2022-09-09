---
title: Azure Kinect Body Tracking SDK download
description: Understand how to download each version of the Azure Kinect Sensor SDK on Windows or Linux.
author: qm13
ms.author: quentinm
ms.prod: kinect-dk
ms.date: 03/18/2021
ms.topic: conceptual
keywords: azure, kinect, sdk, download update, latest, available, install, body, tracking
---

# Download Azure Kinect Body Tracking SDK

This document provides links to install each version of the Azure Kinect Body Tracking SDK.

## Azure Kinect Body Tracking SDK contents

- Headers and libraries to build a body tracking application using the Azure Kinect DK.
- Redistributable DLLs needed by body tracking applications using the Azure Kinect DK.
- Sample body tracking applications.

## Windows download links

Version       | Download
--------------|----------
1.1.0 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=102901)
1.0.1 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100942) [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/1.0.1)
1.0.0 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100848) [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/1.0.0)

## Linux installation instructions

Currently, the only supported distribution is Ubuntu 18.04 and 20.04. To request support for other distributions, see [this page](https://aka.ms/azurekinectfeedback).

First, you'll need to configure [Microsoft's Package Repository](https://packages.microsoft.com/), following the instructions [here](/windows-server/administration/linux-package-repository-for-microsoft-software).

The `libk4abt<major>.<minor>-dev` package contains the headers and CMake files to build against `libk4abt`.
The `libk4abt<major>.<minor>` package contains the shared objects needed to run executables that depend on `libk4abt` as well as the example viewer.

The basic tutorials require the `libk4abt<major>.<minor>-dev` package. To install it, run

`sudo apt install libk4abt<major>.<minor>-dev`

If the command succeeds, the SDK is ready for use.

> [!NOTE]
> When installing the SDK, remember the path you install to. For example, "C:\Program Files\Azure Kinect Body Tracking SDK 1.0.0". You will find the samples referenced in articles in this path.
> Body tracking samples are located in the [body-tracking-samples](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples) folder in the Azure-Kinect-Samples repository. You will find the samples referenced in articles here.

## Change log

### v1.1.0
* [Feature] Add support for DirectML (Windows only) and TensorRT execution of pose estimation model. See FAQ on new execution environments.
* [Feature] Add `model_path` to `k4abt_tracker_configuration_t` struct. Allows users to specify the pathname for pose estimation model. Defaults to `dnn_model_2_0_op11.onnx` standard pose estimation model located in the current directory.
* [Feature] Include `dnn_model_2_0_lite_op11.onnx` lite pose estimation model. This model trades ~2x performance increase for ~5% accuracy decrease.
* [Feature] Verified samples compile with Visual Studio 2019 and updates samples to use this release.
* [Breaking Change] Update to ONNX Runtime 1.6 with support for CPU, CUDA 11.1, DirectML (Windows only), and TensorRT 7.2.1 execution environments. Requires NVIDIA driver update to R455 or better.
* [Breaking Change] No NuGet install.
* [Bug Fix] Add support for NVIDIA RTX 30xx series GPUs [Link](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1481)
* [Bug Fix] Add support for AMD and Intel integrated GPUs (Windows only) [Link](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1481)
* [Bug Fix] Update to CUDA 11.1 [Link](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1125)
* [Bug Fix] Update to Sensor SDK 1.4.1 [Link](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1248)

### v1.0.1
* [Bug Fix] Fix issue that the SDK crashes if loading onnxruntime.dll from path on Windows build 19025 or later: [Link](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/932)

### v1.0.0
* [Feature] Add C# wrapper to the msi installer.
* [Bug Fix] Fix issue that the head rotation cannot be detected correctly: [Link](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/997)
* [Bug Fix] Fix issue that the CPU usage goes up to 100% on Linux machine: [Link](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1007)
* [Samples] Add two samples to the sample repo. Sample 1 demonstrates how to transform body tracking results from the depth space to color space [Link](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples/camera_space_transform_sample); sample 2 demonstrates how to detect floor plane [Link](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples/floor_detector_sample)

## Next steps

- [Azure Kinect DK overview](about-azure-kinect-dk.md)

- [Set up Azure Kinect DK](set-up-azure-kinect-dk.md)

- [Set up Azure Kinect body tracking](body-sdk-setup.md)