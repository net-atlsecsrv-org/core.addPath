---
title: Managing Azure Automation data
description: This article contains multiple topics for managing an Azure Automation environment.  Currently includes Data Retention and Backing up Azure Automation Disaster Recovery in Azure Automation.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
---
# Managing Azure Automation data
This article contains multiple topics for managing an Azure Automation environment.

## Data retention
When you delete a resource in Azure Automation, it is retained for 90 days for auditing purposes before being removed permanently.  You can’t see or use the resource during this time.  This policy also applies to resources that belong to an automation account that is deleted.

Azure Automation automatically deletes and permanently removes jobs older than 90 days.

The following table summarizes the retention policy for different resources.

| Data | Policy |
|:--- |:--- |
| Accounts |Permanently removed 90 days after the account is deleted by a user. |
| Assets |Permanently removed 90 days after the asset is deleted by a user, or 90 days after the account that holds the asset is deleted by a user. |
| Modules |Permanently removed 90 days after the module is deleted by a user, or 90 days after the account that holds the module is deleted by a user. |
| Runbooks |Permanently removed 90 days after the resource is deleted by a user, or 90 days after the account that holds the resource is deleted by a user. |
| Jobs |Deleted and permanently removed 90 days after last being modified. This could be after the job completes, is stopped, or is suspended. |
| Node Configurations/MOF Files |Old node configuration is permanently removed 90 days after a new node configuration is generated. |
| DSC Nodes |Permanently removed 90 days after the node is unregistered from Automation Account using Azure portal or the [Unregister-AzureRMAutomationDscNode](https://docs.microsoft.com/powershell/module/azurerm.automation/unregister-azurermautomationdscnode) cmdlet in Windows PowerShell. Nodes are also permanently removed 90 days after the account that holds the node is deleted by a user. |
| Node Reports |Permanently removed 90 days after a new report is generated for that node |

The retention policy applies to all users and currently cannot be customized.

However, if you need to retain data for a longer period of time, you can forward runbook job logs to Azure Monitor logs.  For further information, review [forward Azure Automation job data to Azure Monitor logs](automation-manage-send-joblogs-log-analytics.md).   

## Backing up Azure Automation
When you delete an automation account in Microsoft Azure, all objects in the account are deleted including runbooks, modules, configurations, settings, jobs, and assets. The objects cannot be recovered after the account is deleted.  You can use the following information to backup the contents of your automation account before deleting it. 

### Runbooks
You can export your runbooks to script files using either the Azure portal or the [Get-AzureAutomationRunbookDefinition](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationrunbookdefinition) cmdlet in Windows PowerShell.  These script files can be imported into another automation account as discussed in [Creating or Importing a Runbook](/previous-versions/azure/dn643637(v=azure.100)).

### Integration modules
You cannot export integration modules from Azure Automation.  You must ensure that they are available outside of the automation account.

### Assets
You cannot export [assets](/previous-versions/azure/dn939988(v=azure.100)) from Azure Automation.  Using the Azure portal, you must note the details of variables, credentials, certificates, connections, and schedules.  You must then manually create any assets that are used by runbooks that you import into another automation.

You can use [Azure cmdlets](https://docs.microsoft.com/powershell/module/azurerm.automation#automation) to retrieve details of unencrypted assets and either save them for future reference or create equivalent assets in another automation account.

You cannot retrieve the value for encrypted variables or the password field of credentials using cmdlets.  If you don't know these values, then you can retrieve them from a runbook using the [Get-AutomationVariable](/previous-versions/azure/dn940012(v=azure.100)) and [Get-AutomationPSCredential](/previous-versions/azure/dn940015(v=azure.100)) activities.

You cannot export certificates from Azure Automation.  You must ensure that any certificates are available outside of Azure.

### DSC configurations
You can export your configurations to script files using either the Azure portal or the 
[Export-AzureRmAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/azurerm.automation/export-azurermautomationdscconfiguration) cmdlet in Windows PowerShell. These configurations can be imported and used in another automation account.

## Geo-replication in Azure Automation
Geo-replication, standard in Azure Automation accounts, backs up account data to a different geographical region for redundancy. You can choose a primary region when setting up your account, and then a secondary region is assigned to it automatically. The secondary data, copied from the primary region, is continuously updated in case of data loss.  

The following table shows the available primary and secondary region pairings.

| Primary | Secondary |
| --- | --- |
| South Central US |North Central US |
| US East 2 |Central US |
| West Europe |North Europe |
| South East Asia |East Asia |
| Japan East |Japan West |

In the unlikely event that a primary region data is lost, Microsoft attempts to recover it. If the primary data cannot be recovered, then geo-failover is performed and the affected customers will be notified about this through their subscription.


