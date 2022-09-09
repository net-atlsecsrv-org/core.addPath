---
title: Track B2B messages with Azure Monitor logs - Azure Logic Apps | Microsoft Docs
description: Track B2B communication for integration accounts and Azure Logic Apps with Azure Log Analytics
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/19/2018
---

# Track B2B messages with Azure Monitor logs

After you set up B2B communication between trading partners 
in your integration account, those partners can exchange 
messages with protocols such as AS2, X12, and EDIFACT. 
To check that these messages are processed correctly, 
you can track these messages with 
[Azure Monitor logs](../log-analytics/log-analytics-overview.md). 
For example, you can use these web-based tracking 
capabilities for tracking messages:

* Message count and status
* Acknowledgments status
* Correlate messages with acknowledgments
* Detailed error descriptions for failures
* Search capabilities

> [!NOTE]
> This page previously described steps for how to perform these tasks 
> with the Microsoft Operations Management Suite (OMS), which is 
> [retiring in January 2019](../azure-monitor/platform/oms-portal-transition.md), 
> replaces those steps with Azure Log Analytics instead. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## Prerequisites

* A logic app that's set up with diagnostics logging. Learn 
[how to create a logic app](quickstart-create-first-logic-app-workflow.md) 
and [how to set up logging for that logic app](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* An integration account that's set up with monitoring and logging. Learn 
[how to create an integration account](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) 
and [how to set up monitoring and logging for that account](../logic-apps/logic-apps-monitor-b2b-message.md).

* If you haven't already, [publish diagnostic data to Azure Monitor logs](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

* After you meet the previous requirements, you also need a Log Analytics workspace, 
which you use for tracking B2B communication through Log Analytics. 
If you don't have a Log Analytics workspace, learn 
[how to create a Log Analytics workspace](../azure-monitor/learn/quick-create-workspace.md).

## Install Logic Apps B2B solution

Before you can have Azure Monitor logs track B2B messages for your logic app, 
add the **Logic Apps B2B** solution to Azure Monitor logs. Learn more about 
[adding solutions to Azure Monitor logs](../azure-monitor/learn/quick-create-workspace.md).

1. In the [Azure portal](https://portal.azure.com), 
select **All services**. In the search box, 
find "log analytics", and select **Log Analytics**.

   ![Select Log Analytics](media/logic-apps-track-b2b-messages-omsportal/find-log-analytics.png)

1. Under **Log Analytics**, find and 
select your Log Analytics workspace. 

   ![Select Log Analytics workspace](media/logic-apps-track-b2b-messages-omsportal/select-log-analytics-workspace.png)

1. Under **Get started with Log Analytics** > **Configure monitoring solutions**, 
choose **View solutions**.

   ![Choose "View solutions"](media/logic-apps-track-b2b-messages-omsportal/log-analytics-workspace.png)

1. On the Overview page, choose **Add**, 
which opens the **Management Solutions** list. 
From that list, select **Logic Apps B2B**. 

   ![Select Logic Apps B2B solution](media/logic-apps-track-b2b-messages-omsportal/add-b2b-solution.png)

   If you can't find the solution, at the bottom of the list, 
   choose **Load more** until the solution appears.

1. Choose **Create**, confirm the Log Analytics 
workspace where you want to install the solution, 
and then choose **Create** again.   

   ![Choose "Create" for Logic Apps B2B](media/logic-apps-track-b2b-messages-omsportal/create-b2b-solution.png)

   If you don't want to use an existing workspace, 
   you can also create a new workspace at this time.

1. When you're done, go back to your workspace's **Overview** page. 

   The Logic Apps B2B solution now appears on the Overview page. 
   When B2B messages are processed, the message count on this page is updated.

<a name="message-status-details"></a>

## View B2B message information

After B2B messages are processed, you can view 
the status and details for those messages on the 
**Logic Apps B2B** tile.

1. Go to your Logic Analytics workspace, and open the Overview page. 
Choose **Logic Apps B2B**.

   ![Updated message count](media/logic-apps-track-b2b-messages-omsportal/b2b-overview-tile.png)

   > [!NOTE]
   > By default, the **Logic Apps B2B** tile 
   > shows data based on a single day. 
   > To change the data scope to a different interval, 
   > choose the scope control at the top of the page:
   > 
   > ![Change interval](media/logic-apps-track-b2b-messages-omsportal/change-interval.png)

1. After the message status dashboard appears, 
you can view more details for a specific message type, 
which shows data based on a single day. 
Choose the tile for **AS2**, **X12**, or **EDIFACT**.

   ![View message status](media/logic-apps-track-b2b-messages-omsportal/omshomepage5.png)

   A list of messages appears for your chosen tile. 
   To learn more about the properties for each message type, 
   see these message property descriptions:

   * [AS2 message properties](#as2-message-properties)
   * [X12 message properties](#x12-message-properties)
   * [EDIFACT message properties](#EDIFACT-message-properties)

   For example, here's what an AS2 message list might look like:

   ![View AS2 messages](media/logic-apps-track-b2b-messages-omsportal/as2messagelist.png)

3. To view or export the inputs and outputs for specific messages, 
select those messages, and choose **Download**. When you're prompted, 
save the .zip file to your local computer, and then extract that file. 

   The extracted folder includes a folder for each selected message. 
   If you set up acknowledgements, 
   the message folder also includes files with acknowledgement details. 
   Each message folder has at least these files: 
   
   * Human-readable files with the input payload and output payload details
   * Encoded files with the inputs and outputs

   For each message type, you can find the folder and file name formats here:

   * [AS2 folder and file name formats](#as2-folder-file-names)
   * [X12 folder and file name formats](#x12-folder-file-names)
   * [EDIFACT folder and file name formats](#edifact-folder-file-names)

   ![Download message files](media/logic-apps-track-b2b-messages-omsportal/download-messages.png)

4. To view all actions that have the same run ID, 
on the **Log Search** page, choose a message from the message list.

   You can sort these actions by column, or search for specific results.

   ![Actions with the same run ID](media/logic-apps-track-b2b-messages-omsportal/logsearch.png)

   * To search results with prebuilt queries, choose **Favorites**.

   * Learn [how to build queries by adding filters](logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md). 
   Or learn more about [how to find data with log searches in Azure Monitor logs](../log-analytics/log-analytics-log-searches.md).

   * To change query in the search box, update the query with the 
   columns and values that you want to use as filters.

<a name="message-list-property-descriptions"></a>

## Property descriptions and name formats for AS2, X12, and EDIFACT messages

For each message type, here are the property descriptions 
and name formats for downloaded message files.

<a name="as2-message-properties"></a>

### AS2 message property descriptions

Here are the property descriptions for each AS2 message.

| Property | Description |
| --- | --- |
| Sender | The guest partner specified in **Receive Settings**, or the host partner specified in **Send Settings** for an AS2 agreement |
| Receiver | The host partner specified in **Receive Settings**, or the guest partner specified in **Send Settings** for an AS2 agreement |
| Logic App | The logic app where the AS2 actions are set up |
| Status | The AS2 message status <br>Success = Received or sent a valid AS2 message. No MDN is set up. <br>Success = Received or sent a valid AS2 message. MDN is set up and received, or MDN is sent. <br>Failed = Received an invalid AS2 message. No MDN is set up. <br>Pending = Received or sent a valid AS2 message. MDN is set up, and MDN is expected. |
| Ack | The MDN message status <br>Accepted = Received or sent a positive MDN. <br>Pending = Waiting to receive or send an MDN. <br>Rejected = Received or sent a negative MDN. <br>Not Required = MDN is not set up in the agreement. |
| Direction | The AS2 message direction |
| Correlation ID | The ID that correlates all the triggers and actions in a logic app |
| Message ID | The AS2 message ID from the AS2 message headers |
| Timestamp | The time when the AS2 action processed the message |
|          |             |

<a name="as2-folder-file-names"></a>

### AS2 name formats for downloaded message files

Here are the name formats for each downloaded AS2 message folder and files.

| Folder or file | Name format |
| :------------- | :---------- |
| Message folder | [sender]\_[receiver]\_AS2\_[correlation-ID]\_[message-ID]\_[timestamp] |
| Input, output, and if set up, acknowledgement files | **Input payload**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_input_payload.txt </p>**Output payload**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_output\_payload.txt </p></p>**Inputs**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_inputs.txt </p></p>**Outputs**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_outputs.txt |
|          |             |

<a name="x12-message-properties"></a>

### X12 message property descriptions

Here are the property descriptions for each X12 message.

| Property | Description |
| --- | --- |
| Sender | The guest partner specified in **Receive Settings**, or the host partner specified in **Send Settings** for an X12 agreement |
| Receiver | The host partner specified in **Receive Settings**, or the guest partner specified in **Send Settings** for an X12 agreement |
| Logic App | The logic app where the X12 actions are set up |
| Status | The X12 message status <br>Success = Received or sent a valid X12 message. No functional ack is set up. <br>Success = Received or sent a valid X12 message. Functional ack is set up and received, or a functional ack is sent. <br>Failed = Received or sent an invalid X12 message. <br>Pending = Received or sent a valid X12 message. Functional ack is set up, and a functional ack is expected. |
| Ack | Functional Ack (997) status <br>Accepted = Received or sent a positive functional ack. <br>Rejected = Received or sent a negative functional ack. <br>Pending = Expecting a functional ack but not received. <br>Pending = Generated a functional ack but can't send to partner. <br>Not Required = Functional ack is not set up. |
| Direction | The X12 message direction |
| Correlation ID | The ID that correlates all the triggers and actions in a logic app |
| Msg type | The EDI X12 message type |
| ICN | The Interchange Control Number for the X12 message |
| TSCN | The Transaction Set Control Number for the X12 message |
| Timestamp | The time when the X12 action processed the message |
|          |             |

<a name="x12-folder-file-names"></a>

### X12 name formats for downloaded message files

Here are the name formats for each downloaded X12 message folder and files.

| Folder or file | Name format |
| :------------- | :---------- |
| Message folder | [sender]\_[receiver]\_X12\_[interchange-control-number]\_[global-control-number]\_[transaction-set-control-number]\_[timestamp] |
| Input, output, and if set up, acknowledgement files | **Input payload**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_input_payload.txt </p>**Output payload**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_output\_payload.txt </p></p>**Inputs**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_inputs.txt </p></p>**Outputs**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_outputs.txt |
|          |             |

<a name="EDIFACT-message-properties"></a>

### EDIFACT message property descriptions

Here are the property descriptions for each EDIFACT message.

| Property | Description |
| --- | --- |
| Sender | The guest partner specified in **Receive Settings**, or the host partner specified in **Send Settings** for an EDIFACT agreement |
| Receiver | The host partner specified in **Receive Settings**, or the guest partner specified in **Send Settings** for an EDIFACT agreement |
| Logic App | The logic app where the EDIFACT actions are set up |
| Status | The EDIFACT message status <br>Success = Received or sent a valid EDIFACT message. No functional ack is set up. <br>Success = Received or sent a valid EDIFACT message. Functional ack is set up and received, or a functional ack is sent. <br>Failed = Received or sent an invalid EDIFACT message <br>Pending = Received or sent a valid EDIFACT message. Functional ack is set up, and a functional ack is expected. |
| Ack | Functional Ack (997) status <br>Accepted = Received or sent a positive functional ack. <br>Rejected = Received or sent a negative functional ack. <br>Pending = Expecting a functional ack but not received. <br>Pending = Generated a functional ack but can't send to partner. <br>Not Required = Functional Ack is not set up. |
| Direction | The EDIFACT message direction |
| Correlation ID | The ID that correlates all the triggers and actions in a logic app |
| Msg type | The EDIFACT message type |
| ICN | The Interchange Control Number for the EDIFACT message |
| TSCN | The Transaction Set Control Number for the EDIFACT message |
| Timestamp | The time when the EDIFACT action processed the message |
|          |               |

<a name="edifact-folder-file-names"></a>

### EDIFACT name formats for downloaded message files

Here are the name formats for each downloaded EDIFACT message folder and files.

| Folder or file | Name format |
| :------------- | :---------- |
| Message folder | [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_[global-control-number]\_[transaction-set-control-number]\_[timestamp] |
| Input, output, and if set up, acknowledgement files | **Input payload**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_input_payload.txt </p>**Output payload**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_output\_payload.txt </p></p>**Inputs**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_inputs.txt </p></p>**Outputs**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_outputs.txt |
|          |             |

## Next steps

* [Query for B2B messages in Azure Monitor logs](../logic-apps/logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md)
* [AS2 tracking schemas](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12 tracking schemas](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Custom tracking schemas](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)
