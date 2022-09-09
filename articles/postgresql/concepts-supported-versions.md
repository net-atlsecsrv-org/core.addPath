---
title: Supported versions in Azure Database for PostgreSQL - Single Server
description: Describes the supported versions in Azure Database for PostgreSQL - Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/06/2019
ms.custom: fasttrack-edit
---
# Supported PostgreSQL database versions
Microsoft aims to support n-2 versions of the PostgreSQL engine in Azure Database for PostgreSQL - Single Server. The versions would be the current major version on Azure (n) and the two prior major versions (-2).

Azure Database for PostgreSQL currently supports the following major versions:

## PostgreSQL version 11
The current minor version is 11.4. Refer to the [PostgreSQL documentation](https://www.postgresql.org/docs/11/static/release-11-4.html) to learn more about improvements and fixes in this minor version.

## PostgreSQL version 10
The current minor version is 10.9. Refer to the [PostgreSQL documentation](https://www.postgresql.org/docs/10/static/release-10-9.html) to learn more about improvements and fixes in this minor version.

## PostgreSQL version 9.6
The current minor version is 9.6.14. Refer to the [PostgreSQL documentation](https://www.postgresql.org/docs/9.6/static/release-9-6-14.html) to learn more about improvements and fixes in this minor version.

## PostgreSQL version 9.5
The current minor version is 9.5.18. Refer to the [PostgreSQL documentation](https://www.postgresql.org/docs/9.5/static/release-9-5-18.html) to learn about improvements and fixes in this minor version.

## Managing upgrades
Azure Database for PostgreSQL automatically manages minor version upgrades. 

Automatic major version upgrade is not supported. For example, there is not an automatic upgrade from PostgreSQL 9.5 to PostgreSQL 9.6. To upgrade to the next major version, create a [database dump and restore](./howto-migrate-using-dump-and-restore.md) to a server that was created with the new engine version.

### Version syntax
Before PostgreSQL version 10, the [PostgreSQL versioning policy](https://www.postgresql.org/support/versioning/) considered a _major version_ upgrade to be an increase in the first _or_ second number. For example, 9.5 to 9.6 was considered a _major_ version upgrade. As of version 10, only a change in the first number is considered a major version upgrade. For example, 10.0 to 10.1 is a _minor_ version upgrade. Version 10 to 11 is a _major_ version upgrade.

## Next steps
For information on supported PostgreSQL extensions, see [the extensions document](concepts-extensions.md).
