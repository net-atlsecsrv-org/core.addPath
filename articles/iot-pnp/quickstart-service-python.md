---
title: Quickstart - Interact with an IoT Plug and Play device connected to your Azure IoT solution (Python) | Microsoft Docs
description: Quickstart - Use Python to connect to and interact with an IoT Plug and Play device that's connected to your Azure IoT solution.
author: elhorton
ms.author: elhorton
ms.date: 10/05/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc, devx-track-azurecli

# As a solution builder, I want to connect to and interact with an IoT Plug and Play device that's connected to my solution. For example, to collect telemetry from the device or to control the behavior of the device.
---

# Quickstart: Interact with an IoT Plug and Play device that's connected to your solution (Python)

[!INCLUDE [iot-pnp-quickstarts-service-selector.md](../../includes/iot-pnp-quickstarts-service-selector.md)]

IoT Plug and Play simplifies IoT by enabling you to interact with a device's model without knowledge of the underlying device implementation. This quickstart shows you how to use Python to connect to and control an IoT Plug and Play device that's connected to your solution.

## Prerequisites

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

To complete this quickstart, you need Python 3.7 on your development machine. You can download the latest recommended version for multiple platforms from [python.org](https://www.python.org/). You can check your Python version with the following command:  

```cmd/sh
python --version
```

The **azure-iot-device** package is published as a PIP.

In your local python environment install the package as follows:

```cmd/sh
pip install azure-iot-device
```

Install the **azure-iot-hub** package by running the following command:

```cmd/sh
pip install azure-iot-hub
```

## Run the sample device

[!INCLUDE [iot-pnp-environment](../../includes/iot-pnp-environment.md)]

To learn more about the sample configuration, see the [sample readme](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/pnp/README.md).

In this quickstart, you use a sample thermostat device, written in Python, as the IoT Plug and Play device. To run the sample device:

1. Open a terminal window in a folder of your choice. Run the following command to clone the [Azure IoT Python SDK](https://github.com/Azure/azure-iot-sdk-python) GitHub repository into this location:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```

1. This terminal window is used as your **device** terminal. Go to the folder of your cloned repository, and navigate to the */azure-iot-sdk-python/azure-iot-device/samples/pnp* folder.

1. Run the sample thermostat device with the following command:

    ```cmd/sh
    python simple_thermostat.py
    ```

1. You see messages saying that the device has sent some information and reported itself online. These messages indicate that the device has begun sending telemetry data to the hub, and is now ready to receive commands and property updates. Don't close this terminal, you need it to confirm the service sample is working.

## Run the sample solution

In this quickstart, you use a sample IoT solution in Python to interact with the sample device you just set up.

1. Open another terminal window to use as your **service** terminal.

1. Navigate to the */azure-iot-sdk-python/azure-iot-hub/samples* folder of the cloned Python SDK repository.

1. Open the *registry_manager_pnp_sample.py* file and review the code. This sample shows how to use the **IoTHubRegistryManager** class to interact with your IoT Plug and Play device.

> [!NOTE]
> These service samples use the **IoTHubRegistryManager** class from the **IoT Hub service client**. To learn more about the APIs, including the digital twins API, see the [service developer guide](concepts-developer-guide-service.md).

### Get the device twin

In [Set up your environment for the IoT Plug and Play quickstarts and tutorials](set-up-environment.md) you created two environment variables to configure the sample to connect to your IoT hub and device:

* **IOTHUB_CONNECTION_STRING**: the IoT hub connection string you made a note of previously.
* **IOTHUB_DEVICE_ID**: `"my-pnp-device"`.

Use the following command in the **service** terminal to run this sample:

```cmd/sh
set IOTHUB_METHOD_NAME="getMaxMinReport"
set IOTHUB_METHOD_PAYLOAD="hello world"
python registry_manager_pnp_sample.py
```

> [!NOTE]
> If you're running this sample on Linux, use `export` in place of `set`.

The output shows the device twin and prints its model ID:

```cmd/sh
The Model ID for this device is:
dtmi:com:example:Thermostat;1
```

The following snippet shows the sample code from *registry_manager_pnp_sample.py*:

```python
    # Create IoTHubRegistryManager
    iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

    # Get device twin
    twin = iothub_registry_manager.get_twin(device_id)
    print("The device twin is: ")
    print("")
    print(twin)
    print("")

    # Print the device's model ID
    additional_props = twin.additional_properties
    if "modelId" in additional_props:
        print("The Model ID for this device is:")
        print(additional_props["modelId"])
        print("")
```

### Update a device twin

This sample shows you how to update the `targetTemperature` writable property in the device:

```python
    # Update twin
    twin_patch = Twin()
    twin_patch.properties = TwinProperties(
        desired={"targetTemperature": 42}
    )  # this is relevant for the thermostat device sample
    updated_twin = iothub_registry_manager.update_twin(device_id, twin_patch, twin.etag)
    print("The twin patch has been successfully applied")
    print("")
```

You can verify that the update is applied in the **device** terminal that shows the following output:

```cmd/sh
the data in the desired properties patch was: {'targetTemperature': 42, '$version': 2}
```

The **service** terminal confirms that the patch was successful:

```cmd/sh
The twin patch has been successfully applied
```

### Invoke a command

The sample then invokes a command:

The **service** terminal shows a confirmation message from the device:

```cmd/sh
The device method has been successfully invoked
```

In the **device** terminal, you see the device receives the command:

```cmd/sh
Command request received with payload
hello world
Will return the max, min and average temperature from the specified time hello to the current time
Done generating
{"tempReport": {"avgTemp": 34.2, "endTime": "09/07/2020 09:58:11", "maxTemp": 49, "minTemp": 10, "startTime": "09/07/2020 09:56:51"}}
```

## Next steps

In this quickstart, you learned how to connect an IoT Plug and Play device to a IoT solution. To learn more about IoT Plug and Play device models, see:

> [!div class="nextstepaction"]
> [IoT Plug and Play modeling developer guide](concepts-developer-guide-device-csharp.md)
