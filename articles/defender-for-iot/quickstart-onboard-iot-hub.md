---
title: Onboard to Defender for IoT agent-based solution
description: Learn how to onboard and enable the Defender for IoT security service in your Azure IoT Hub.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''


ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/20/2021
ms.author: shhazam
---

# Onboard to Defender for IoT agent-based solution

This article explains how to enable the Defender for IoT service on your existing IoT Hub. If you don't currently have an IoT Hub, see [Create an IoT Hub using the Azure portal](../iot-hub/iot-hub-create-through-portal.md) to get started.

You can manage your IoT security through the IoT Hub in Defender for IoT. The management portal located in the IoT Hub allows you to do the following: 

- Manage IoT Hub security.

- Basic management of an IoT device's security without installing an agent based on the IoT Hub telemetry. 

- Advanced management for the security of an IoT device based on the micro agent.

> [!NOTE]
> Defender for IoT currently only supports standard tier IoT Hubs.

## Onboard to Defender for IoT in IoT Hub

For all new IoT hubs, Defender for IoT is set to **On** by default. You can verify that Defender for IoT is toggled to **On** during the IoT Hub creation process.

To verify the toggle is set to **On**:

1. Navigate to the Azure portal.

1. Select **IoT Hub** from the list of Azure services.

1. Select **Create**.

    :::image type="content" source="media/quickstart-onboard-iot-hub/create-iot-hub.png" alt-text="Select the create button from the top toolbar." lightbox="media/quickstart-onboard-iot-hub/create-iot-hub-expanded.png":::

1. Select the **Management** tab, and verify that **Defender for IoT** toggle is set to **On**.

    :::image type="content" source="media/quickstart-onboard-iot-hub/management-tab.png" alt-text="Ensure the Defender for IoT toggle is set to on.":::

## Onboard Defender for IoT to an existing IoT Hub

You can monitor your device identity management, device to cloud, and cloud to device communication patterns, do the following to start the service: 

1. Navigate to IoT Hub. 

1. Select the **Security overview** menu. 

1. Click Secure your IoT solution and complete the onboarding form. 


## Next steps

Advance to the next article to configure your solution...

> [!div class="nextstepaction"]
> [Create a Defender Iot micro agent module twin (Preview)](quickstart-create-micro-agent-module-twin.md)
