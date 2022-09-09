---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
---

Handling compressed audio is implemented using [GStreamer](https://gstreamer.freedesktop.org). For licensing reasons GStreamer binaries are not compiled and linked with the Speech SDK. Developers need to install several dependencies and plugins, see [Installing on Windows](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c) or [Installing on Linux](https://gstreamer.freedesktop.org/documentation/installing/on-linux.html?gi-language=c). GStreamer binaries need to be in the system path, so that the Speech SDK can load the binaries during runtime. For example, on Windows, if the Speech SDK is able to find `libgstreamer-1.0-0.dll` or `gstreamer-1.0-0.dll` (for latest gstreamer) during runtime, it means the gstreamer binaries are in the system path.

