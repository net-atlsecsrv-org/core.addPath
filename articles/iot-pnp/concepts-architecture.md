---
title: IoT Plug and Play architecture | Microsoft Docs
description: As a solution builder, understand key architectural elements of IoT Plug and Play.
author: ridomin
ms.author: rmpablos
ms.date: 07/06/2020
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
---

# IoT Plug and Play Preview architecture

IoT Plug and Play Preview enables solution builders to integrate smart devices with their solutions without any manual configuration. At the core of IoT Plug and Play, is a device _model_ that describes a device's capabilities to an IoT Plug and Play-enabled application. This model is structured as a set of interfaces that define:

- _Properties_ that represent the read-only or writable state of a device or other entity. For example, a device serial number may be a read-only property and a target temperature on a thermostat may be a writable property.
- _Telemetry_ that's the data emitted by a device, whether the data is a regular stream of sensor readings, an occasional error, or an information message.
- _Commands_ that describe a function or operation that can be done on a device. For example, a command could reboot a gateway or take a picture using a remote camera.

Every model and interface has a unique ID.

The following diagram shows the key elements of an IoT Plug and Play solution:

:::image type="content" source="media/concepts-architecture/pnp-architecture.png" alt-text="IoT Plug and Play architecture":::

## Model repository

The [model repository](./concepts-model-repository.md) is a store for model and interface definitions. You define models and interfaces using the [Digital Twins Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl).

The web UI lets you manage the models and interfaces.

The model repository uses RBAC to enable you to limit access to interface definitions.

## Devices

A device builder implements the code to run on an IoT smart device using one of the [Azure IoT device SDKs](./libraries-sdks.md). The device SDKs help the device builder to:

- Connect securely to an IoT hub.
- Register the device with your IoT hub and announce the model ID that identifies the collection of interfaces the device implements.
- Update the properties defined in the DTDL interfaces the device implements. These properties are implemented using digital twins that manage the synchronization with your IoT hub.
- Add command handlers for the commands defined in the DTDL interfaces the device implements.
- Send telemetry to the IoT hub.

## IoT Hub

[IoT Hub](../iot-hub/about-iot-hub.md) is a cloud-hosted service that acts as a central message hub for bi-directional communication between your IoT solution and the devices it manages.

An IoT hub:

- Makes the model ID implemented by a device available to a backend solution.
- Maintains the digital twin associated with each Plug and Play device connected to the hub.
- Forwards telemetry streams to other services for processing or storage.
- Routes digital twin change events to other services to enable device monitoring.

## Backend solution

A backend solution monitors and controls connected devices by interacting with digital twins in the IoT hub. Use one of the service SDKs to implement your backend solution. To understand the capabilities of a connected device, the solution backend:

1. Retrieves the model ID the device registered with the IoT hub.
1. Uses the model ID to retrieve the interface definitions from any model repository.
1. Uses the model parser to extract information from the interface definitions.

The backend solution can use the information from the interface definitions to:

- Read property values reported by devices.
- Update writable properties on a device.
- Call commands implemented by a device.
- Understand the format of telemetry sent by a device.

## Next steps

Now that you have an overview of the architecture of an IoT Plug and Play solution, the next steps are to learn more about:

- [The model repository](./concepts-model-repository.md)
- [Digital twin model integration](./concepts-model-discovery.md)
- [Developing for IoT Plug and Play](./concepts-developer-guide.md)
