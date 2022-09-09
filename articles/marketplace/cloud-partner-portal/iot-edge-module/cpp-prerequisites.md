---
title: Azure IoT Edge module prerequisites | Azure Marketplace
description: Prerequisites for publishing an IoT Edge module.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
---

# IoT Edge module publishing prerequisites

>[!Important]
>Starting April 13, 2020, we'll begin moving management of your IoT Edge module offers to Partner Center. After the migration, you'll create and manage your offers in Partner Center. Follow the instructions in [Create an IoT Edge module offer](https://aka.ms/AzureCreateIoT) to manage your migrated offers.

This article describes the prerequisites for publishing an IoT Edge module offer.  If you have not already done so, review the [IoT Edge modules publishing guide](../..//iot-edge-module.md).


## Publishing prerequisites

To publish an IoT Edge module to the Azure Marketplace, you have to meet the following prerequisites:

<!-- P2: It would be great to point to the terms of use of CPP here. This can often be a blocker for big companies and these terms of use are not anonymously visible yet.-->
- Access to the [Cloud Partner Portal](https://cloudpartner.azure.com/). For more information, see [Azure Marketplace and AppSource publishing guide](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).
- Agreement to the [Azure Marketplace Terms](https://azure.microsoft.com/support/legal/marketplace-terms/)
- Host your IoT Edge module technical asset in an Azure Container Registry.  For more information, see [how to prepare your IoT Edge module technical asset](./cpp-create-technical-assets.md)
- Have your IoT Edge module metadata ready to use. For example, prepare the following assets:
    - A title
    - A description (in HTML format)
    - A logo image (PNG format and fixed image sizes including 40x40px, 90x90px, 115x115px, 255x115px)
    - A term of use and privacy policy
    - A default module configuration that includes: routes, twin desired properties, createOptions, and environment variables.
    - Module documentation
    - Support contacts


## Next steps

Once you have [prepared your IoT Edge module technical asset](./cpp-create-technical-assets.md), you will be ready to [create your IoT Edge module offer](./cpp-create-offer.md). 
