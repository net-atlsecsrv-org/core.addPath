---
title: Manage read replicas - Azure PowerShell - Azure Database for PostgreSQL
description: Learn how to set up and manage read replicas in Azure Database for PostgreSQL using PowerShell.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: how-to
ms.date: 06/08/2020 
ms.custom: devx-track-azurepowershell
---

# How to create and manage read replicas in Azure Database for PostgreSQL using PowerShell

In this article, you learn how to create and manage read replicas in the Azure Database for PostgreSQL
service using PowerShell. To learn more about read replicas, see the
[overview](concepts-read-replicas.md).

## Azure PowerShell

You can create and manage read replicas using PowerShell.

## Prerequisites

To complete this how-to guide, you need:

- The [Az PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps) installed
  locally or [Azure Cloud Shell](https://shell.azure.com/) in the browser
- An [Azure Database for PostgreSQL server](quickstart-create-postgresql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> While the Az.PostgreSql PowerShell module is in preview, you must install it separately from the Az
> PowerShell module using the following command: `Install-Module -Name Az.PostgreSql -AllowPrerelease`.
> Once the Az.PostgreSql PowerShell module is generally available, it becomes part of future Az
> PowerShell module releases and available natively from within Azure Cloud Shell.

If you choose to use PowerShell locally, connect to your Azure account using the
[Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) cmdlet.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> The read replica feature is only available for Azure Database for PostgreSQL servers in the General
> Purpose or Memory Optimized pricing tiers. Ensure the master server is in one of these pricing
> tiers.

### Create a read replica

A read replica server can be created using the following command:

```azurepowershell-interactive
Get-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  New-AzPostgreSqlServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

The `New-AzPostgreSqlServerReplica` command requires the following parameters:

| Setting | Example value | Description  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  The resource group where the replica server is created.  |
| Name | mydemoreplicaserver | The name of the new replica server that is created. |

To create a cross region read replica, use the **Location** parameter. The following example creates
a replica in the **West US** region.

```azurepowershell-interactive
Get-AzPostgreSqlServer -Name mrdemoserver -ResourceGroupName myresourcegroup |
  New-AzMariaDServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup -Location westus
```

To learn more about which regions you can create a replica in, visit the
[read replica concepts article](concepts-read-replicas.md).

By default, read replicas are created with the same server configuration as the master unless the
**Sku** parameter is specified.

> [!NOTE]
> It is recommended that the replica server's configuration should be kept at equal or greater
> values than the master to ensure the replica is able to keep up with the master.

### List replicas for a master server

To view all replicas for a given master server, run the following command:

```azurepowershell-interactive
Get-AzMariaDReplica -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

The `Get-AzMariaDReplica` command requires the following parameters:

| Setting | Example value | Description  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  The resource group where the replica server will be created to.  |
| ServerName | mydemoserver | The name or ID of the master server. |

### Delete a replica server

Deleting a read replica server can be done by running the `Remove-AzPostgreSqlServer` cmdlet.

```azurepowershell-interactive
Remove-AzPostgreSqlServer -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

### Delete a master server

> [!IMPORTANT]
> Deleting a master server stops replication to all replica servers and deletes the master server
> itself. Replica servers become standalone servers that now support both read and writes.

To delete a master server, you can run the `Remove-AzPostgreSqlServer` cmdlet.

```azurepowershell-interactive
Remove-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## Next steps

> [!div class="nextstepaction"]
> [Restart Azure Database for PostgreSQL server using PowerShell](howto-restart-server-powershell.md)
