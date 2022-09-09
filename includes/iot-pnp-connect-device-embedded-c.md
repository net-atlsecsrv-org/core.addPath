---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 04/28/2021
---

If you're developing for *constrained devices*, you can use IoT Plug and Play with:

- The [Azure SDK for Embedded C IoT client libraries](https://aka.ms/embeddedcsdk).
- [Azure RTOS](/azure/rtos/overview-rtos).

This article includes links and resources for these constrained scenarios.

## Prerequisites

Many of the samples below require a specific hardware device and the prerequisites are different for each of the samples. Follow the link to the relevant sample for detailed prerequisites, configuration, and build instructions.

## Use the SDK for Embedded C

The SDK for Embedded C offers a lightweight solution to connect constrained devices to Azure IoT services, including using the [IoT Plug and Play conventions](../articles/iot-pnp/concepts-convention.md). The following links include samples for MCU-based devices and for educational and debugging purposes.

### Use an MCU-based device

For a complete end-to-end tutorial using the SDK for Embedded C, the Device Provisioning Service, and IoT Plug and Play on an MCU, see [Repurpose PIC-IoT Wx Development Board to Connect to Azure through IoT Hub Device Provisioning Service](https://github.com/Azure-Samples/Microchip-PIC-IoT-Wx).

### Introductory samples

The SDK for Embedded C repository contains [several samples](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/samples/iot#iot-hub-plug-and-play-sample) that show you how to use IoT Plug and Play:

> [!NOTE]
> These samples are shown running on Windows and Linux for educational and debugging purposes. In a production scenario, the samples are intended for constrained devices only.

- [Thermostat example with SDK for Embedded C](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/paho_iot_hub_pnp_sample.c)

- [Temperature Controller example with the SDK for Embedded C](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/paho_iot_hub_pnp_component_sample.c)

## Using Azure RTOS

Azure RTOS includes a lightweight layer that adds native connectivity to Azure IoT cloud services. This layer provides a simple mechanism to connect constrained devices to Azure IoT while using the advanced features of Azure RTOS. To learn more, see the [What is Microsoft Azure RTOS](/azure/rtos/overview-rtos).

### Toolchains

The Azure RTOS samples are provided with different kinds of IDE and toolchain combinations, such as:

- IAR: IAR's [Embedded Workbench](https://www.iar.com/iar-embedded-workbench/) IDE
- GCC/CMake: Build on top of the open-source [CMake](https://cmake.org/) build system and [GNU Arm Embedded toolchain](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm).
- MCUExpresso: NXP's [MCUXpresso IDE](https://www.nxp.com/design/software/development-software/mcuxpresso-software-and-tools-/mcuxpresso-integrated-development-environment-ide:MCUXpresso-IDE)
- STM32Cube: STMicroelectronics's [STM32Cube IDE](https://www.st.com/en/development-tools/stm32cubeide.html)
- MPLAB: Microchip's [MPLAB X IDE](https://www.microchip.com/mplab/mplab-x-ide)

### Samples

The following table lists samples that show you how to get started on different devices with Azure RTOS and IoT Plug and Play:

Manufacturer | Device | Samples |
| --- | --- | --- |
| Microchip | [ATSAME54-XPRO](https://www.microchip.com/developmenttools/productdetails/atsame54-xpro) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/Microchip/ATSAME54-XPRO) • [IAR](https://aka.ms/azrtos-sample/e54-iar) • [MPLAB](https://aka.ms/azrtos-sample/e54-mplab)
| MXCHIP | [AZ3166](https://aka.ms/iot-devkit) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/MXChip/AZ3166)
| NXP | [MIMXRT1060-EVK](https://www.nxp.com/design/development-boards/i-mx-evaluation-and-development-boards/mimxrt1060-evk-i-mx-rt1060-evaluation-kit:MIMXRT1060-EVK) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/NXP/MIMXRT1060-EVK) • [IAR](https://aka.ms/azrtos-sample/rt1060-iar) • [MCUXpresso](https://aka.ms/azrtos-sample/rt1060-mcuxpresso)
| STMicroelectronics | [32F746GDISCOVERY](https://www.st.com/en/evaluation-tools/32f746gdiscovery.html) | [IAR](https://aka.ms/azrtos-sample/f746g-iar) • [STM32Cube](https://aka.ms/azrtos-sample/f746g-cubeide)
| STMicroelectronics | [B-L475E-IOT01](https://www.st.com/en/evaluation-tools/b-l475e-iot01a.html) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4%2B) • [IAR](https://aka.ms/azrtos-sample/l4s5-iar) • [STM32Cube](https://aka.ms/azrtos-sample/l4s5-cubeide)
| STMicroelectronics | [B-L4S5I-IOT01](https://www.st.com/en/evaluation-tools/b-l4s5i-iot01a.html) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4%2B) • [IAR](https://aka.ms/azrtos-sample/l4s5-iar) • [STM32Cube](https://aka.ms/azrtos-sample/l4s5-cubeide)
