---
title: Deny Public Network Access - Azure portal - Azure Database for PostgreSQL - Single server
description: Learn how to configure Deny Public Network Access using Azure portal for your Azure Database for PostgreSQL Single server 
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: how-to
ms.date: 03/10/2020
---

# Deny Public Network Access in Azure Database for PostgreSQL Single server using Azure portal

This article describes how you can configure an Azure Database for PostgreSQL Single server to deny all public configurations and allow only connections through private endpoints to further enhance the network security.

## Prerequisites

To complete this how-to guide, you need:

* An [Azure Database for PostgreSQL Single server](quickstart-create-server-database-portal.md)

## Set Deny Public Network Access

Follow these steps to set PostgreSQL Single server Deny Public Network Access:

1. In the [Azure portal](https://portal.azure.com/), select your existing Azure Database for PostgreSQL Single server.

1. On the PostgreSQL Single server page, under **Settings**, click **Connection security** to open the connection security configuration page.

1. In **Deny Public Network Access**, select **Yes** to enable deny public access for your PostgreSQL Single server.

    :::image type="content" source="./media/howto-deny-public-network-access/deny-public-network-access.PNG" alt-text="Azure Database for PostgreSQL Single server Deny network access":::

1. Click **Save** to save the changes.

1. A notification will confirm that connection security setting was successfully enabled.

    :::image type="content" source="./media/howto-deny-public-network-access/deny-public-network-access-success.png" alt-text="Azure Database for PostgreSQL Single server Deny network access success":::

## Next steps

Learn about [how to create alerts on metrics](howto-alert-on-metric.md).
