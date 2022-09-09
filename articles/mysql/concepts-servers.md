---
title: Server concepts - Azure Database for MySQL
description: This topic provides considerations and guidelines for working with Azure Database for MySQL servers.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
---
# Server concepts in Azure Database for MySQL

This article provides considerations and guidelines for working with Azure Database for MySQL servers.

## What is an Azure Database for MySQL server?

An Azure Database for MySQL server is a central administrative point for multiple databases. It is the same MySQL server construct that you may be familiar with in the on-premises world. Specifically, the Azure Database for MySQL service is managed, provides performance guarantees, and exposes access and features at server-level.

An Azure Database for MySQL server:

- Is created within an Azure subscription.
- Is the parent resource for databases.
- Provides a namespace for databases.
- Is a container with strong lifetime semantics - delete a server and it deletes the contained databases.
- Collocates resources in a region.
- Provides a connection endpoint for server and database access.
- Provides the scope for management policies that apply to its databases: login, firewall, users, roles, configurations, etc.
- Is available in multiple versions. For more information, see [Supported Azure Database for MySQL database versions](./concepts-supported-versions.md).

Within an Azure Database for MySQL server, you can create one or multiple databases. You can opt to create a single database per server to use all the resources or to create multiple databases to share the resources. The pricing is structured per-server, based on the configuration of pricing tier, vCores, and storage (GB). For more information, see [Pricing tiers](./concepts-pricing-tiers.md).

## How do I connect and authenticate to an Azure Database for MySQL server?

The following elements help ensure safe access to your database.

|     |     |
| :-- | :-- |
| **Authentication and authorization** | Azure Database for MySQL server supports native MySQL authentication. You can connect and authenticate to a server with the server's admin login. |
| **Protocol** | The service supports a message-based protocol used by MySQL. |
| **TCP/IP** | The protocol is supported over TCP/IP and over Unix-domain sockets. |
| **Firewall** | To help protect your data, a firewall rule prevents all access to your database server, until you specify which computers have permission. See [Azure Database for MySQL Server firewall rules](./concepts-firewall-rules.md). |
| **SSL** | The service supports enforcing SSL connections between your applications and your database server.  See [Configure SSL connectivity in your application to securely connect to Azure Database for MySQL](./howto-configure-ssl.md). |

## Stop/Start an Azure Database for MySQL (Preview)

Azure Database for MySQL gives you the ability to **Stop** the server when not in use and **Start** the server when you resume activity. This is essentially done to save costs on the database servers and only pay for the resource when in use. This becomes even more important for dev-test workloads and when you are only using the server for part of the day. When you stop the server, all active connections will be dropped. Later, when you want to bring the server back online, you can either use the [Azure portal](how-to-stop-start-server.md) or [CLI](how-to-stop-start-server.md).

When the server is in the **Stopped** state, the server's compute is not billed. However, storage continues to to be billed as the server's storage remains to ensure that data files are available when the server is started again.

> [!IMPORTANT]
> When you **Stop** the server it remains in that state for the next 7 days in a stretch. If you do not manually **Start** it during this time, the server will automatically be started at the end of 7 days. You can chose to **Stop** it again if you are not using the server.

During the time server is stopped, no management operations can be performed on the server. In order to change any configuration settings on the server, you will need to [start the server](how-to-stop-start-server.md).

### Limitations of Stop/start operation
- Not supported with read replica configurations (both source and replicas).

## How do I manage a server?

You can manage Azure Database for MySQL servers by using the Azure portal or the Azure CLI.

## Next steps

- For an overview of the service, see [Azure Database for MySQL Overview](./overview.md)
- For information about specific resource quotas and limitations based on your **pricing tier**, see [Pricing tiers](./concepts-pricing-tiers.md)
- For information about connecting to the service, see [Connection libraries for Azure Database for MySQL](./concepts-connection-libraries.md).
