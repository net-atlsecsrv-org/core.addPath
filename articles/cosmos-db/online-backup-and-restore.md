---
title: Online backup and on-demand data restore in Azure Cosmos DB.
description: This article describes how automatic backup, on-demand data restore works. It also explains the difference between continuous and periodic backup modes. 
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/13/2020
ms.author: govindk
ms.reviewer: sngun

---

# Online backup and on-demand data restore in Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB automatically takes backups of your data at regular intervals. The automatic backups are taken without affecting the performance or availability of the database operations. All the backups are stored separately in a storage service. The automatic backups are helpful in scenarios when you accidentally delete or update your Azure Cosmos account, database, or container and later require the data recovery. There are two backup modes:

* **Periodic backup mode** - This mode is the default backup mode for all existing accounts. In this mode, backup is taken at a periodic interval and the data is restored by creating a request with the support team. In this mode, you configure a backup interval and retention for your account. The maximum retention period extends to a month. The minimum backup interval can be one hour.  To learn more, see the [Periodic backup mode](configure-periodic-backup-restore.md) article.

* **Continuous backup mode** (currently in public preview) – You choose this mode while creating the Azure Cosmos DB account. This mode allows you to do restore to any point of time within the last 30 days. To learn more, see the [Introduction to Continuous backup mode](continuous-backup-restore-introduction.md), configure continuous backup with [Azure portal](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md), [CLI](continuous-backup-restore-command-line.md), and [Resource Manager](continuous-backup-restore-template.md) articles.

  > [!NOTE]
  > If you configure a new account with continuous backup, you can do self-service restore via Azure portal, PowerShell, or CLI. If your account is configured in continuous mode, you can’t switch it back to periodic mode. Currently existing accounts with periodic backup mode can’t be changed into continuous mode.  

## Next steps

Next you can learn about how to configure and manage periodic and continuous backup modes for your account:

* [Configure and manage periodic backup](configure-periodic-backup-restore.md) policy.
* What is [continuous backup](continuous-backup-restore-introduction.md) mode?
* Configure and manage continuous backup using [Azure portal](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md), [CLI](continuous-backup-restore-command-line.md), or [Azure Resource Manager](continuous-backup-restore-template.md).
* [Manage permissions](continuous-backup-restore-permissions.md) required to restore data with continuous backup mode.
