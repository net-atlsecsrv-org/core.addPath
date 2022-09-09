---
title: Quickstart - Port a phone number into Azure Communication Services
description: Learn how to port a phone number into your Communication Services resource
author: mikben
manager: mikben
services: azure-communication-services

ms.author: mikben
ms.date: 03/20/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.custom: references_regions

---
# Quickstart: Port a phone number

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Get started with Azure Communication Services by porting your phone number into your Azure Communication Services resource. Toll-free and geographic numbers based in the United States are eligible for porting. For more information about phone number types, visit the [phone number conceptual documentation](../../concepts/telephony-sms/plan-solution.md).

## Prerequisites

- An Azure account with an active subscription. [Create an account for free](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [An active Communication Services resource.](../create-communication-resource.md)

## Gather your Azure resource details

Before initiating a port request, navigate to the Azure portal and select your Communication Services resource. With the `Overview` pane selected, click on the `JSON View` link in the upper right-hand corner:

:::image type="content" source="./media/number-port/json-view.png" alt-text="Screenshot showing selecting the JSON View.":::

Record your resource's **Azure ID** and **Immutable Resource ID**:

:::image type="content" source="./media/number-port/json-properties.png" alt-text="Screenshot showing selecting the JSON properties.":::

## Initiate the port request

Toll-free and geographic numbers based in the United States are eligible for porting. Use one of the following forms to submit your port request:

- For toll-free numbers: [Toll-free number port request](https://aka.ms/acs-port-form-tollfree)
- For geographic numbers based in the US: [Geographic number port request](https://aka.ms/acs-port-form-geographic)

When completed, send your completed port request form to acsporting@microsoft.com. Please ensure that your email subject line begins with "ACS Port-In Request".

## Next steps

In this quickstart you learned how to:

> [!div class="checklist"]
> * Acquire your Communication Services resource metadata
> * Submit a request to port your phone number

> [!div class="nextstepaction"]
> [Send an SMS](../telephony-sms/send.md)
> [Get started with calling](../voice-video-calling/getting-started-with-calling.md)
