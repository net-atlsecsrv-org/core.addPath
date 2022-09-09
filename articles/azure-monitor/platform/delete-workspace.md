---
title: Delete and restore Azure Log Analytics workspace | Microsoft Docs
description: Learn how to delete your Log Analytics workspace if you created one in a personal subscription or restructure your workspace model.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: magoedte
---

# Delete and restore Azure Log Analytics workspace
This article explains the concept of Azure Log Analytics workspace soft-delete and how to recover deleted workspace. 

## Considerations when deleting a workspace
When you delete a Log Analytics workspace, a soft-delete operation is performed to allow the recovery of the workspace including its data and connected agents within 14 days, whether the deletion was accidental or intentional. After the soft-delete period, the workspace and its data are non-recoverable and are queued for permanent deletion within 30 days.

You want to exercise caution when you delete a workspace because there might be important data and configuration that may negatively impact your service operation. Review what agents, solutions, and other Azure services and sources that store their data in Log Analytics, such as:
* Management solutions
* Azure Automation
* Agents running on Windows and Linux virtual machines
* Agents running on Windows and Linux computers in your environment
* System Center Operations Manager

The soft-delete operation deletes the workspace resource and any associated users’ permission is broken. If users are associated with other workspaces, then they can continue using Log Analytics with those other workspaces.

## Soft-delete behavior
The workspace delete operation removes the workspace Resource Manager resource, but its configuration and data are kept for 14 days, while giving the appearance that the workspace is deleted. Any agents and System Center Operations Manager management groups configured to report to the workspace remain in an orphaned state during the soft-delete period. The service further provides a mechanism for recovering the deleted workspace including its data and connected resources, essentially undoing the deletion.

> [!NOTE] 
> Installed solutions and linked services like Automation account are permanently removed from the workspace at deletion time and can’t be recovered. These should be reconfigured after the recovery operation to bring the workspace to its previous functionality. 

You can delete a workspace using [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/remove-azurermoperationalinsightsworkspace?view=azurermps-6.13.0), [API](https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete), or in [Azure portal](https://portal.azure.com).

### Delete workspace in Azure portal
1. To sign in, go to the [Azure portal](https://portal.azure.com). 
2. In the Azure portal, select **All services**. In the list of resources, type **Log Analytics**. As you begin typing, the list filters based on your input. Select **Log Analytics workspaces**.
3. In the list of Log Analytics workspaces, select a workspace and then click **Delete**  from the top of the middle pane.
   ![Delete option from Workspace properties pane](media/delete-workspace/log-analytics-delete-workspace.png)
4. When the confirmation message window appears asking you to confirm deletion of the workspace, click **Yes**.
   ![Confirm deletion of workspace](media/delete-workspace/log-analytics-delete-workspace-confirm.png)

## Recover workspace
If you have Contributor permissions to the subscription and resource group to where the workspace was associated before the soft-delete operation, you can recover it during its soft-delete period including its data, configuration and connected agents. After the soft-delete period, the workspace is non-recoverable and assigned for permanent deletion.

You can recover a workspace by re-creating the workspace using any of the supported create methods: PowerShell, Azure CLI, or from the Azure portal as long as these properties are populated with the deleted workspace’s details including:
1.	Subscription ID
2.	Resource Group name
3.	Workspace name
4.	Region

> [!NOTE]
> Names of deleted workspaces are kept preserved for the soft-delete period and they can't be used when creating a new workspace. The workspace names are *released* and available for use for new workspace creation after the soft-delete period has expired.
