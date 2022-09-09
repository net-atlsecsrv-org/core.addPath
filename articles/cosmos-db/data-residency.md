---
title: How to meet data residency requirements in Azure Cosmos DB
description: learn how to meet data residency requirements in Azure Cosmos DB for your data and backups to remain in a single region.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/05/2021
ms.author: govindk
ms.reviewer: sngun

---

# How to meet data residency requirements in Azure Cosmos DB

In Azure Cosmos DB, you can configure your data and backups to remain in a single region to meet the[ residency requirements.](https://azure.microsoft.com/en-us/global-infrastructure/data-residency/)

## Residency requirements for data

In Azure Cosmos DB, you must explicitly configure the cross-region data replication. Learn how to configure geo-replication using [Azure portal](how-to-manage-database-account.md#addremove-regions-from-your-database-account), [Azure CLI](scripts/cli/common/regions.md). To meet data residency requirements, you can create an Azure policy that allows certain regions to prevent data replication to unwanted regions.

## Residency requirements for backups

**Continuous mode Backups**: These backups are resident by default as they are stored in either locally redundant or zone redundant storage. To learn more, see the [continuous backup](continuous-backup-restore-portal.md) article.

**Periodic mode Backups**: By default, periodic mode account backups will be stored in geo-redundant storage. For periodic backup modes, you can configure data redundancy at the account level. There are three redundancy options for the backup storage. They are local redundancy, zone redundancy, or geo redundancy. To learn more, see how to [configure backup redundancy](configure-periodic-backup-restore.md#configure-backup-interval-retention) using portal.

## Use Azure Policy to enforce the residency requirements

If you have data residency requirements that require you to keep all your data in a single Azure region, you can enforce zone-redundant or locally redundant backups for your account by using an Azure Policy.  You can also enforce a policy that the Azure Cosmos DB accounts are not geo-replicated to other regions.

Azure Policy is a service that you can use to create, assign, and manage policies that apply rules to Azure resources. Azure Policy helps you to keep these resources compliant with your corporate standards and service level agreements. For more information, see how to use [Azure Policy](policy.md) to implement governance and controls for Azure Cosmos DB resources

## Next steps

* Configure and manage periodic backup using [Azure portal](configure-periodic-backup-restore.md)
* Configure and manage continuous backup using [Azure portal](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md), [CLI](continuous-backup-restore-command-line.md), or [Azure Resource Manager](continuous-backup-restore-template.md).
