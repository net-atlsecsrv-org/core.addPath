---
title: Connect to a SQL Server virtual machine (Resource Manager) | Microsoft Docs
description: Learn how to connect to your SQL Server virtual machine on Azure. This topic uses the classic deployment model. The scenarios differ depending on the networking configuration and the location of the client.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager

ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.subservice: management

ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/12/2017
ms.author: mathoma
ms.reviewer: jroth
---
# Connect to a SQL Server virtual machine on Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

## Overview

This topic describes how to connect to your SQL on Azure virtual machine (VM). It covers some [general connectivity scenarios](#connection-scenarios) and then provides [steps in the portal for changing connectivity settings](#change). If you need to troubleshoot or configure connectivity outside of the portal, see the [manual configuration](#manual) at the end of this topic. 

If you would rather have a full walkthrough of both provisioning and connectivity, see [Provision a SQL Server virtual machine on Azure](create-sql-vm-portal.md).

## Connection scenarios

The way a client connects to a SQL Server VM differs depending on the location of the client and the networking configuration.

If you provision a SQL Server VM in the Azure portal, you have the option of specifying the type of **SQL connectivity**.

![Public SQL connectivity option during provisioning](./media/ways-to-connect-to-sql/sql-vm-portal-connectivity.png)

Your options for connectivity include:

| Option | Description |
|---|---|
| **Public** | Connect to SQL Server over the internet. |
| **Private** | Connect to SQL Server in the same virtual network. |
| **Local** | Connect to SQL Server locally on the same virtual machine. | 

The following sections explain the **Public** and **Private** options in more detail.

## Connect to SQL Server over the internet

If you want to connect to your SQL Server database engine from the internet, select **Public** for the **SQL connectivity** type in the portal during provisioning. The portal automatically does the following steps:

* Enables the TCP/IP protocol for SQL Server.
* Configures a firewall rule to open the SQL Server TCP port (default 1433).
* Enables SQL Server authentication, required for public access.
* Configures the network security group on the VM to all TCP traffic on the SQL Server port.

> [!IMPORTANT]
> The virtual machine images for the SQL Server Developer and Express editions do not automatically enable the TCP/IP protocol. For Developer and Express editions, you must use SQL Server Configuration Manager to [manually enable the TCP/IP protocol](#manualtcp) after creating the VM.

Any client with internet access can connect to the SQL Server instance by specifying either the public IP address of the virtual machine or any DNS label assigned to that IP address. If the SQL Server port is 1433, you do not need to specify it in the connection string. The following connection string connects to a SQL VM with a DNS label of `sqlvmlabel.eastus.cloudapp.azure.com` using SQL authentication (you could also use the public IP address).

```
Server=sqlvmlabel.eastus.cloudapp.azure.com;Integrated Security=false;User ID=<login_name>;Password=<your_password>
```

Although this string enables connectivity for clients over the internet, this does not imply that anyone can connect to your SQL Server instance. Outside clients have to use the correct username and password. However, for additional security, you can avoid the well-known port 1433. For example, if you were to configure SQL Server to listen on port 1500 and establish proper firewall and network security group rules, you could connect by appending the port number to the server name. The following example alters the previous one by adding a custom port number, **1500**, to the server name:

```
Server=sqlvmlabel.eastus.cloudapp.azure.com,1500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"
```

> [!NOTE]
> When you query SQL Server on VM over the internet, all outgoing data from the Azure datacenter is subject to normal [pricing on outbound data transfers](https://azure.microsoft.com/pricing/details/data-transfers/).

## Connect to SQL Server within a virtual network

When you choose **Private** for the **SQL connectivity** type in the portal, Azure configures most of the settings identical to **Public**. The one difference is that there is no network security group rule to allow outside traffic on the SQL Server port (default 1433).

> [!IMPORTANT]
> The virtual machine images for the SQL Server Developer and Express editions do not automatically enable the TCP/IP protocol. For Developer and Express editions, you must use SQL Server Configuration Manager to [manually enable the TCP/IP protocol](#manualtcp) after creating the VM.

Private connectivity is often used in conjunction with a [virtual network](../../../virtual-network/virtual-networks-overview.md), which enables several scenarios. You can connect VMs in the same virtual network, even if those VMs exist in different resource groups. And with a [site-to-site VPN](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), you can create a hybrid architecture that connects VMs with on-premises networks and machines.

Virtual networks also enable you to join your Azure VMs to a domain. This is the only way to use Windows authentication to SQL Server. The other connection scenarios require SQL authentication with user names and passwords.

Assuming that you have configured DNS in your virtual network, you can connect to your SQL Server instance by specifying the SQL Server VM computer name in the connection string. The following example also assumes that Windows authentication has been configured and that the user has been granted access to the SQL Server instance.

```
Server=mysqlvm;Integrated Security=true
```

## <a id="change"></a> Change SQL connectivity settings

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

You can change the connectivity settings for your SQL Server virtual machine in the Azure portal.

1. In the Azure portal, select **SQL virtual machines**.

2. Select your SQL Server VM.

3. Under **Settings**, select **Security**.

4. Change the **SQL connectivity level** to your required setting. You can optionally use this area to change the SQL Server port or the SQL authentication settings.

   ![Change SQL connectivity](./media/ways-to-connect-to-sql/sql-vm-portal-connectivity-change.png)

5. Wait several minutes for the update to complete.

   ![SQL VM update notification](./media/ways-to-connect-to-sql/sql-vm-updating-notification.png)

## <a id="manualtcp"></a> Enable TCP/IP for Developer and Express editions

When changing SQL Server connectivity settings, Azure does not automatically enable the TCP/IP protocol for SQL Server Developer and Express editions. The steps below explain how to manually enable TCP/IP so that you can connect remotely by IP address.

First, connect to the SQL Server virtual machine with remote desktop.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Next, enable the TCP/IP protocol with **SQL Server Configuration Manager**.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-connection-tcp-protocol.md)]

## Connect with SSMS

The following steps show how to create an optional DNS label for your Azure VM and then connect with SQL Server Management Studio (SSMS).

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## <a id="manual"></a> Manual configuration and troubleshooting

Although the portal provides options to automatically configure connectivity, it is useful to know how to manually configure connectivity. Understanding the requirements can also aid troubleshooting.

The following table lists the requirements to connect to SQL Server on Azure VM.

| Requirement | Description |
|---|---|
| [Enable SQL Server authentication mode](/sql/database-engine/configure-windows/change-server-authentication-mode#use-ssms) | SQL Server authentication is needed to connect to the VM remotely unless you have configured Active Directory on a virtual network. |
| [Create a SQL login](/sql/relational-databases/security/authentication-access/create-a-login) | If you are using SQL authentication, you need a SQL login with a user name and password that also has permissions to your target database. |
| [Enable TCP/IP protocol](#manualtcp) | SQL Server must allow connections over TCP. |
| [Enable firewall rule for the SQL Server port](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) | The firewall on the VM must allow inbound traffic on the SQL Server port (default 1433). |
| [Create a network security group rule for TCP 1433](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) | You must allow the VM to receive traffic on the SQL Server port (default 1433) if you want to connect over the internet. Local and virtual-network-only connections do not require this. This is the only step required in the Azure portal. |

> [!TIP]
> The steps in the preceding table are done for you when you configure connectivity in the portal. Use these steps only to confirm your configuration or to set up connectivity manually for SQL Server.

## Next steps

To see provisioning instructions along with these connectivity steps, see [Provisioning a SQL Server virtual machine on Azure](create-sql-vm-portal.md).

For other topics related to running SQL Server on Azure VMs, see [SQL Server on Azure virtual machines](sql-server-on-azure-vm-iaas-what-is-overview.md).