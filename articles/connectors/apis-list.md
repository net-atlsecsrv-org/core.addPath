---
title: Connectors for Azure Logic Apps
description: Automate workflows with connectors for Azure Logic Apps, such as built-in, managed, on-premises, integration account, ISE, and enterprise connectors
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 06/11/2020
---

# Connectors for Azure Logic Apps

Connectors provide quick access from Azure Logic Apps to events, data, and actions across other apps, services, systems, protocols, and platforms. By using connectors in your logic apps, you expand the capabilities for your cloud and on-premises apps to perform tasks with the data that you create and already have.

While Logic Apps offers [hundreds of connectors](/connectors), this article describes the *popular and more commonly used* connectors that are successfully used by thousands of apps and millions of executions for processing data and information. To find the full list of connectors and each connector's reference information, such as triggers, actions, and limits, review the connector reference pages under [Connectors overview](/connectors). Also, learn more about [triggers and actions](#triggers-actions), [Logic Apps pricing model](../logic-apps/logic-apps-pricing.md), and [Logic Apps pricing details](https://azure.microsoft.com/pricing/details/logic-apps/).

> [!TIP]
> To integrate with a service or API that doesn't have connector, you can either directly 
> call the service over a protocol such as HTTP, or create a [custom connector](#custom).

## Connector types

Connectors are available as built-in triggers and actions or as managed connectors.

<a name="built-in"></a>

* [**Built-in**](#built-ins): Built-in triggers and actions are "native" to Azure Logic Apps and help you perform these tasks for your logic apps:

  * Run on custom and advanced schedules.

  * Organize and control your logic app's workflow, for example, loops and conditions, and also to work with variables and data operations.

  * Communicate with other endpoints.

  * Receive and respond to requests.

  * Call Azure functions, Azure API Apps (Web Apps), your own APIs managed and published with Azure API Management, and nested logic apps that can receive requests.

<a name="managed-connectors"></a>

* [**Managed connectors**](#managed-api-connectors): Deployed and managed by Microsoft, these connectors provide triggers and actions for accessing cloud services, on-premises systems, or both, including Office 365, Azure Blob Storage, SQL Server, Dynamics, Salesforce, SharePoint, and more. Some connectors specifically support business-to-business (B2B) communication scenarios and require an [integration account](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) that's linked to your logic app. Before using certain connectors, you might have to first create connections, which are managed by Azure Logic Apps.

  For example, if you're using Microsoft BizTalk Server, your logic apps can connect to and communicate with your BizTalk Server by using the [BizTalk Server on-premises connector](#on-premises-connectors). You can then extend or perform BizTalk-like operations in your logic apps by using the [integration account connectors](#integration-account-connectors).

  Connectors are classified as either Standard or Enterprise. [Enterprise connectors](#enterprise-connectors) provide access to enterprise systems such as SAP, IBM MQ, and IBM 3270 for an additional cost. To determine whether a connector is Standard or Enterprise, see the technical details in each connector's reference page under [Connectors overview](/connectors).

  You can also identify connectors by using these categories, although some connectors can exist in multiple categories. For example, SAP is an Enterprise connector and an on-premises connector:

  | Category | Description |
  |----------|-------------|
  | [**Managed connectors**](#managed-api-connectors) | Create logic apps that use services such as Azure Blob Storage, Office 365, Dynamics, Power BI, OneDrive, Salesforce, SharePoint Online, and many more. |
  | [**On-premises connectors**](#on-premises-connectors) | After you install and set up the [on-premises data gateway][gateway-doc], these connectors help your logic apps access on-premises systems such as SQL Server, SharePoint Server, Oracle DB, file shares, and others. |
  | [**Integration account connectors**](#integration-account-connectors) | Available when you create and pay for an integration account, these connectors transform and validate XML, encode and decode flat files, and process business-to-business (B2B) messages with AS2, EDIFACT, and X12 protocols. |
  |||

<a name="integration-service-environment"></a>

### Connect from an integration service environment (ISE)

For logic apps that need direct access to resources in an Azure virtual network, you can create a dedicated [integration service environment (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) where you can build, deploy, and run your logic apps on dedicated resources. In the Logic App Designer, when you browse the connectors that you want to use for logic apps in an ISE, a **CORE** label appears on built-in triggers and actions, while the **ISE** label appears on some connectors.

> [!NOTE]
> Logic apps that run in an ISE and their connectors, regardless where those connectors run, 
> follow a fixed pricing plan versus the consumption-based pricing plan. For more information, 
> see [Logic Apps pricing model](../logic-apps/logic-apps-pricing.md) and 
> [Logic Apps pricing details](https://azure.microsoft.com/pricing/details/logic-apps/).

| Label | Example | Description |
|-------|---------|-------------|
| **CORE** | ![Example CORE connector](./media/apis-list/example-core-connector.png) | Built-in triggers and actions with this label run in the same ISE as your logic apps. |
| **ISE** | ![Example ISE connector](./media/apis-list/example-ise-connector.png) | Managed connectors with this label run in the same ISE as your logic apps. If you have an on-premises system that's connected to an Azure virtual network, an ISE lets your logic apps directly access that system without the [on-premises data gateway](../logic-apps/logic-apps-gateway-connection.md). Instead, you can either use that system's **ISE** connector if available, an HTTP action, or a [custom connector](#custom). For on-premises systems that don't have **ISE** connectors, use on-premises data gateway. To review available ISE connectors, see [ISE connectors](#ise-connectors). |
| No label | ![Example multi-tenant connector](./media/apis-list/example-multi-tenant-connector.png) | All other connectors without the **CORE** or **ISE** label, which you can continue to use, run in the global, multi-tenant Logic Apps service. |
|||

<a name="built-ins"></a>

## Built-in

Logic Apps provides built-in triggers and actions so that you can create schedule-based workflows, help your logic apps communicate with other apps and services, control the workflow through your logic apps, and manage or manipulate data.

| Name | Description |
|------|-------------|
| [![Schedule built-in connector][schedule-icon]<br>**Schedule**][schedule-doc] | - Run a logic app on a specified recurrence, ranging from basic to advanced schedules with the [**Recurrence** trigger][schedule-recurrence-doc]. <br>- Run a logic app that needs to handle data in continuous chunks with the [**Sliding Window** trigger][schedule-sliding-window-doc]. <br>- Pause your logic app for a specified duration with the [**Delay** action][schedule-delay-doc]. <br>- Pause your logic app until the specified date and time with the [**Delay until** action][schedule-delay-until-doc]. |
| [![Batch built-in connector][batch-icon]<br>**Batch**][batch-doc] | - Process messages in batches with the **Batch messages** trigger. <br>- Call logic apps that have existing batch triggers with the **Send messages to batch** action. |
| [![HTTP built-in connector][http-icon]<br>**HTTP**][http-doc] | Call HTTP or HTTPS endpoints with triggers and actions for HTTP. Other HTTP built-in triggers and actions include [HTTP + Swagger built-in connector][http-swagger-doc] and [HTTP + Webhook][http-webhook-doc]. |
| [![Request built-in connector][http-request-icon]<br>**Request**][http-request-doc] | - Make your logic app callable from other apps or services, trigger on Event Grid resource events, or trigger on responses to Azure Security Center alerts with the **Request** trigger. <br>- Send responses to an app or service with the **Response** action. |
| [![Azure API Management built-in connector][azure-api-management-icon]<br>**Azure API <br>Management**][azure-api-management-doc] | Call triggers and actions defined by your own APIs that you manage and publish with Azure API Management. |
| [![Azure App Services built-in connector][azure-app-services-icon]<br>**Azure App <br>Services**][azure-app-services-doc] | Call Azure API Apps, or Web Apps, hosted on Azure App Service. The triggers and actions defined by these apps appear like any other first-class triggers and actions when Swagger is included. |
| [![Azure Logic Apps built-in connector][azure-logic-apps-icon]<br>**Azure Logic <br>Apps**][nested-logic-app-doc] | Call other logic apps that start with the **Request** trigger. |
|||

### Run code from logic apps

Logic Apps provides built-in actions for running your own code in your logic app's workflow:

| Name | Description |
|------|-------------|
| [![Azure Functions built-in connector][azure-functions-icon]<br>**Azure Functions**][azure-functions-doc] | Call Azure functions that run custom code snippets (C# or Node.js) from your logic apps. |
| [![Inline Code built-in connector][inline-code-icon]<br>**Inline code**][inline-code-doc] | Add and run JavaScript code snippets from your logic apps. |
|||

### Control workflow

Logic Apps provides built-in actions for structuring and controlling the actions in your logic app's workflow:

| Name | Description |
|------|-------------|
| [![Condition built-in action][condition-icon]<br>**Condition**][condition-doc] | Evaluate a condition and run different actions based on whether the condition is true or false. |
| [![For Each built-in action][for-each-icon]<br>**For each**][for-each-doc] | Perform the same actions on every item in an array. |
| [![Scope built-in action][scope-icon]<br>**Scope**][scope-doc] | Group actions into *scopes*, which get their own status after the actions in the scope finish running. |
| [![Switch built-in action][switch-icon]<br>**Switch**][switch-doc] | Group actions into *cases*, which are assigned unique values except for the default case. Run only that case whose assigned value matches the result from an expression, object, or token. If no matches exist, run the default case. |
| [![Terminate built-in action][terminate-icon]<br>**Terminate**][terminate-doc] | Stop an actively running logic app workflow. |
| [![Until built-in action][until-icon]<br>**Until**][until-doc] | Repeat actions until the specified condition is true or some state has changed. |
|||

### Manage or manipulate data

Logic Apps provides built-in actions for working with data outputs and their formats:

| Name | Description |
|------|-------------|
| [![Data Operations built-in action][data-operations-icon]<br>**Data Operations**][data-operations-doc] | Perform operations with data: <p>- **Compose**: Create a single output from multiple inputs with various types. <br>- **Create CSV table**: Create a comma-separated-value (CSV) table from an array with JSON objects. <br>- **Create HTML table**: Create an HTML table from an array with JSON objects. <br>- **Filter array**: Create an array from items in another array that meet your criteria. <br>- **Join**: Create a string from all items in an array and separate those items with the specified delimiter. <br>- **Parse JSON**: Create user-friendly tokens from properties and their values in JSON content so that you can use those properties in your workflow. <br>- **Select**: Create an array with JSON objects by transforming items or values in another array and mapping those items to specified properties. |
| ![Date Time built-in action][date-time-icon]<br>**Date Time** | Perform operations with timestamps: <p>- **Add to time**: Add the specified number of units to a timestamp. <br>- **Convert time zone**: Convert a timestamp from the source time zone to the target time zone. <br>- **Current time**: Return the current timestamp as a string. <br>- **Get future time**: Return the current timestamp plus the specified time units. <br>- **Get past time**: Return the current timestamp minus the specified time units. <br>- **Subtract from time**: Subtract a number of time units from a timestamp. |
| [![Variables built-in action][variables-icon]<br>**Variables**][variables-doc] | Perform operations with variables: <p>- **Append to array variable**: Insert a value as the last item in an array stored by a variable. <br>- **Append to string variable**: Insert a value as the last character in a string stored by a variable. <br>- **Decrement variable**: Decrease a variable by a constant value. <br>- **Increment variable**: Increase a variable by a constant value. <br>- **Initialize variable**: Create a variable and declare its data type and initial value. <br>- **Set variable**: Assign a different value to an existing variable. |
|||

<a name="managed-api-connectors"></a>

## Managed connectors

Logic Apps provides these popular Standard connectors for automating tasks, processes, and workflows with these services or systems:

| Name | Description |
|------|-------------|
| [![Azure Service Bus managed connector][azure-service-bus-icon]<br>**Azure Service Bus**][azure-service-bus-doc] | Manage asynchronous messages, sessions, and topic subscriptions with the most commonly used connector in Logic Apps. |
| [![SQL Server managed connector][sql-server-icon]<br>**SQL Server**][sql-server-doc] | Connect to your SQL Server on premises or an Azure SQL Database in the cloud so that you can manage records, run stored procedures, or perform queries. |
| [![Azure Blob Storage managed connector][azure-blob-storage-icon]<br>**Azure Blob<br>Storage**][azure-blob-storage-doc] | Connect to your storage account so that you can create and manage blob content. |
| [![Office 365 Outlook managed connector][office-365-outlook-icon]<br>**Office 365<br>Outlook**][office-365-outlook-doc] | Connect to your work or school email account so that you can create and manage emails, tasks, calendar events and meetings, contacts, requests, and more. |
| [![SFTP-SSH managed connector][sftp-ssh-icon]<br>**SFTP-SSH**][sftp-ssh-doc] | Connect to SFTP servers that you can access from the internet by using SSH so that you can work with your files and folders. |
| [![SharePoint Online managed connector][sharepoint-online-icon]<br>**SharePoint<br>Online**][sharepoint-online-doc] | Connect to SharePoint Online so that you can manage files, attachments, folders, and more. |
| [![Azure Queues managed connector][azure-queues-icon]<br>**Azure <br>Queues**][azure-queues-doc] | Connect to your Azure Storage account so that you can create and manage queues and messages. |
| [![FTP managed connector][ftp-icon]<br>**FTP**][ftp-doc] | Connect to FTP servers you can access from the internet so that you can work with your files and folders. |
| [![File System managed connector][file-system-icon]<br>**File <br>System**][file-system-doc] | Connect to your on-premises file share so that you can create and manage files. |
| [![Azure Event Hubs managed connector][azure-event-hubs-icon]<br>**Azure Event Hubs**][azure-event-hubs-doc] | Consume and publish events through an Event Hub. For example, get output from your logic app with Event Hubs, and then send that output to a real-time analytics provider. |
| [![Azure Event Grid managed connector][azure-event-grid-icon]<br>**Azure Event**<br>**Grid**][azure-event-grid-doc] | Monitor events published by an Event Grid, for example, when Azure resources or third-party resources change. |
| [![Salesforce managed connector][salesforce-icon]<br>**Salesforce**][salesforce-doc] | Connect to your Salesforce account so that you can create and manage items such as records, jobs, objects, and more. |
|||

<a name="on-premises-connectors"></a>

## On-premises connectors

Here are some commonly used Standard connectors that Logic Apps provides for accessing data and resources in on-premises systems. Before you can create a connection to an on-premises system, you must first [download, install, and set up an on-premises data gateway][gateway-doc]. This gateway provides a secure communication channel without having to set up the necessary network infrastructure.

:::row:::
    :::column:::
        [![BizTalk Server connector][biztalk-server-icon]<br>**BizTalk** <br>**Server**][biztalk-server-doc]
    :::column-end:::
    :::column:::
        [![File System connector][file-system-icon]<br>**File <br>System**][file-system-doc]
    :::column-end:::
    :::column:::
        [![DB2 connector][ibm-db2-icon]<br>**IBM DB2**][ibm-db2-doc]
    :::column-end:::
    :::column:::
        [![Informix connector][ibm-informix-icon]<br>**IBM** <br>**Informix**][ibm-informix-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![MySQL connector][mysql-icon]<br>**MySQL**][mysql-doc]
    :::column-end:::
    :::column:::
        [![Oracle DB connector][oracle-db-icon]<br>**Oracle DB**][oracle-db-doc]
    :::column-end:::
    :::column:::
        [![PostgreSQL connector][postgre-sql-icon]<br>**PostgreSQL**][postgre-sql-doc]
    :::column-end:::
    :::column:::
        [![SharePoint Server connector][sharepoint-server-icon]<br>**SharePoint <br>Server**][sharepoint-server-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![SQL Server connector][sql-server-icon]<br>**SQL <br>Server**][sql-server-doc]
    :::column-end:::
    :::column:::
        [![Teradata connector][teradata-icon]<br>**Teradata**][teradata-doc]
    :::column-end:::
    :::column:::
        
    :::column-end:::
    :::column:::
        
    :::column-end:::
:::row-end:::

<a name="integration-account-connectors"></a>

## Integration account connectors

Logic Apps provides Standard connectors for building business-to-business (B2B) solutions with your logic apps when you create and pay for an [integration account](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), which is available through the Enterprise Integration Pack (EIP) in Azure. With this account, you can create and store B2B artifacts such as trading partners, agreements, maps, schemas, certificates, and so on. To use these artifacts, associate your logic apps with your integration account. If you currently use BizTalk Server, these connectors might seem familiar already.

:::row:::
    :::column:::
        [![AS2 decoding action][as2-icon]<br>**AS2 <br>decoding**][as2-doc]
    :::column-end:::
    :::column:::
        [![AS2 encoding action][as2-icon]<br>**AS2 <br>encoding**][as2-doc]
    :::column-end:::
    :::column:::
        [![EDIFACT decoding action][edifact-icon]<br>**EDIFACT <br>decoding**][edifact-decode-doc]
    :::column-end:::
    :::column:::
        [![EDIFACT encoding action][edifact-icon]<br>**EDIFACT <br>encoding**][edifact-encode-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Flat file decoding action][flat-file-decode-icon]<br>**Flat file <br>decoding**][flat-file-decode-doc]
    :::column-end:::
    :::column:::
        [![Flat file encoding action][flat-file-encode-icon]<br>**Flat file <br>encoding**][flat-file-encode-doc]
    :::column-end:::
    :::column:::
        [![Integration account action][integration-account-icon]<br>**Integration <br>account**][integration-account-doc]
    :::column-end:::
    :::column:::
        [![Liquid transforms action][liquid-icon]<br>**Liquid** <br>**transforms**][json-liquid-transform-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![X12 decoding action][x12-icon]<br>**X12 <br>decoding**][x12-decode-doc]
    :::column-end:::
    :::column:::
        [![X12 encoding action][x12-icon]<br>**X12 <br>encoding**][x12-encode-doc]
    :::column-end:::
    :::column:::
        [![XML transforms action][xml-transform-icon]<br>**XML** <br>**transforms**][xml-transform-doc]
    :::column-end:::
    :::column:::
        [![XML validation action][xml-validate-icon]<br>**XML <br>validation**][xml-validate-doc]
    :::column-end:::
:::row-end:::

<a name="enterprise-connectors"></a>

## Enterprise connectors

Logic Apps provides these Enterprise connectors for accessing enterprise systems, such as SAP and IBM MQ:

:::row:::
    :::column:::
        [![IBM 3270 connector][ibm-3270-icon]<br>**IBM 3270**][ibm-3270-doc]
    :::column-end:::
    :::column:::
        [![MQ connector][ibm-mq-icon]<br>**IBM MQ**][ibm-mq-doc]
    :::column-end:::
    :::column:::
        [![SAP connector][sap-icon]<br>**SAP**][sap-connector-doc]
    :::column-end:::
    :::column:::
        
    :::column-end:::
:::row-end:::

<a name="ise-connectors"></a>

## ISE connectors

For logic apps that you create and run in a dedicated [integration service environment (ISE)](#integration-service-environment), the Logic App Designer identifies built-in triggers and actions that run in your ISE by using the **CORE** label. Managed connectors that run in an ISE display the **ISE** label, while connectors that run in the global, multi-tenant Logic Apps service don't display either label. This list shows the connectors that currently have ISE versions:

:::row:::
    :::column:::
        [![AS2 ISE connector][as2-icon]<br>**AS2**][as2-doc]
    :::column-end:::
    :::column:::
        [![Azure Automation ISE connector][azure-automation-icon]<br>**Azure <br>Automation**][azure-automation-doc]
    :::column-end:::
    :::column:::
        [![Azure Blob Storage ISE connector][azure-blob-storage-icon]<br>**Azure Blob<br>Storage**][azure-blob-storage-doc]
    :::column-end:::
    :::column:::
        [![Azure Cosmos DB ISE connector][azure-cosmos-db-icon]<br>**Azure Cosmos <br> DB**][azure-cosmos-db-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Azure Event Hubs ISE connector][azure-event-hubs-icon]<br>**Azure Event <br>Hubs**][azure-event-hubs-doc]
    :::column-end:::
    :::column:::
        [![Azure Event Grid ISE connector][azure-event-grid-icon]<br>**Azure Event <br>Grid**][azure-event-grid-doc]
    :::column-end:::
    :::column:::
        [![Azure File Storage ISE connector][azure-file-storage-icon]<br>**Azure File<br>Storage**][azure-file-storage-doc]
    :::column-end:::
    :::column:::
        [![Azure Key Vault ISE connector][azure-key-vault-icon]<br>**Azure Key <br>Vault**][azure-key-vault-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Azure Monitor Logs ISE connector][azure-monitor-logs-icon]<br>**Azure Monitor <br>Logs**][azure-monitor-logs-doc]
    :::column-end:::
    :::column:::
        [![Azure Service Bus ISE connector][azure-service-bus-icon]<br>**Azure Service <br>Bus**][azure-service-bus-doc]
    :::column-end:::
    :::column:::
        [![Azure Synapse Analytics ISE connector][azure-sql-data-warehouse-icon]<br>**Azure SQL Data <br>Warehouse**][azure-sql-data-warehouse-doc]
    :::column-end:::
    :::column:::
        [![Azure Table Storage ISE connector][azure-table-storage-icon]<br>**Azure Table <br>Storage**][azure-table-storage-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Azure Queues ISE connector][azure-queues-icon]<br>**Azure <br>Queues**][azure-queues-doc]
    :::column-end:::
    :::column:::
        [![EDIFACT ISE connector][edifact-icon]<br>**EDIFACT**][edifact-doc]
    :::column-end:::
    :::column:::
        [![File System ISE connector][file-system-icon]<br>**File <br>System**][file-system-doc]
    :::column-end:::
    :::column:::
        [![FTP ISE connector][ftp-icon]<br>**FTP**][ftp-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![IBM 3270 ISE connector][ibm-3270-icon]<br>**IBM 3270**][ibm-3270-doc]
    :::column-end:::
    :::column:::
        [![DB2 ISE connector][ibm-db2-icon]<br>**IBM DB2**][ibm-db2-doc]
    :::column-end:::
    :::column:::
        [![MQ ISE connector][ibm-mq-icon]<br>**IBM MQ**][ibm-mq-doc]
    :::column-end:::
    :::column:::
        [![SAP ISE connector][sap-icon]<br>**SAP**][sap-connector-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![SFTP-SSH ISE connector][sftp-ssh-icon]<br>**SFTP-SSH**][sftp-ssh-doc]
    :::column-end:::
    :::column:::
        [![SMTP ISE connector][smtp-icon]<br>**SMTP**][smtp-doc]
    :::column-end:::
    :::column:::
        [![SQL Server ISE connector][sql-server-icon]<br>**SQL <br>Server**][sql-server-doc]
    :::column-end:::
    :::column:::
        [![X12 ISE connector][x12-icon]<br>**X12**][x12-doc]
    :::column-end:::
:::row-end:::

For more information, see these topics:

* [Access to Azure virtual network resources from Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)
* [Logic Apps pricing model](../logic-apps/logic-apps-pricing.md)
* [Connect to Azure virtual networks from Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

<a name="triggers-actions"></a>

## Triggers and action types

Connectors can provide *triggers*, *actions*, or both. A *trigger* is the first step in any logic app, usually specifying the event that fires the trigger and starts running your logic app. For example, the FTP connector has a trigger that starts your logic app "when a file is added or modified". Some triggers regularly check for the specified event or data and then fire when they detect the specified event or data. Other triggers wait but fire instantly when a specific event happens or when new data is available. Triggers also pass along any required data to your logic app. Your logic app can read and use that data throughout the workflow. For example, the Office 365 Outlook connector has a trigger, "When a new email arrives", that can pass the content from that email into your logic app's workflow.

After a trigger fires, Azure Logic Apps creates an instance of your logic app and starts running the *actions* in your logic app's workflow. Actions are the steps that follow the trigger and perform tasks in your logic app's workflow. For example, you can create a logic app that gets customer data from a SQL database and process that data in later actions.

Here are the general kinds of triggers that Azure Logic Apps provides:

* *Recurrence trigger*: This trigger runs on a specified schedule and isn't tightly associated with a particular service or system.

* *Polling trigger*: This trigger regularly polls a specific service or system based on the specified schedule, checking for new data or whether a specific event happened. If new data is available or the specific event happened, the trigger creates and runs a new instance of your logic app, which can now use the data that's passed as input.

* *Push trigger*: This trigger waits and listens for new data or for an event to happen. When new data is available or when the event happens, the trigger creates and runs new instance of your logic app, which can now use the data that's passed as input.

<a name="connections"></a>

## Connector configuration

Each connector's triggers and actions provide their own properties for you to configure. Many connectors also require that you first create a *connection* to the target service or system and provide authentication credentials or other configuration details before you can use a trigger or action in your logic app. For example, before you can access and working with your Office 365 Outlook email account, you must authorize a connection to that account.

For connectors that use Azure Active Directory (Azure AD) OAuth, creating a connection means signing into the service, such as Office 365, Salesforce, or GitHub, where your access token is [encrypted](../security/fundamentals/encryption-overview.md) and securely stored in an Azure secret store. Other connectors, such as FTP and SQL, require a connection that has configuration details, such as the server address, username, and password. These connection configuration details are also encrypted and securely stored. Learn more about [encryption in Azure](../security/fundamentals/encryption-overview.md).

Connections can access the target service or system for as long as that service or system allows. For services that use Azure AD OAuth connections, such as Office 365 and Dynamics, Azure Logic Apps refreshes access tokens indefinitely. Other services might have limits on how long Azure Logic Apps can use a token without refreshing. Generally, some actions invalidate all access tokens, such as changing your password.

<a name="custom"></a>

## Custom APIs and connectors

To call APIs that run custom code or aren't available as connectors, you can extend the Logic Apps platform by [creating custom API Apps](../logic-apps/logic-apps-create-api-app.md). You can also [create custom connectors](../logic-apps/custom-connector-overview.md) for *any* REST or SOAP-based APIs, which make those APIs available to any logic app in your Azure subscription. To make custom API Apps or connectors public for anyone to use in Azure, you can [submit connectors for Microsoft certification](/connectors/custom-connectors/submit-certification).

> [!NOTE]
> Logic apps that you deploy and run in an [integration service environment (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 
> can directly access resources in an Azure virtual network. If you have custom connectors that require the on-premises data gateway, 
> and you created those connectors outside an ISE, logic apps in an ISE can also use those connectors.
>
> Custom connectors created within an ISE don't work with the on-premises data gateway. However, these connectors can directly access 
> on-premises data sources that are connected to an Azure virtual network hosting the ISE. So, logic apps in an ISE most likely don't 
> need the data gateway when communicating with those resources.
>
> For more information about creating ISEs, see [Connect to Azure virtual networks from Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

<a name="block-connections"></a>

## Block creating connections

If your organization doesn't permit connecting to specific resources by using their connectors in Azure Logic Apps, you can [block the capability to create those connections](../logic-apps/block-connections-connectors.md) for specific connectors in logic app workflows by using [Azure Policy](../governance/policy/overview.md). For more information, see [Block connections created by specific connectors in Azure Logic Apps](../logic-apps/block-connections-connectors.md).

## Get ready for deployment

Although you create connections from within a logic app, connections are separate Azure resources with their own resource definitions. To review these connection resource definitions, [download your logic app from Azure into Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md), which is the easiest way to create a valid parameterized logic app template that's mostly ready for deployment.

## Next steps

* View the [full connector list](/connectors)
* [Create your first logic app](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Create custom connectors for logic apps](/connectors/custom-connectors/)
* [Create custom APIs for logic apps](../logic-apps/logic-apps-create-api-app.md)

<!-- Built-ins icons -->
[azure-api-management-icon]: ./media/apis-list/azure-api-management.png
[azure-app-services-icon]: ./media/apis-list/azure-app-services.png
[azure-functions-icon]: ./media/apis-list/azure-functions.png
[azure-logic-apps-icon]: ./media/apis-list/azure-logic-apps.png
[batch-icon]: ./media/apis-list/batch.png
[condition-icon]: ./media/apis-list/condition.png
[data-operations-icon]: ./media/apis-list/data-operations.png
[date-time-icon]: ./media/apis-list/date-time.png
[for-each-icon]: ./media/apis-list/for-each-loop.png
[http-icon]: ./media/apis-list/http.png
[http-request-icon]: ./media/apis-list/request.png
[http-response-icon]: ./media/apis-list/response.png
[http-swagger-icon]: ./media/apis-list/http-swagger.png
[http-webhook-icon]: ./media/apis-list/http-webhook.png
[inline-code-icon]: ./media/apis-list/inline-code.png
[schedule-icon]: ./media/apis-list/recurrence.png
[scope-icon]: ./media/apis-list/scope.png
[switch-icon]: ./media/apis-list/switch.png
[terminate-icon]: ./media/apis-list/terminate.png
[until-icon]: ./media/apis-list/until.png
[variables-icon]: ./media/apis-list/variables.png

<!--Managed connector icons-->
[appfigures-icon]: ./media/apis-list/appfigures.png
[asana-icon]: ./media/apis-list/asana.png
[azure-automation-icon]: ./media/apis-list/azure-automation.png
[azure-blob-storage-icon]: ./media/apis-list/azure-blob-storage.png
[azure-cognitive-services-text-analytics-icon]: ./media/apis-list/azure-cognitive-services-text-analytics.png
[azure-cosmos-db-icon]: ./media/apis-list/azure-cosmos-db.png
[azure-data-lake-icon]: ./media/apis-list/azure-data-lake.png
[azure-devops-icon]: ./media/apis-list/azure-devops.png
[azure-document-db-icon]: ./media/apis-list/azure-document-db.png
[azure-event-grid-icon]: ./media/apis-list/azure-event-grid.png
[azure-event-grid-publish-icon]: ./media/apis-list/azure-event-grid-publish.png
[azure-event-hubs-icon]: ./media/apis-list/azure-event-hubs.png
[azure-file-storage-icon]: ./media/apis-list/azure-file-storage.png
[azure-key-vault-icon]: ./media/apis-list/azure-key-vault.png
[azure-ml-icon]: ./media/apis-list/azure-ml.png
[azure-monitor-logs-icon]: ./media/apis-list/azure-monitor-logs.png
[azure-queues-icon]: ./media/apis-list/azure-queues.png
[azure-resource-manager-icon]: ./media/apis-list/azure-resource-manager.png
[azure-service-bus-icon]: ./media/apis-list/azure-service-bus.png
[azure-sql-data-warehouse-icon]: ./media/apis-list/azure-sql-data-warehouse.png
[azure-table-storage-icon]: ./media/apis-list/azure-table-storage.png
[basecamp-3-icon]: ./media/apis-list/basecamp.png
[bitbucket-icon]: ./media/apis-list/bitbucket.png
[bitly-icon]: ./media/apis-list/bitly.png
[biztalk-server-icon]: ./media/apis-list/biztalk.png
[blogger-icon]: ./media/apis-list/blogger.png
[campfire-icon]: ./media/apis-list/campfire.png
[common-data-service-icon]: ./media/apis-list/common-data-service.png
[dynamics-365-financials-icon]: ./media/apis-list/dynamics-365-financials.png
[dynamics-365-operations-icon]: ./media/apis-list/dynamics-365-operations.png
[easy-redmine-icon]: ./media/apis-list/easyredmine.png
[file-system-icon]: ./media/apis-list/file-system.png
[ftp-icon]: ./media/apis-list/ftp.png
[github-icon]: ./media/apis-list/github.png
[google-calendar-icon]: ./media/apis-list/google-calendar.png
[google-drive-icon]: ./media/apis-list/google-drive.png
[google-sheets-icon]: ./media/apis-list/google-sheet.png
[google-tasks-icon]: ./media/apis-list/google-tasks.png
[hipchat-icon]: ./media/apis-list/hipchat.png
[ibm-3270-icon]: ./media/apis-list/ibm-3270.png
[ibm-db2-icon]: ./media/apis-list/ibm-db2.png
[ibm-informix-icon]: ./media/apis-list/ibm-informix.png
[ibm-mq-icon]: ./media/apis-list/ibm-mq.png
[insightly-icon]: ./media/apis-list/insightly.png
[instagram-icon]: ./media/apis-list/instagram.png
[instapaper-icon]: ./media/apis-list/instapaper.png
[jira-icon]: ./media/apis-list/jira.png
[mandrill-icon]: ./media/apis-list/mandrill.png
[mysql-icon]: ./media/apis-list/mysql.png
[office-365-outlook-icon]: ./media/apis-list/office-365.png
[onedrive-icon]: ./media/apis-list/onedrive.png
[onedrive-for-business-icon]: ./media/apis-list/onedrive-business.png
[oracle-db-icon]: ./media/apis-list/oracle-db.png
[outlook.com-icon]: ./media/apis-list/outlook.png
[pagerduty-icon]: ./media/apis-list/pagerduty.png
[pinterest-icon]: ./media/apis-list/pinterest.png
[postgre-sql-icon]: ./media/apis-list/postgre-sql.png
[project-online-icon]: ./media/apis-list/projecton-line.png
[redmine-icon]: ./media/apis-list/redmine.png
[salesforce-icon]: ./media/apis-list/salesforce.png
[sap-icon]: ./media/apis-list/sap.png
[send-grid-icon]: ./media/apis-list/sendgrid.png
[sftp-ssh-icon]: ./media/apis-list/sftp.png
[sharepoint-online-icon]: ./media/apis-list/sharepoint-online.png
[sharepoint-server-icon]: ./media/apis-list/sharepoint-server.png
[slack-icon]: ./media/apis-list/slack.png
[smartsheet-icon]: ./media/apis-list/smartsheet.png
[smtp-icon]: ./media/apis-list/smtp.png
[sparkpost-icon]: ./media/apis-list/sparkpost.png
[sql-server-icon]: ./media/apis-list/sql.png
[teradata-icon]: ./media/apis-list/teradata.png
[todoist-icon]: ./media/apis-list/todoist.png
[twilio-icon]: ./media/apis-list/twilio.png
[vimeo-icon]: ./media/apis-list/vimeo.png
[wordpress-icon]: ./media/apis-list/wordpress.png
[youtube-icon]: ./media/apis-list/youtube.png

<!-- Enterprise Integration Pack icons -->
[as2-icon]: ./media/apis-list/as2.png
[edifact-icon]: ./media/apis-list/edifact.png
[flat-file-encode-icon]: ./media/apis-list/flat-file-encoding.png
[flat-file-decode-icon]: ./media/apis-list/flat-file-decoding.png
[integration-account-icon]: ./media/apis-list/integration-account.png
[liquid-icon]: ./media/apis-list/liquid-transform.png
[x12-icon]: ./media/apis-list/x12.png
[xml-validate-icon]: ./media/apis-list/xml-validation.png
[xml-transform-icon]: ./media/apis-list/xsl-transform.png

<!--Other doc links-->
[gateway-doc]: ../logic-apps/logic-apps-gateway-connection.md "Connect to data sources on-premises from logic apps with on-premises data gateway"

<!--Built-in doc links-->
[azure-api-management-doc]: ../api-management/get-started-create-service-instance.md "Create an Azure API Management service instance for managing and publishing your APIs"
[azure-app-services-doc]: ../logic-apps/logic-apps-custom-api-host-deploy-call.md "Integrate logic apps with App Service API Apps"
[azure-functions-doc]: ../logic-apps/logic-apps-azure-functions.md "Integrate logic apps with Azure Functions"
[batch-doc]: ../logic-apps/logic-apps-batch-process-send-receive-messages.md "Process messages in groups, or as batches"
[condition-doc]: ../logic-apps/logic-apps-control-flow-conditional-statement.md "Evaluate a condition and run different actions based on whether the condition is true or false"
[for-each-doc]: ../logic-apps/logic-apps-control-flow-loops.md#foreach-loop "Perform the same actions on every item in an array"
[http-doc]: ./connectors-native-http.md "Call HTTP or HTTPS endpoints from your logic apps"
[http-request-doc]: ./connectors-native-reqres.md "Receive HTTP requests in your logic apps"
[http-response-doc]: ./connectors-native-reqres.md "Respond to HTTP requests from your logic apps"
[http-swagger-doc]: ./connectors-native-http-swagger.md "Call REST endpoints from your logic apps"
[http-webhook-doc]: ./connectors-native-webhook.md "Wait for specific events from HTTP or HTTPS endpoints"
[inline-code-doc]: ../logic-apps/logic-apps-add-run-inline-code.md "Add and run JavaScript code snippets from your logic apps"
[nested-logic-app-doc]: ../logic-apps/logic-apps-http-endpoint.md "Integrate logic apps with nested workflows"
[query-doc]: ../logic-apps/logic-apps-perform-data-operations.md#filter-array-action "Select and filter arrays with the Query action"
[schedule-doc]: ../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md "Run logic apps based a schedule"
[schedule-delay-doc]: ./connectors-native-delay.md "Delay running the next action"
[schedule-delay-until-doc]: ./connectors-native-delay.md "Delay running the next action"
[schedule-recurrence-doc]:  ./connectors-native-recurrence.md "Run logic apps on a recurring schedule"
[schedule-sliding-window-doc]: ./connectors-native-sliding-window.md "Run logic apps that need to handle data in contiguous chunks"
[scope-doc]: ../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md "Organize actions into groups, which get their own status after the actions in group finish running"
[switch-doc]: ../logic-apps/logic-apps-control-flow-switch-statement.md "Organize actions into cases, which are assigned unique values. Run only the case whose value matches the result from an expression, object, or token. If no matches exist, run the default case"
[terminate-doc]: ../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action "Stop or cancel an actively running workflow for your logic app"
[until-doc]: ../logic-apps/logic-apps-control-flow-loops.md#until-loop "Repeat actions until the specified condition is true or some state has changed"
[data-operations-doc]: ../logic-apps/logic-apps-perform-data-operations.md "Perform data operations such as filtering arrays or creating CSV and HTML tables"
[variables-doc]: ../logic-apps/logic-apps-create-variables-store-values.md "Perform operations with variables, such as initialize, set, increment, decrement, and append to string or array variable"

<!--Managed connector doc links-->
[azure-automation-doc]: /connectors/azureautomation/ "Create and manage automation jobs for your cloud and on-premises infrastructure"
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "Manage files in your blob container with Azure blob storage connector"
[azure-cosmos-db-doc]: /connectors/documentdb/ "Connect to Azure Cosmos DB so that you can access documents and stored procedures"
[azure-event-grid-doc]: ../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md "Monitor events published by an Event Grid, for example, when Azure resources or third-party resources change"
[azure-event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "Connect to Azure Event Hubs so that you can receive and send events between logic apps and Event Hubs"
[azure-file-storage-doc]: /connectors/azurefile/ "Connect to your Azure Storage account so that you can create, update, get, and delete files"
[azure-key-vault-doc]: /connectors/keyvault/ "Connect to your Azure Key Vault so that you can manage your secrets and keys"
[azure-monitor-logs-doc]: /connectors/azuremonitorlogs/ "Run queries against Azure Monitor Logs across Log Analytics workspaces and Application Insights components"
[azure-queues-doc]: /connectors/azurequeues/ "Connect to your Azure Storage account so that you can create and manage queues and messages"
[azure-service-bus-doc]: ./connectors-create-api-servicebus.md "Send messages from Service Bus Queues and Topics and receive messages from Service Bus Queues and Subscriptions"
[azure-sql-data-warehouse-doc]: /connectors/sqldw/ "Connect to Azure Synapse Analytics so that you can view your data"
[azure-table-storage-doc]: /connectors/azuretables/ "Connect to your Azure Storage account so that you can create, update, and query tables and more"
[biztalk-server-doc]: /connectors/biztalk/ "Connect to your BizTalk Server so that you can run BizTalk-based applications side by side with Azure Logic Apps"
[file-system-doc]: ../logic-apps/logic-apps-using-file-connector.md "Connect to an on-premises file system"
[ftp-doc]: ./connectors-create-api-ftp.md "Connect to an FTP / FTPS server for FTP tasks, like uploading, getting, deleting files, and more"
[github-doc]: ./connectors-create-api-github.md "Connect to GitHub and track issues"
[google-calendar-doc]: ./connectors-create-api-googlecalendar.md "Connects to Google Calendar and can manage calendar"
[google-sheets-doc]: ./connectors-create-api-googlesheet.md "Connect to Google Sheets so that you can modify your sheets"
[google-tasks-doc]: ./connectors-create-api-googletasks.md "Connects to Google Tasks so that you can manage your tasks"
[ibm-3270-doc]: ./connectors-run-3270-apps-ibm-mainframe-create-api-3270.md "Connect to 3270 apps on IBM mainframes"
[ibm-db2-doc]: ./connectors-create-api-db2.md "Connect to IBM DB2 in the cloud or on-premises. Update a row, get a table, and more"
[ibm-informix-doc]: ./connectors-create-api-informix.md "Connect to Informix in the cloud or on-premises. Read a row, list the tables, and more"
[ibm-mq-doc]: ./connectors-create-api-mq.md "Connect to IBM MQ on-premises or in Azure to send and receive messages"
[instagram-doc]: ./connectors-create-api-instagram.md "Connect to Instagram. Trigger or act on events"
[mandrill-doc]: ./connectors-create-api-mandrill.md "Connect to Mandrill for communication"
[mysql-doc]: /connectors/mysql/ "Connect to your on-premises MySQL database so that you can read and write data"
[office-365-outlook-doc]: ./connectors-create-api-office365-outlook.md "Connect to your work or school account so that you can send and receive emails, manage your calendar and contacts, and more"
[onedrive-doc]: ./connectors-create-api-onedrive.md "Connect to your personal Microsoft OneDrive so that you can upload, delete, list files, and more"
[onedrive-for-business-doc]: ./connectors-create-api-onedriveforbusiness.md "Connect to your business Microsoft OneDrive so that you can upload, delete, list your files, and more"
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "Connect to an Oracle database so that you can add, insert, delete rows, and more"
[outlook.com-doc]: ./connectors-create-api-outlook.md "Connect to your Outlook mailbox so that you can manage your email, calendars, contacts, and more"
[postgre-sql-doc]: /connectors/postgresql/ "Connect to your PostgreSQL database so that you can read data from tables"
[salesforce-doc]: ./connectors-create-api-salesforce.md "Connect to your Salesforce account. Manage accounts, leads, opportunities, and more"
[sap-connector-doc]: ../logic-apps/logic-apps-using-sap-connector.md "Connect to an on-premises SAP system"
[sendgrid-doc]: ./connectors-create-api-sendgrid.md "Connect to SendGrid. Send email and manage recipient lists"
[sftp-ssh-doc]: ./connectors-sftp-ssh.md "Connect to your SFTP account by using SSH. Upload, get, delete files, and more"
[sharepoint-server-doc]: ./connectors-create-api-sharepoint.md "Connect to SharePoint on-premises server. Manage documents, list items, and more"
[sharepoint-online-doc]: ./connectors-create-api-sharepoint.md "Connect to SharePoint Online. Manage documents, list items, and more"
[slack-doc]: ./connectors-create-api-slack.md "Connect to Slack and post messages to Slack channels"
[smtp-doc]: ./connectors-create-api-smtp.md "Connect to an SMTP server and send email with attachments"
[sparkpost-doc]: ./connectors-create-api-sparkpost.md "Connects to SparkPost for communication"
[sql-server-doc]: ./connectors-create-api-sqlazure.md "Connect to Azure SQL Database or SQL Server. Create, update, get, and delete entries in a SQL database table"
[teradata-doc]: /connectors/teradata/ "Connect to your Teradata database to read data from tables"
[twilio-doc]: ./connectors-create-api-twilio.md "Connect to Twilio. Send and get messages, get available numbers, manage incoming phone numbers, and more"
[youtube-doc]: ./connectors-create-api-youtube.md "Connect to YouTube. Manage your videos and channels"

<!--Enterprise Intregation Pack doc links-->
[as2-doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "Encode and decode messages that use the AS2 protocol"
[edifact-doc]: ../logic-apps/logic-apps-enterprise-integration-edifact.md "Encode and decode messages that use the EDIFACT protocol"
[edifact-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "Decode messages that use the EDIFACT protocol"
[edifact-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "Encode messages that use the EDIFACT protocol"
[flat-file-decode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Learn about enterprise integration flat file"
[flat-file-encode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Learn about enterprise integration flat file"
[integration-account-doc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "Manage metadata for integration account artifacts"
[json-liquid-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "Transform JSON with Liquid templates"
[x12-doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "Encode and decode messages that use the X12 protocol"
[x12-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "Decode messages that use the X12 protocol"
[x12-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "Encode messages that use the X12 protocol"
[xml-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "Transform XML messages"
[xml-validate-doc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "Validate XML messages"
