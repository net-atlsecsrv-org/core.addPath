---
title: Managing Azure Automation data
description: This article contains multiple topics for managing an Azure Automation environment.  Currently includes Data Retention and Backing up Azure Automation Disaster Recovery in Azure Automation.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/23/2020
ms.topic: conceptual
---
# Managing Azure Automation data

This article contains multiple topics for managing data in an Azure Automation environment.

>[!NOTE]
>This article has been updated to use the new Azure PowerShell Az module. You can still use the AzureRM module, which will continue to receive bug fixes until at least December 2020. To learn more about the new Az module and AzureRM compatibility, see [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). For Az module installation instructions on your Hybrid Runbook Worker, see [Install the Azure PowerShell Module](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). For your Automation account, you can update your modules to the latest version using [How to update Azure PowerShell modules in Azure Automation](automation-update-azure-modules.md).

## Data retention

When you delete a resource in Azure Automation, it's retained for a number of days for auditing purposes before permanent removal. You can't see or use the resource during this time. This policy also applies to resources that belong to a deleted Automation account.

The following table summarizes the retention policy for different resources.

| Data | Policy |
|:--- |:--- |
| Accounts |An account is permanently removed 30 days after a user deletes it. |
| Assets |An asset is permanently removed 30 days after a user deletes it, or 30 days after a user deletes an account that holds the asset. |
| DSC Nodes |A DSC node is permanently removed 30 days after being unregistered from an Automation account using Azure portal or the [Unregister-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-3.7.0) cmdlet in Windows PowerShell. A node is also permanently removed 30 days after a user deletes the account that holds the node. |
| Jobs |A job is deleted and permanently removed 30 days after modification, for example, after the job completes, is stopped, or is suspended. |
| Modules |A module is permanently removed 30 days after a user deletes it, or 30 days after a user deletes the account that holds the module. |
| Node Configurations/MOF Files |An old node configuration is permanently removed 30 days after a new node configuration is generated. |
| Node Reports |A node report is permanently removed 90 days after a new report is generated for that node. |
| Runbooks |A runbook is permanently removed 30 days after a user deletes the resource, or 30 days after a user deletes the account that holds the resource. |

The retention policy applies to all users and currently can't be customized. However, if you need to keep data for a longer period, you can [forward Azure Automation job data to Azure Monitor logs](automation-manage-send-joblogs-log-analytics.md).

## Data backup

When you delete an Automation account in Azure, all objects in the account are deleted. The objects include runbooks, modules, configurations, settings, jobs, and assets. They can't be recovered after the account is deleted. You can use the following information to back up the contents of your Automation account before deleting it.

### Runbooks

You can export your runbooks to script files using either the Azure portal or the [Get-AzureAutomationRunbookDefinition](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationrunbookdefinition) cmdlet in Windows PowerShell. You can import these script files into another Automation account, as discussed in [Manage runbooks in Azure Automation](manage-runbooks.md).

### Integration modules

You can't export integration modules from Azure Automation. You must make them available outside the Automation account.

### Assets

You can't export Azure Automation assets: certificates, connections, credentials, schedules, and variables. Instead, you can use the Azure portal and Azure cmdlets to note the details of these assets. Then use these details to create any assets that are used by runbooks that you import into another Automation account.

You can't retrieve the values for encrypted variables or the password fields of credentials using cmdlets. If you don't know these values, you can retrieve them in a runbook. For retrieving variable values, see [Variable assets in Azure Automation](shared-resources/variables.md). To find out more about retrieving credential values, see [Credential assets in Azure Automation](shared-resources/credentials.md).

 ### DSC configurations

You can export your DSC configurations to script files using either the Azure portal or the 
[Export-AzAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/az.automation/export-azautomationdscconfiguration?view=azps-3.7.0
) cmdlet in Windows PowerShell. You can import and use these configurations in another Automation account.

## Geo-replication in Azure Automation

Geo-replication is standard in Azure Automation accounts. You choose a primary region when setting up your account. The internal Automation geo-replication service assigns a secondary region to the account automatically. The service then continuously backs up account data from the primary region to the secondary region. The full list of primary and secondary regions can be found at [Business continuity and disaster recovery (BCDR): Azure Paired Regions](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

The backup created by the Automation geo-replication service is a complete copy of Automation assets, configurations, and the like. This backup can be used if the primary region goes down and loses data. In the unlikely event that data for a primary region is lost, Microsoft attempts to recover it. If the company can't recover the primary data, it uses automatic failover and informs you of the situation through your Azure subscription. 

The Automation geo-replication service isn't accessible directly to external customers if there is a regional failure. If you want to maintain Automation configuration and runbooks during regional failures:

1. Select a secondary region to pair with the geographical region of your primary Automation account.

2. Create an Automation account in the secondary region.

3. In the primary account, export your runbooks as script files.

4. Import the runbooks to your Automation account in the secondary region.

## Next steps

* To learn more about secure assets in Azure Automation, see [Encrypt secure assets in Azure Automation](automation-secure-asset-encryption.md).

* To find out more about geo-replication, see [Creating and using active geo-replication](https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication).