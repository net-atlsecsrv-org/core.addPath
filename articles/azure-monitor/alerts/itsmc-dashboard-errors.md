---
title: Connector status errors in the ITSMC dashboard
description: Learn about common errors that exist in the IT Service Management Connector dashboard. 
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/18/2021

---

# Connector status errors in the ITSMC dashboard

The IT Service Management Connector (ITSMC) dashboard presents errors that can help you to fix problems in your connector.

The following sections describe common errors that appear in the connector status section of the dashboard and how you can resolve them.

## Unexpected response

**Error**: "Unexpected response from ServiceNow along with success status code. Response: { "import_set": "{import_set_id}", "staging_table": "x_mioms_microsoft_oms_incident", "result": [ { "transform_map": "OMS Incident", "table": "incident", "status": "error", "error_message": "{Target record not found|Invalid table|Invalid staging table" }"

**Cause**: ServiceNow returns this error when:

* A custom script deployed in a ServiceNow instance causes incidents to be ignored.
* "OMS Integrator app" code was modified on the ServiceNow side (for example, through the `onBefore` script).

**Resolution**: Disable all custom scripts or code modifications.

## Exception update failure

**Error**: "{"error":{"message":"Operation Failed","detail":"ACL Exception Update Failed due to security constraints"}"

**Cause**: ServiceNow permissions are misconfigured.

**Resolution**: Check that all the roles are properly assigned as [specified](itsmc-connections-servicenow.md#install-the-user-app-and-create-the-user-role).

## Problem sending a request

**Error**: "An error occurred while sending the request."

**Cause**: A ServiceNow instance is unavailable.

**Resolution**: Check your instance in ServiceNow. It might be deleted or unavailable.

## ServiceNow rate problem

**Error**: "ServiceDeskHttpBadRequestException: StatusCode=429"

**Cause**: ServiceNow rate limits are too high or too low.

**Resolution**: Increase or cancel the rate limits in the ServiceNow instance, as explained in the [ServiceNow documentation](https://docs.servicenow.com/bundle/london-application-development/page/integrate/inbound-rest/task/investigate-rate-limit-violations.html).

## Invalid refresh token

**Error**: 
  * "AccessToken and RefreshToken invalid. User needs to authenticate again."
  * "Could not sync templates configuration for Event,Alert,Incident. See Exception Message for more details."

**Cause**: A refresh token is expired.

**Resolution**: Sync ITSMC to generate a new refresh token, as explained in [How to manually fix sync problems](./itsmc-resync-servicenow.md).

## Missing connector

**Error**: "Could not create/update work item for alert {alertName}. ITSM Connector {connectionIdentifier} does not exist or was deleted."

**Cause**: ITSMC was deleted.

**Resolution**: ITSMC was deleted, but defined IT Service Management (ITSM) action groups are still associated with it. There are three options to solve this problem:

* Find and disable or delete such action groups.
* [Reconfigure the action groups](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts) to use an existing ITSMC instance.
* [Create a new ITSMC instance](./itsmc-definition.md#create-an-itsm-connection) and [reconfigure the action groups to use it](itsmc-definition.md#create-itsm-work-items-from-azure-alerts).

## Lack of connection details

**Error**:"Something went wrong. Could not get connection details." This error appears when you define an ITSM action group.

**Cause**: Such an error appears in either of these situations:

* A newly created ITSM Connector instance has yet to finish the initial sync.
* The connector was not defined correctly.

**Resolution**: 

* When a new ITSMC instance is created, it starts syncing information from the ITSM system, such as work item templates and work items. [Sync ITSMC to generate a new refresh token](./itsmc-resync-servicenow.md).
* [Review your connection details in ITSMC](./itsmc-connections-servicenow.md#create-a-connection) and check that ITSMC can successfully [sync](./itsmc-resync-servicenow.md).


## IP restrictions
**Error**: "Failed to add ITSM Connection named "XXX" due to Bad Request. Error: Bad request. Invalid parameters provided for connection. Http Exception: Status Code Forbidden."

**Cause**: The IP address of ITSM application is not allow ITSM connections from partners ITSM tools.

**Resolution**: In order to list the ITSM IP addresses in order to allow ITSM connections from partners ITSM tools, we recommend the to list the whole public IP range of Azure region where their LogAnalytics workspace belongs. [details here](https://www.microsoft.com/download/details.aspx?id=56519) For regions EUS/WEU/EUS2/WUS2/US South Central the customer can list ActionGroup network tag only.
