---
title: Supported versions - Azure Database for PostgreSQL - Flexible Server
description: Describes the supported Postgres major and minor versions in Azure Database for PostgreSQL - Flexible Server.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
---

# Supported PostgreSQL major versions in Azure Database for PostgreSQL - Flexible Server

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server is in preview

Azure Database for PostgreSQL - Flexible Server currently supports the following major versions:

## PostgreSQL version 12

The current minor release is 12.1. Refer to the [PostgreSQL documentation](https://www.postgresql.org/docs/12/static/release-12-1.html) to learn more about improvements and fixes in this minor release.

## PostgreSQL version 11

The current minor release is 11.8. Refer to the [PostgreSQL documentation](https://www.postgresql.org/docs/11/static/release-11-8.html) to learn more about improvements and fixes in this minor release.

## PostgreSQL version 10 and older

We do not support PostgreSQL version 10 and older for Azure Database for PostgreSQL - Flexible Server. Please use the [Single Server](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions) deployment option if you require older versions.

## Managing upgrades

The PostgreSQL project regularly issues minor releases to fix reported bugs. Azure Database for PostgreSQL automatically patches servers with minor releases during the service's monthly deployments.

Automatic major version upgrade is not yet supported. For example, there is currently no automatic upgrade from PostgreSQL 11 to PostgreSQL 12.<!-- To upgrade to the next major version, create a [database dump and restore](howto-migrate-using-dump-and-restore.md) to a server that was created with the new engine version.-->

<!--
## Next steps

For information on supported PostgreSQL extensions, see [the extensions document](concepts-extensions.md).
-->
