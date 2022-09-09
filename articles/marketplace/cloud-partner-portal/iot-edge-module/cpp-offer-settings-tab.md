---
title: Offer settings for an Azure IoT Edge module | Azure Marketplace
description: Configure offer settings for an IoT Edge module.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
---

# IoT Edge module Offer Settings tab

>[!Important]
>Starting April 13, 2020, we'll begin moving management of your IoT Edge module offers to Partner Center. After the migration, you'll create and manage your offers in Partner Center. Follow the instructions in [Create an IoT Edge module offer](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-iot-edge-module-creation) to manage your migrated offers.

The **IoT Edge Modules > New Offer** page opens with the focus on the **Offer Settings** tab.

![New Offer page for IoT Edge modules](./media/iot-edge-module-offer-settings-tab.png)


## Offer Identity settings

Under **Offer Identity**, you must provide information for the fields described in the following table. An asterisk (*) appended to the field name indicates that it's required. 

|  **Field**       |     **Description**                                                          |
|  ---------       |     ---------------                                                          |
| **Offer ID\***       | A unique identifier (within a publisher profile) for the offer. This identifier will be visible in product URLs and insights reports. It has a maximum length of 50 characters, and can use lowercase alphanumeric characters and dashes (-). (The identifier can't end with a dash.) **Note:** This field can't be changed after an offer goes live. <br> For example, if Contoso publishes an offer with offer ID **sample-iot-edge-module**, it's assigned the Azure Marketplace URL `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-iot-edge-module?tab=Overview`. |
| **Publisher\***     | Your organization's unique identifier in the Azure Marketplace. All your offerings should be associated with your publisher ID. This value can't be changed after the offer's saved. |
| **Name\***          | The display name for your offer. This name is displayed in the Azure Marketplace and in the Cloud Partner Portal. It can have a maximum of 50 characters. We recommend using a  recognizable brand name for your product. Don't include your organization's name unless that's how your product is marketed. If you are marketing this offer in other websites and publications, ensure that the name is exactly the same across all publications. |
|  |  |


Select **Save** to save your Offer Settings.


## Next steps

Use the [SKUs](./cpp-skus-tab.md) tab to configure the SKUs for your offer.
