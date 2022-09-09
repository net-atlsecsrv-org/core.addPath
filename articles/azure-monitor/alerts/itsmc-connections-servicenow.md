---
title: Connect ServiceNow with IT Service Management Connector
description: Learn how to connect ServiceNow with the IT Service Management Connector (ITSMC) in Azure Monitor to centrally monitor and manage ITSM work items.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020

---

# Connect ServiceNow with IT Service Management Connector

This article shows you how to configure the connection between a ServiceNow instance and the IT Service Management Connector (ITSMC) in Log Analytics, so you can centrally manage your IT Service Management (ITSM) work items.

## Prerequisites
Ensure that you meet the following prerequisites for the connection.

### ITSMC installation

For information about installing ITSMC, see [Add the IT Service Management Connector solution](./itsmc-definition.md#add-it-service-management-connector).

> [!NOTE]
> ITSMC supports only the official software as a service (SaaS) offering from ServiceNow. Private deployments of ServiceNow are not supported.

### OAuth setup

ServiceNow supported versions include Paris, Orlando, New York, Madrid, London, Kingston, Jakarta, Istanbul, Helsinki, and Geneva.

ServiceNow admins must generate a client ID and client secret for their ServiceNow instance. See the following information as required:

- [Set up OAuth for Paris](https://docs.servicenow.com/bundle/paris-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Set up OAuth for Orlando](https://docs.servicenow.com/bundle/orlando-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Set up OAuth for New York](https://docs.servicenow.com/bundle/newyork-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Set up OAuth for Madrid](https://docs.servicenow.com/bundle/madrid-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Set up OAuth for London](https://docs.servicenow.com/bundle/london-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Set up OAuth for Kingston](https://docs.servicenow.com/bundle/kingston-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Set up OAuth for Jakarta](https://docs.servicenow.com/bundle/jakarta-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Set up OAuth for Istanbul](https://docs.servicenow.com/bundle/istanbul-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Set up OAuth for Helsinki](https://docs.servicenow.com/bundle/helsinki-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Set up OAuth for Geneva](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/security/task/t_SettingUpOAuth.html)

As a part of setting up OAuth, we recommend:

1. [Create an endpoint for clients to access the instance](https://docs.servicenow.com/bundle/newyork-platform-administration/page/administer/security/task/t_CreateEndpointforExternalClients.html).

1. Update the lifespan of the refresh token:

   1. On the **ServiceNow** pane, search for **System OAuth**, and then select **Application Registry**. 
   1. Select the name of the OAuth that was defined, and change **Refresh Token Lifespan** to **7,776,000 seconds** (90 days). 
   1. Select **Update**. 

1. Establish an internal procedure to ensure that the connection remains alive. A couple of days before the expected expiration of the refresh token lifespan, perform the following operations:

   1. [Complete a manual sync process for ITSM connector configuration](./itsmc-resync-servicenow.md).

   1. Revoke to the old refresh token. We don't recommend keeping old keys for security reasons. 
   
      1. On the **ServiceNow** pane, search for **System OAuth**, and then select **Manage Tokens**. 
      
      1. Select the old token from the list according to the OAuth name and expiration date.

         ![Screenshot that shows a list of tokens for OAuth.](media/itsmc-connections-servicenow/snow-system-oauth.png)
      1. Select **Revoke Access** > **Revoke**.

## Install the user app and create the user role

Use the following procedure to install the Service Now user app and create the integration user role for it. You'll use these credentials to make the ServiceNow connection in Azure.

> [!NOTE]
> ITSMC supports only the official user app for Microsoft Log Analytics integration that's downloaded from the ServiceNow store. ITSMC does not support any code ingestion on the ServiceNow side or any application that's not part of the official ServiceNow solution. 

1. Visit the [ServiceNow store](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1) and install **User App for ServiceNow and Microsoft OMS Integration** in your ServiceNow instance.
   
   >[!NOTE]
   >As part of the ongoing transition from Microsoft Operations Management Suite (OMS) to Azure Monitor, OMS is now called Log Analytics.     
2. After installation, go to the left navigation bar of the ServiceNow instance, and then search for and select **Microsoft OMS integrator**.  
3. Select **Installation Checklist**.

   The status is displayed as  **Not complete** because the user role is not yet created.

4. In the text box next to **Create integration user**, enter the name for the user who can connect to ITSMC in Azure.
5. Enter the password for this user, and then select **OK**.  

The newly created user is displayed with the default roles assigned:

- personalize_choices
- import_transformer
- x_mioms_microsoft.user
- itil
- template_editor
- view_changer

After you successfully create the user, the status of **Check Installation Checklist** moves to **Completed** and lists the details of the user role created for the app.

> [!NOTE]
> ITSMC can send incidents to ServiceNow without any other modules installed on your ServiceNow instance. If you're using the EventManagement module in your ServiceNow instance and want to create events or alerts in ServiceNow by using the connector, add the following roles to the integration user:
> 
> - evt_mgmt_integration
> - evt_mgmt_operator  

## Create a connection
Use the following procedure to create a ServiceNow connection.

> [!NOTE]
> The alerts that are sent from Azure Monitor can create one of the following elements in ServiceNow: events, incidents, or alerts. 

1. In Azure portal, go to **All Resources** and look for **ServiceDesk(YourWorkspaceName)**.

2. Under **Workspace Data Sources**, select **ITSM Connections**.

   ![Screenshot that shows selection of a data source.](media/itsmc-overview/add-new-itsm-connection.png)

3. At the top of the right pane, select **Add**.

4. Provide the information as described in the following table, and then select **OK**.

   | **Field** | **Description** |
   | --- | --- |
   | **Connection Name**   | Enter a name for the ServiceNow instance that you want to connect with ITSMC. You use this name later in Log Analytics when you configure ITSM work items and view detailed analytics. |
   | **Partner Type**   | Select **ServiceNow**. |
   | **Server Url**   | Enter the URL of the ServiceNow instance that you want to connect to ITSMC. The URL should point to a supported SaaS version with the suffix *.servicenow.com* (for example `https://XXXXX.service-now.com/`).|
   | **Username**   | Enter the integration username that you created in the ServiceNow app to support the connection to ITSMC.|
   | **Password**   | Enter the password associated with this username. **Note**: The username and password are used for generating authentication tokens only. They're not stored anywhere within the ITSMC service.  |
   | **Client Id**   | Enter the client ID that you want to use for OAuth2 authentication, which you generated earlier. For more information on generating a client ID and a secret, see [Set up OAuth](https://old.wiki/index.php/OAuth_Setup). |
   | **Client Secret**   | Enter the client secret generated for this ID.   |
   | **Data Sync Scope (in Days)** | Enter the number of past days that you want the data from. The limit is 120 days. |
   | **Work Items To Sync**   | Select the ServiceNow work items that you want to sync to Azure Log Analytics, through ITSMC. The selected values are imported into Log Analytics. Options are incidents and change requests.|
   | **Create New Configuration Item in ITSM Product** | Select this option if you want to create the configuration items in the ITSM product. When it's selected, ITSMC creates configuration items (if none exist) in the supported ITSM system. It's disabled by default. |

![Screenshot of boxes and options for adding a ServiceNow connection.](media/itsmc-connections-servicenow/itsm-connection-servicenow-connection-latest.png)

When you're successfully connected and synced:

- Selected work items from the ServiceNow instance are imported into Log Analytics. You can view the summary of these work items on the **IT Service Management Connector** tile.

- You can create incidents from Log Analytics alerts or log records, or from Azure alerts in this ServiceNow instance.

> [!NOTE]
> ServiceNow has a rate limit for requests per hour. To configure the limit, define **Inbound REST API rate limiting** in the ServiceNow instance.

## Next steps

* [ITSM Connector overview](itsmc-overview.md)
* [Create ITSM work items from Azure alerts](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [Troubleshooting problems in the ITSM Connector](./itsmc-resync-servicenow.md)
