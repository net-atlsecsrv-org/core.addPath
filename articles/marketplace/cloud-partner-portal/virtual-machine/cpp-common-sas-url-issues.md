---
title: Common SAS URL issues and fixes for the Azure Marketplace 
description: List common problems around using shared access signature URIs and possible solutions.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/27/2018
ms.author: dsindona
---

# Common SAS URL issues and fixes

> [!IMPORTANT]
> Starting April 13, 2020, we'll begin moving the management of your Azure Virtual Machine offers to Partner Center. After the migration, you'll create and manage your offers in Partner Center. Follow the instructions in [Common SAS URL issues and fixes](https://aka.ms/AzureSAS_URL_FAQ) to manage your migrated offers.

The following table lists some of the common issues encountered when working with shared access signatures (which are used to identify and share the uploaded VHDs for your solution), along with suggested resolutions.

| **Issue** | **Failure Message** | **Fix** | 
| --------- | ------------------- | ------- | 
| &emsp;  *Failure in copying images* |  |  |
| "?" is not found in SAS URL | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Update the SAS URL using recommended tools. |
| "st" and "se" parameters not in SAS URL | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Update the SAS URL with proper **Start Date** and **End Date** values in it. | 
| "sp=rl" not in SAS URL | `Failure: Copying Images. Not able to download blob using provided SAS Uri` | Update the SAS URL with permissions set as `Read` and `List`. | 
| SAS URL has white spaces in VHD name | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Update the SAS URL to remove white spaces. |
| SAS URL Authorization error | `Failure: Copying Images. Not able to download blob due to authorization error` | Review and correct the SAS URI format. Regenerate if necessary. |
| SAS URL "st" and "se" parameters do not have full date-time specification | `Failure: Copying Images. Not able to download blob due to incorrect SAS URL` | SAS URL **Start Date** and **End Date** parameters (`st` and `se` substrings) are required to have full datetime format, such as `11-02-2017T00:00:00Z`. Shortened versions are not valid. (Some commands in Azure CLI may generate shortened values by default.) | 
|  |  |  |

For more information, see [Using shared access signatures (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).
