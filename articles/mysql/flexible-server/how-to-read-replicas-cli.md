---
title: Manage read replicas in Azure Database for MySQL Flexible Server using Azure CLI.
description: Learn how to set up and manage read replicas in Azure Database for MySQL flexible server using the Azure CLI.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 10/23/2020 
ms.custom: devx-track-azurecli
---

# How to create and manage read replicas in Azure Database for MySQL flexible server using the Azure CLI

> [!IMPORTANT]
> Read replicas in Azure Database for MySQL - Flexible Server is in preview.

In this article, you will learn how to create and manage read replicas in the Azure Database for MySQL flexible server using the Azure CLI. To learn more about read replicas, see the [overview](concepts-read-replicas.md).

> [!Note]
> Replica is not supported on high availability enabled server. 

## Azure CLI
You can create and manage read replicas using the Azure CLI.

### Prerequisites

- [Install Azure CLI 2.0](/cli/azure/install-azure-cli)
- An [Azure Database for MySQL Flexible Server](quickstart-create-server-cli.md) that will be used as the source server.

### Create a read replica

> [!IMPORTANT]
> When you create a replica for a source that has no existing replicas, the source will first restart to prepare itself for replication. Take this into consideration and perform these operations during an off-peak period.

A read replica server can be created using the following command:

```azurecli-interactive
az mysql flexible-server replica create --replica-name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
``` 

> [!NOTE]
> Read replicas are created with the same server configuration as the source. The replica server configuration can be changed after it has been created. The replica server is always created in the same resource group, same location and same subscription as the source server. If you want to create a replica server to a different resource group or different subscription, you can [move the replica server](../../azure-resource-manager/management/move-resource-group-and-subscription.md) after creation. It is recommended that the replica server's configuration should be kept at equal or greater values than the source to ensure the replica is able to keep up with the source.


### List replicas for a source server

To view all replicas for a given source server, run the following command: 

```azurecli-interactive
az mysql flexible-server replica list --server-name mydemoserver --resource-group myresourcegroup
```

### Stop replication to a replica server

> [!IMPORTANT]
> Stopping replication to a server is irreversible. Once replication has stopped between a source and replica, it cannot be undone. The replica server then becomes a standalone server and now supports both read and writes. This server cannot be made into a replica again.

Replication to a read replica server can be stopped using the following command:

```azurecli-interactive
az mysql flexible-server replica stop-replication --replica-name mydemoreplicaserver --resource-group myresourcegroup
```

### Delete a replica server

Deleting a read replica server can be done by running the **[az mysql server delete](/cli/azure/mysql/server)** command.

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

### Delete a source server

> [!IMPORTANT]
> Deleting a source server stops replication to all replica servers and deletes the source server itself. Replica servers become standalone servers that now support both read and writes.

To delete a source server, you can run the **[az mysql flexible-server delete](/cli/azure/mysql/flexible-server)** command.

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## Next steps

- Learn more about [read replicas](concepts-read-replicas.md)