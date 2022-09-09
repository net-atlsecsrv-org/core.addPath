---
title: Security agents overview
description: Understand security agent architecture for the agents used in the Azure Defender for IoT service.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''


ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2021
ms.author: shhazam
---

# Security agent reference architecture

Azure Defender for IoT provides reference architecture for security agents that log, process, aggregate, and send security data through IoT Hub.

Security agents are designed to work in a constrained IoT environment, and are highly customizable in terms of values they provide when compared to the resources they consume.

Security agents support the following features:

- Authenticate with existing device identity, or a dedicated module identity. To learn more, see [Security agent authentication methods](concept-security-agent-authentication-methods.md).

- Collect raw security events from the underlying Operating System (Linux, Windows). To learn more about available security data collectors, see [Defender for IoT agent configuration](how-to-agent-configuration.md).

- Aggregate raw security events into messages sent through IoT Hub.

- Configure remotely through use of the **azureiotsecurity** module twin. To learn more, see [Configure a Defender for IoT agent](how-to-agent-configuration.md).

Defender for IoT Security agents is developed as open-source projects, and are available from GitHub:

- [Defender for IoT C-based agent](https://github.com/Azure/Azure-IoT-Security-Agent-C)
- [Defender for IoT C#-based agent](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## Agent supported platforms

Defender for IoT offers different installer agents for 32 bit and 64-bit Windows, and the same for 32 bit and 64-bit Linux. Make sure you have the correct agent installer for each of your devices according to the following table:

| Architecture | Linux | Windows | Details |
|--|--|--|--|
| 32 bit | C | C# |  |
| 64 bit | C# or C | C# | We recommend using the C agent for devices with more restricted or minimal device resources. |


## Next steps

In this article, you got a high-level overview about Defender for IoT security module architecture, and the available installers.

To continue getting started with Defender for IoT deployment, use the following articles:

- Understand [security agent authentication methods](concept-security-agent-authentication-methods.md)
- Select and deploy a [security agent](how-to-deploy-agent.md)
- Review the Defender for IoT [system prerequisites](quickstart-system-prerequisites.md)
- Learn how to [enable Defender for IoT service in your IoT Hub](quickstart-onboard-iot-hub.md)
- Learn more about the service from the [Defender for IoT FAQ](resources-frequently-asked-questions.md)
