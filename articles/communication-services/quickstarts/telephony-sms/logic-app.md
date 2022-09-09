---
title: Quickstart - Send SMS messages in Azure Logic Apps using Azure Communication Services
titleSuffix: An Azure Communication Services quickstart
description: In this quickstart, learn how to send SMS messages in Azure Logic Apps workflows by using the Azure Communication Services connector.
author: tophpalmer
manager: anvalent
services: azure-communication-services

ms.author: chpalm
ms.date: 10/06/2020
ms.topic: quickstart
ms.service: azure-communication-services
---

# Quickstart: Send SMS messages in Azure Logic Apps with Azure Communication Services

By using the [Azure Communication Services SMS](../../overview.md) connector and [Azure Logic Apps](../../../logic-apps/logic-apps-overview.md), you can create automated workflows, or *logic apps*, that can send SMS messages. This quickstart shows how to automatically send text messages in response to a trigger event, which is the first step in a logic app workflow. A trigger event can be an incoming email message, a recurrence schedule, an [Azure Event Grid](../../../event-grid/overview.md) resource event, or any other [trigger that's supported by Azure Logic Apps](/connectors/connector-reference/connector-reference-logicapps-connectors).

:::image type="content" source="./media/logic-app/azure-communication-services-connector.png" alt-text="Screenshot that shows the Azure portal, which is open to the Logic App Designer, and shows an example logic app that uses the Send SMS action for the Azure Communication Services connector.":::

Although this quickstart focuses on using the connector to respond to a trigger, you can also use the connector to respond to other actions, which are the steps that follow the trigger in a workflow. If you're new to Logic Apps, review [What is Azure Logic Apps](../../../logic-apps/logic-apps-overview.md) before you get started.

> [!NOTE]
> Completing this quickstart incurs a small cost of a few USD cents or less in your Azure account.

## Prerequisites

- An Azure account with an active subscription, or [create an Azure account for free](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- An active Azure Communication Services resource, or [create a Communication Services resource](../create-communication-resource.md).

- An active Logic Apps resource (logic app), or [create a blank logic app but with the trigger that you want to use](../../../logic-apps/quickstart-create-first-logic-app-workflow.md). Currently, the Azure Communication Services SMS connector provides only actions, so your logic app requires a trigger, at minimum.

  This quickstart uses the **When a new email arrives** trigger, which is available with the [Office 365 Outlook connector](/connectors/office365/).

- An SMS enabled phone number, or [get a phone number](./get-phone-number.md).

## Add an SMS action

To add the **Send SMS** action as a new step in your workflow by using the Azure Communication Services SMS connector, follow these steps in the [Azure portal](https://portal.azure.com) with your logic app workflow open in the Logic App Designer:

1. On the designer, under the step where you want to add the new action, select **New step**. Alternatively, to add the new action between steps, move your pointer over the arrow between those steps, select the plus sign (**+**), and select **Add an action**.

1. In the **Choose an operation** search box, enter `Azure Communication Services`. From the actions list, select **Send SMS**.

   :::image type="content" source="./media/logic-app/select-send-sms-action.png" alt-text="Screenshot that shows the Logic App Designer and the Azure Communication Services connector with the Send SMS action selected.":::

1. Now create a connection to your Communication Services resource.

   1. Provide a name for the connection.

   1. Select your Azure Communication Services resource.

   1. Select **Create**.

   :::image type="content" source="./media/logic-app/send-sms-configuration.png" alt-text="Screenshot that shows the Send SMS action configuration with sample information.":::

1. In the **Send SMS** action, provide the following information: 

   * The source and destination phone numbers. For testing purposes, you can use your own phone number as the destination phone number.

   * The message content that you want to send, for example, "Hello from Logic Apps!".

   Here's a **Send SMS** action with example information:

   :::image type="content" source="./media/logic-app/send-sms-action.png" alt-text="Screenshot that shows the Send SMS action with sample information.":::

1. When you're done, on the designer toolbar, select **Save**.

Next, run your logic app for testing.

## Test your logic app

To manually start your logic app, on the designer toolbar, select **Run**. Or, you can wait for your logic app to trigger. In both cases, the logic app should send an SMS message to your specified destination phone number. For more information about running your logic app, review [how to run your logic app](../../../logic-apps/quickstart-create-first-logic-app-workflow.md#run-your-logic-app)

## Clean up resources

To remove a Communication Services subscription, delete the Communication Services resource or resource group. Deleting the resource group also deletes any other resources in that group. For more information, review [how to clean up Communication Services resources](../create-communication-resource.md#clean-up-resources).

To clean up your logic app workflow and related resources, review [how to clean up Logic Apps resources](../../../logic-apps/quickstart-create-first-logic-app-workflow.md#clean-up-resources).

## Next steps

In this quickstart, you learned how to send SMS messages by using Azure Logic Apps and Azure Communication Services. To learn more, continue with subscribing to SMS events:

> [!div class="nextstepaction"]
> [Subscribe to SMS Events](./handle-sms-events.md)

For more information about SMS in Azure Communication Services, see these articles:

- [SMS concepts](../../concepts/telephony-sms/concepts.md)
- [Plan your telephony and SMS solution](../../concepts/telephony-sms/plan-solution.md)
- [SMS SDK](../../concepts/telephony-sms/sdk-features.md)
