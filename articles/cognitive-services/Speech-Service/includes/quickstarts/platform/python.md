---
title: "Quickstart: Speech SDK for Python platform setup - Speech service"
titleSuffix: Azure Cognitive Services
description: Use this guide to set up your platform for using Python with the Speech service SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/09/2019
ms.author: erhopf
---

This guide shows how to install the [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) for Python.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## Supported operating systems

- The Python Speech SDK package is available for these operating systems:
  - Windows: x64 and x86
  - Mac: macOS X version 10.12 or later
  - Linux: Ubuntu 16.04, Ubuntu 18.04, Debian 9 on x64

## Prerequisites

- Supported Linux platforms will require certain libraries installed (`libssl` for secure sockets layer support and `libasound2` for sound support). Refer to your distribution below for the commands needed to install the correct versions of these libraries.

  - On Ubuntu, run the following commands to install the required packages:

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.0 libasound2
        ```

  - On Debian 9, run the following commands to install the required packages:

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.2 libasound2
        ```

- On Windows, you need the [Microsoft Visual C++ Redistributable for Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) for your platform. Note that installing this for the first time may require you to restart Windows before continuing with this guide.
- And finally, you'll need [Python 3.5 or later](https://www.python.org/downloads/). To check your installation, open a command prompt and type the command `python --version` and check the result. If it's installed properly, you'll get a response "Python 3.5.1" or similar.

## Install the Speech SDK using Visual Studio Code

1. Download and install the latest supported version of [Python](https://www.python.org/downloads/) for your platform, 3.5 or later.
   - Windows users make sure to select "Add Python to your PATH" during the installation process.
1. Download and install [Visual Studio Code](https://code.visualstudio.com/Download).
1. Open Visual Studio Code and install the Python extension. Select **File** > **Preferences** > **Extensions** from the menu. Search for **Python** and click **Install**.

   ![Install the Python extension](~/articles/cognitive-services/speech-service/media/sdk/qs-python-vscode-python-extension.png)

1. Also from within Visual Studio Code, install the Speech SDK Python package from the integrated command line:
   1. Open a terminal (from the drop-down menus, **Terminal** > **New Terminal**)
   1. In the terminal that opens, enter the command `python -m pip install azure-cognitiveservices-speech`

That's it, you're ready to start coding to the Speech SDK in Python, and can move on to [Next steps](#next-steps) below. If you are new to Visual Studio Code, refer to the more extensive [Visual Studio Code Documentation](https://code.visualstudio.com/docs). For more information about Visual Studio Code and Python, see [Visual Studio Code Python tutorial](https://code.visualstudio.com/docs/python/python-tutorial).

## Install the Speech SDK using the command line

If you are not using Visual Studio Code, the following command installs the Python package from [PyPI](https://pypi.org/) for the Speech SDK. For users of Visual Studio Code, skip to the next sub-section.

```sh
pip install azure-cognitiveservices-speech
```

If you are on macOS, you may need to run the following command to get the `pip` command above to work:

```sh
python3 -m pip install --upgrade pip
```

Once you've successfully used `pip` to install `azure-cognitiveservices-speech`, you can use the Speech SDK by importing the namespace into your Python projects. For example:

```py
import azure.cognitiveservices.speech as speechsdk
```

This is shown in more detail within the code examples listed in [Next steps](#next-steps) below.

## Support and updates

Updates to the Speech SDK Python package are distributed via PyPI and announced in the [Release notes](~/articles/cognitive-services/speech-service/releasenotes.md).
If a new version is available, you can update to it with the command `pip install --upgrade azure-cognitiveservices-speech`.
Check which version is currently installed by inspecting the `azure.cognitiveservices.speech.__version__` variable.

If you have a problem, or you're missing a feature, see [Support and help options](~/articles/cognitive-services/speech-service/support.md).

## Next steps

[!INCLUDE [windows](../quickstart-list.md)]