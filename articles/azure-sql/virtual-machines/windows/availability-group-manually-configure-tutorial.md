---
title: "Tutorial: Configure a SQL Server Always On availability group"
description: "This tutorial shows how to create a SQL Server Always On availability group on Azure Virtual Machines."
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management

ms.assetid: 08a00342-fee2-4afe-8824-0db1ed4b8fca
ms.service: virtual-machines-sql
ms.subservice: hadr


ms.topic: tutorial
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/30/2018
ms.author: mathoma
ms.custom: "seo-lt-2019"

---

# Tutorial: Manually configure an availability group (SQL Server on Azure VMs)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

This tutorial shows how to create an Always On availability group for SQL Server on Azure Virtual Machines (VMs). The complete tutorial creates an availability group with a database replica on two SQL Servers.

While this article manually configures the availability group environment, it is also possible to do so using the [Azure portal](availability-group-azure-portal-configure.md), [PowerShell or the Azure CLI](availability-group-az-commandline-configure.md), or [Azure Quickstart templates](availability-group-quickstart-template-configure.md) as well. 


**Time estimate**: Takes about 30 minutes to complete once the [prerequisites](availability-group-manually-configure-prerequisites-tutorial.md) are met.


## Prerequisites

The tutorial assumes you have a basic understanding of SQL Server Always On availability groups. If you need more information, see [Overview of Always On availability groups (SQL Server)](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server).

Before you begin the tutorial, you need to [Complete prerequisites for creating Always On availability groups in Azure Virtual Machines](availability-group-manually-configure-prerequisites-tutorial.md). If these prerequisites are completed already, you can jump to [Create Cluster](#CreateCluster).

The following table lists the prerequisites that you need to complete before starting this tutorial:

| Requirement |Description |
|----- |----- |----- |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **Two SQL Server instances** | - In an Azure availability set <br/> - In a single domain <br/> - With Failover Clustering feature installed |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **Windows Server** | File share for cluster witness |  
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **SQL Server service account** | Domain account |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **SQL Server Agent service account** | Domain account |  
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **Firewall ports open** | - SQL Server: **1433** for default instance <br/> - Database mirroring endpoint: **5022** or any available port <br/> - Availability group load balancer IP address health probe: **59999** or any available port <br/> - Cluster core load balancer IP address health probe: **58888** or any available port |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **Add Failover Clustering Feature** | Both SQL Server instances require this feature |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **Installation domain account** | - Local administrator on each SQL Server <br/> - Member of SQL Server sysadmin fixed server role for each instance of SQL Server  |

>[!NOTE]
> Many of the steps provided in this tutorial can now be automated with the [Azure portal](availability-group-azure-portal-configure.md), [PowerShell and the Az CLI](./availability-group-az-commandline-configure.md) and [Azure Quickstart Templates](availability-group-quickstart-template-configure.md).


<!--**Procedure**: *This is the first "step". Make titles H2's and short and clear – H2's appear in the right pane on the web page and are important for navigation.*-->

<a name="CreateCluster"></a>

## Create the cluster

After the prerequisites are completed, the first step is to create a Windows Server Failover Cluster that includes two SQL Severs and a witness server.

1. Use Remote Desktop Protocol (RDP) to connect to the first SQL Server. Use a domain account that is an administrator on both SQL Servers and the witness server.

   >[!TIP]
   >If you followed the [prerequisites document](availability-group-manually-configure-prerequisites-tutorial.md), you created an account called **CORP\Install**. Use this account.

2. In the **Server Manager** dashboard, select **Tools**, and then select **Failover Cluster Manager**.
3. In the left pane, right-click **Failover Cluster Manager**, and then select **Create a Cluster**.

   ![Create Cluster](./media/availability-group-manually-configure-tutorial/40-createcluster.png)

4. In the Create Cluster Wizard, create a one-node cluster by stepping through the pages with the settings in the following table:

   | Page | Settings |
   | --- | --- |
   | Before You Begin |Use defaults |
   | Select Servers |Type the first SQL Server name in **Enter server name** and select **Add**. |
   | Validation Warning |Select **No. I do not require support from Microsoft for this cluster, and therefore do not want to run the validation tests. When I select Next, continue Creating the cluster**. |
   | Access Point for Administering the Cluster |Type a cluster name, for example **SQLAGCluster1** in **Cluster Name**.|
   | Confirmation |Use defaults unless you are using Storage Spaces. See the note following this table. |

### Set the Windows server failover cluster IP address

  > [!NOTE]
  > On Windows Server 2019, the cluster creates a **Distributed Server Name** instead of the **Cluster Network Name**. If you're using Windows Server 2019, skip any steps that refer to the cluster core name in this tutorial. You can create a cluster network name using [PowerShell](failover-cluster-instance-storage-spaces-direct-manually-configure.md#create-failover-cluster). Review the blog [Failover Cluster: Cluster Network Object](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97) for more information. 

1. In **Failover Cluster Manager**, scroll down to **Cluster Core Resources** and expand the cluster details. You should see both the **Name** and the **IP Address** resources in the **Failed** state. The IP address resource cannot be brought online because the cluster is assigned the same IP address as the machine itself, therefore it is a duplicate address.

2. Right-click the failed **IP Address** resource, and then select **Properties**.

   ![Cluster Properties](./media/availability-group-manually-configure-tutorial/42_IPProperties.png)

3. Select **Static IP Address** and specify an available address from the same subnet as your virtual machines.

4. In the **Cluster Core Resources** section, right-click cluster name and select **Bring Online**. Wait until both resources are online. When the cluster name resource comes online, it updates the domain controller (DC) server with a new Active Directory (AD) computer account. Use this AD account to run the availability group clustered service later.

### <a name="addNode"></a>Add the other SQL Server to cluster

Add the other SQL Server to the cluster.

1. In the browser tree, right-click the cluster and select **Add Node**.

    ![Add Node to the Cluster](./media/availability-group-manually-configure-tutorial/44-addnode.png)

1. In the **Add Node Wizard**, select **Next**. In the **Select Servers** page, add the second SQL Server. Type the server name in **Enter server name** and then select **Add**. When you are done, select **Next**.

1. In the **Validation Warning** page, select **No** (in a production scenario you should perform the validation tests). Then, select **Next**.

8. In the **Confirmation** page if you are using Storage Spaces, clear the checkbox labeled **Add all eligible storage to the cluster.**

   ![Add Node Confirmation](./media/availability-group-manually-configure-tutorial/46-addnodeconfirmation.png)

   >[!WARNING]
   >If you are using Storage Spaces and do not uncheck **Add all eligible storage to the cluster**, Windows detaches the virtual disks during the clustering process. As a result, they don't appear in Disk Manager or Explorer until the storage spaces are removed from the cluster and reattached using PowerShell. Storage Spaces groups multiple disks in to storage pools. For more information, see [Storage Spaces](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831739(v=ws.11)).
   >

1. Select **Next**.

1. Select **Finish**.

   Failover Cluster Manager shows that your cluster has a new node and lists it in the **Nodes** container.

10. Log out of the remote desktop session.

### Add a cluster quorum file share

In this example, the Windows cluster uses a file share to create a cluster quorum. This tutorial uses a Node and File Share Majority quorum. For more information, see [Understanding Quorum Configurations in a Failover Cluster](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731739(v=ws.11)).

1. Connect to the file share witness member server with a remote desktop session.

1. On **Server Manager**, select **Tools**. Open **Computer Management**.

1. Select **Shared Folders**.

1. Right-click **Shares**, and select **New Share...**.

   ![Right-click shares and select new share](./media/availability-group-manually-configure-tutorial/48-newshare.png)

   Use **Create a Shared Folder Wizard** to create a share.

1. On **Folder Path**, select **Browse** and locate or create a path for the shared folder. Select **Next**.

1. In **Name, Description, and Settings** verify the share name and path. Select **Next**.

1. On **Shared Folder Permissions** set **Customize permissions**. Select **Custom...**.

1. On **Customize Permissions**, select **Add...**.

1. Make sure that the account used to create the cluster has full control.

   ![Make sure the account used to create the cluster has full control](./media/availability-group-manually-configure-tutorial/50-filesharepermissions.png)

1. Select **OK**.

1. In **Shared Folder Permissions**, select **Finish**. Select **Finish** again.  

1. Log out of the server

### Configure the cluster quorum

Next, set the cluster quorum.

1. Connect to the first cluster node with remote desktop.

1. In **Failover Cluster Manager**, right-click the cluster, point to **More Actions**, and select **Configure Cluster Quorum Settings...**.

   ![Select configure cluster quorum settings](./media/availability-group-manually-configure-tutorial/52-configurequorum.png)

1. In **Configure Cluster Quorum Wizard**, select **Next**.

1. In **Select Quorum Configuration Option**, choose **Select the quorum witness**, and select **Next**.

1. On **Select Quorum Witness**, select **Configure a file share witness**.

   >[!TIP]
   >Windows Server 2016 supports a cloud witness. If you choose this type of witness, you do not need a file share witness. For more information, see [Deploy a cloud witness for a Failover Cluster](/windows-server/failover-clustering/deploy-cloud-witness). This tutorial uses a file share witness, which is supported by previous operating systems.
   >

1. On **Configure File Share Witness**, type the path for the share you created. Select **Next**.

1. Verify the settings on **Confirmation**. Select **Next**.

1. Select **Finish**.

The cluster core resources are configured with a file share witness.

## Enable availability groups

Next, enable the **AlwaysOn availability groups** feature. Do these steps on both SQL Servers.

1. From the **Start** screen, launch **SQL Server Configuration Manager**.
2. In the browser tree, select **SQL Server Services**, then right-click the **SQL Server (MSSQLSERVER)** service and select **Properties**.
3. Select the **AlwaysOn High Availability** tab, then select **Enable AlwaysOn availability groups**, as follows:

    ![Enable AlwaysOn availability groups](./media/availability-group-manually-configure-tutorial/54-enableAlwaysOn.png)

4. Select **Apply**. Select **OK** in the pop-up dialog.

5. Restart the SQL Server service.

Repeat these steps on the other SQL Server.

<!-----------------
## <a name="endpoint-firewall"></a>Open firewall for the database mirroring endpoint

Each instance of SQL Server that participates in an availability group requires a database mirroring endpoint. This endpoint is a TCP port for the instance of SQL Server that is used to synchronize the database replicas in the availability groups on that instance.

On both SQL Servers, open the firewall for the TCP port for the database mirroring endpoint.

1. On the first SQL Server **Start** screen, launch **Windows Firewall with Advanced Security**.
2. In the left pane, select **Inbound Rules**. On the right pane, select **New Rule**.
3. For **Rule Type**, choose **Port**.
1. For the port, specify TCP and choose an unused TCP port number. For example, type *5022* and select **Next**.

   >[!NOTE]
   >For this example, we're using TCP port 5022. You can use any available port.

5. In the **Action** page, keep **Allow the connection** selected and select **Next**.
6. In the **Profile** page, accept the default settings and select **Next**.
7. In the **Name** page, specify a rule name, such as **Default Instance Mirroring Endpoint** in the **Name** text box, then select **Finish**.

Repeat these steps on the second SQL Server.
-------------------------->

## Create a database on the first SQL Server

1. Launch the RDP file to the first SQL Server with a domain account that is a member of sysadmin fixed server role.
1. Open SQL Server Management Studio and connect to the first SQL Server.
7. In **Object Explorer**, right-click **Databases** and select **New Database**.
8. In **Database name**, type **MyDB1**, then select **OK**.

### <a name="backupshare"></a> Create a backup share

1. On the first SQL Server in **Server Manager**, select **Tools**. Open **Computer Management**.

1. Select **Shared Folders**.

1. Right-click **Shares**, and select **New Share...**.

   ![Select New Share](./media/availability-group-manually-configure-tutorial/48-newshare.png)

   Use **Create a Shared Folder Wizard** to create a share.

1. On **Folder Path**, select **Browse** and locate or create a path for the database backup shared folder. Select **Next**.

1. In **Name, Description, and Settings** verify the share name and path. Select **Next**.

1. On **Shared Folder Permissions** set **Customize permissions**. Select **Custom...**.

1. On **Customize Permissions**, select **Add...**.

1. Make sure that the SQL Server and SQL Server Agent service accounts for both servers have full control.

   ![Make sure that the SQL Server and SQL Server Agent service accounts for both servers have full control.](./media/availability-group-manually-configure-tutorial/68-backupsharepermission.png)

1. Select **OK**.

1. In **Shared Folder Permissions**, select **Finish**. Select **Finish** again.  

### Take a full backup of the database

You need to back up the new database to initialize the log chain. If you do not take a backup of the new database, it cannot be included in an availability group.

1. In **Object Explorer**, right-click the database, point to **Tasks...**, select **Back Up**.

1. Select **OK** to take a full backup to the default backup location.

## Create the availability group

You are now ready to configure an availability group using the following steps:

* Create a database on the first SQL Server.
* Take both a full backup and a transaction log backup of the database.
* Restore the full and log backups to the second SQL Server with the **NORECOVERY** option.
* Create the availability group (**AG1**) with synchronous commit, automatic failover, and readable secondary replicas.

### Create the availability group:

1. On remote desktop session to the first SQL Server. In **Object Explorer** in SSMS, right-click **AlwaysOn High Availability** and select **New availability group Wizard**.

    ![Launch New availability group Wizard](./media/availability-group-manually-configure-tutorial/56-newagwiz.png)

2. In the **Introduction** page, select **Next**. In the **Specify availability group Name** page, type a name for the availability group in **Availability group name**. For example, **AG1**. Select **Next**.

    ![New availability group Wizard, Specify availability group Name](./media/availability-group-manually-configure-tutorial/58-newagname.png)

3. In the **Select Databases** page, select your database, and then select **Next**.

   >[!NOTE]
   >The database meets the prerequisites for an availability group because you have taken at least one full backup on the intended primary replica.
   >

   ![New availability group Wizard, Select Databases](./media/availability-group-manually-configure-tutorial/60-newagselectdatabase.png)

4. In the **Specify Replicas** page, select **Add Replica**.

   ![New availability group Wizard, Specify Replicas](./media/availability-group-manually-configure-tutorial/62-newagaddreplica.png)

5. The **Connect to Server** dialog pops up. Type the name of the second server in **Server name**. Select **Connect**.

   Back in the **Specify Replicas** page, you should now see the second server listed in **Availability Replicas**. Configure the replicas as follows.

   ![New availability group Wizard, Specify Replicas (Complete)](./media/availability-group-manually-configure-tutorial/64-newagreplica.png)

6. Select **Endpoints** to see the database mirroring endpoint for this availability group. Use the same port that you used when you set the [firewall rule for database mirroring endpoints](availability-group-manually-configure-prerequisites-tutorial.md#endpoint-firewall).

    ![New availability group Wizard, Select Initial Data Synchronization](./media/availability-group-manually-configure-tutorial/66-endpoint.png)

8. In the **Select Initial Data Synchronization** page, select **Full** and specify a shared network location. For the location, use the [backup share that you created](#backupshare). In the example it was, **\\\\<First SQL Server\>\Backup\\**. Select **Next**.

   >[!NOTE]
   >Full synchronization takes a full backup of the database on the first instance of SQL Server and restores it to the second instance. For large databases, full synchronization is not recommended because it may take a long time. You can reduce this time by manually taking a backup of the database and restoring it with `NO RECOVERY`. If the database is already restored with `NO RECOVERY` on the second SQL Server before configuring the availability group, choose **Join only**. If you want to take the backup after configuring the availability group, choose **Skip initial data synchronization**.
   >

   ![Choose Skip initial data synchronization](./media/availability-group-manually-configure-tutorial/70-datasynchronization.png)

9. In the **Validation** page, select **Next**. This page should look similar to the following image:

    ![New availability group Wizard, Validation](./media/availability-group-manually-configure-tutorial/72-validation.png)

    >[!NOTE]
    >There is a warning for the listener configuration because you have not configured an availability group listener. You can ignore this warning because on Azure virtual machines you create the listener after creating the Azure load balancer.

10. In the **Summary** page, select **Finish**, then wait while the wizard configures the new availability group. In the **Progress** page, you can select **More details** to view the detailed progress. Once the wizard is finished, inspect the **Results** page to verify that the availability group is successfully created.

     ![New availability group Wizard, Results](./media/availability-group-manually-configure-tutorial/74-results.png)

11. Select **Close** to exit the wizard.

### Check the availability group

1. In **Object Explorer**, expand **AlwaysOn High Availability**, and then expand **availability groups**. You should now see the new availability group in this container. Right-click the availability group and select **Show Dashboard**.

   ![Show availability group Dashboard](./media/availability-group-manually-configure-tutorial/76-showdashboard.png)

   Your **AlwaysOn Dashboard** should look similar to the following screenshot:

   ![availability group Dashboard](./media/availability-group-manually-configure-tutorial/78-agdashboard.png)

   You can see the replicas, the failover mode of each replica, and the synchronization state.

2. In **Failover Cluster Manager**, select your cluster. Select **Roles**. The availability group name you used is a role on the cluster. That availability group does not have an IP address for client connections because you did not configure a listener. You will configure the listener after you create an Azure load balancer.

   ![availability group in Failover Cluster Manager](./media/availability-group-manually-configure-tutorial/80-clustermanager.png)

   > [!WARNING]
   > Do not try to fail over the availability group from the Failover Cluster Manager. All failover operations should be performed from within **AlwaysOn Dashboard** in SSMS. For more information, see [Restrictions on Using The Failover Cluster Manager with availability groups](/sql/database-engine/availability-groups/windows/failover-clustering-and-always-on-availability-groups-sql-server).
    >

At this point, you have an availability group with replicas on two instances of SQL Server. You can move the availability group between instances. You cannot connect to the availability group yet because you do not have a listener. In Azure virtual machines, the listener requires a load balancer. The next step is to create the load balancer in Azure.

<a name="configure-internal-load-balancer"></a>

## Create an Azure load balancer

[!INCLUDE [sql-ag-use-dnn-listener](../../includes/sql-ag-use-dnn-listener.md)]

On Azure virtual machines, a SQL Server availability group requires a load balancer. The load balancer holds the IP addresses for the availability group listeners and the Windows Server Failover Cluster. This section summarizes how to create the load balancer in the Azure portal.

A load balancer in Azure can be either a Standard Load Balancer or a Basic Load Balancer. Standard Load Balancer has more features than the Basic Load Balancer. For an availability group, the Standard Load Balancer is required if you use an Availability Zone (instead of an Availability Set). For details on the difference between the load balancer SKUs, see [Load Balancer SKU comparison](../../../load-balancer/skus.md).

1. In the Azure portal, go to the resource group where your SQL Servers are and select **+ Add**.
1. Search for **Load Balancer**. Choose the load balancer published by Microsoft.

   ![Choose the load balancer published by Microsoft](./media/availability-group-manually-configure-tutorial/82-azureloadbalancer.png)

1. Select **Create**.
1. Configure the following parameters for the load balancer.

   | Setting | Field |
   | --- | --- |
   | **Name** |Use a text name for the load balancer, for example **sqlLB**. |
   | **Type** |Internal |
   | **Virtual network** |Use the name of the Azure virtual network. |
   | **Subnet** |Use the name of the subnet that the virtual machine is in.  |
   | **IP address assignment** |Static |
   | **IP address** |Use an available address from subnet. Use this address for your availability group listener. Note that this is different from your cluster IP address.  |
   | **Subscription** |Use the same subscription as the virtual machine. |
   | **Location** |Use the same location as the virtual machine. |

   The Azure portal blade should look like this:

   ![Create Load Balancer](./media/availability-group-manually-configure-tutorial/84-createloadbalancer.png)

1. Select **Create**, to create the load balancer.

To configure the load balancer, you need to create a backend pool, a probe, and set the load balancing rules. Do these in the Azure portal.

### Add a backend pool for the availability group listener

1. In the Azure portal, go to your availability group. You might need to refresh the view to see the newly created load balancer.

   ![Find Load Balancer in Resource Group](./media/availability-group-manually-configure-tutorial/86-findloadbalancer.png)

1. Select the load balancer, select **Backend pools**, and select **+Add**.

1. Type a name for the backend pool.

1. Associate the backend pool with the availability set that contains the VMs.

1. Under **Target network IP configurations**, check **VIRTUAL MACHINE** and choose both of the virtual machines that will host availability group replicas. Do not include the file share witness server.

   >[!NOTE]
   >If both virtual machines are not specified, connections will only succeed to the primary replica.

1. Select **OK** to create the backend pool.

### Set the probe

1. Select the load balancer, choose **Health probes**, and then select **+Add**.

1. Set the listener health probe as follows:

   | Setting | Description | Example
   | --- | --- |---
   | **Name** | Text | SQLAlwaysOnEndPointProbe |
   | **Protocol** | Choose TCP | TCP |
   | **Port** | Any unused port | 59999 |
   | **Interval**  | The amount of time between probe attempts in seconds |5 |
   | **Unhealthy threshold** | The number of consecutive probe failures that must occur for a virtual machine to be considered unhealthy  | 2 |

1. Select **OK** to set the health probe.

### Set the load balancing rules

1. Select the load balancer, choose **Load balancing rules**, and select **+Add**.

1. Set the listener load balancing rules as follows.

   | Setting | Description | Example
   | --- | --- |---
   | **Name** | Text | SQLAlwaysOnEndPointListener |
   | **Frontend IP address** | Choose an address |Use the address that you created when you created the load balancer. |
   | **Protocol** | Choose TCP |TCP |
   | **Port** | Use the port for the availability group listener | 1433 |
   | **Backend Port** | This field is not used when Floating IP is set for direct server return | 1433 |
   | **Probe** |The name you specified for the probe | SQLAlwaysOnEndPointProbe |
   | **Session Persistence** | Drop down list | **None** |
   | **Idle Timeout** | Minutes to keep a TCP connection open | 4 |
   | **Floating IP (direct server return)** | |Enabled |

   > [!WARNING]
   > Direct server return is set during creation. It cannot be changed.
   >

1. Select **OK** to set the listener load balancing rules.

### Add the cluster core IP address for the Windows Server Failover Cluster (WSFC)

The WSFC IP address also needs to be on the load balancer.

1. In the Azure portal, go to the same Azure load balancer. Select **Frontend IP configuration** and select **+Add**. Use the IP Address you configured for the WSFC in the cluster core resources. Set the IP address as static.

1. On the load balancer, select **Health probes**, and then select **+Add**.

1. Set the WSFC cluster core IP address health probe as follows:

   | Setting | Description | Example
   | --- | --- |---
   | **Name** | Text | WSFCEndPointProbe |
   | **Protocol** | Choose TCP | TCP |
   | **Port** | Any unused port | 58888 |
   | **Interval**  | The amount of time between probe attempts in seconds |5 |
   | **Unhealthy threshold** | The number of consecutive probe failures that must occur for a virtual machine to be considered unhealthy  | 2 |

1. Select **OK** to set the health probe.

1. Set the load balancing rules. Select **Load balancing rules**, and select **+Add**.

1. Set the cluster core IP address load balancing rules as follows.

   | Setting | Description | Example
   | --- | --- |---
   | **Name** | Text | WSFCEndPoint |
   | **Frontend IP address** | Choose an address |Use the address that you created when you configured the WSFC IP address. This is different from the listener IP address |
   | **Protocol** | Choose TCP |TCP |
   | **Port** | Use the port for the cluster IP address. This is an available port that is not used for the listener probe port. | 58888 |
   | **Backend Port** | This field is not used when Floating IP is set for direct server return | 58888 |
   | **Probe** |The name you specified for the probe | WSFCEndPointProbe |
   | **Session Persistence** | Drop down list | **None** |
   | **Idle Timeout** | Minutes to keep a TCP connection open | 4 |
   | **Floating IP (direct server return)** | |Enabled |

   > [!WARNING]
   > Direct server return is set during creation. It cannot be changed.
   >

1. Select **OK** to set the load balancing rules.

## <a name="configure-listener"></a> Configure the listener

The next thing to do is to configure an availability group listener on the failover cluster.

> [!NOTE]
> This tutorial shows how to create a single listener, with one ILB IP address. To create one or more listeners using one or more IP addresses, see [Create availability group listener and load balancer | Azure](availability-group-listener-powershell-configure.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## Set listener port

In SQL Server Management Studio, set the listener port.

1. Launch SQL Server Management Studio and connect to the primary replica.

1. Navigate to **AlwaysOn High Availability** > **availability groups** > **availability group Listeners**.

1. You should now see the listener name that you created in Failover Cluster Manager. Right-click the listener name and select **Properties**.

1. In the **Port** box, specify the port number for the availability group listener. 1433 is the default. Select **OK**.

You now have a SQL Server availability group in Azure virtual machines running in Resource Manager mode.

## Test connection to listener

To test the connection:

1. Use RDP to connect to a SQL Server that is in the same virtual network, but does not own the replica. You can use the other SQL Server in the cluster.

1. Use the **sqlcmd** utility to test the connection. For example, the following script establishes a **sqlcmd** connection to the primary replica through the listener with Windows authentication:

   ```cmd
   sqlcmd -S <listenerName> -E
   ```

   If the listener is using a port other than the default port (1433), specify the port in the connection string. For example, the following `sqlcmd` command connects to a listener at port 1435:

   ```cmd
   sqlcmd -S <listenerName>,1435 -E
   ```

The SQLCMD connection automatically connects to whichever instance of SQL Server hosts the primary replica.

> [!TIP]
> Make sure that the port you specify is open on the firewall of both SQL Servers. Both servers require an inbound rule for the TCP port that you use. For more information, see [Add or Edit Firewall Rule](/previous-versions/orphan-topics/ws.11/cc753558(v=ws.11)).
>

## Next steps

- [Add an IP address to a load balancer for a second availability group](availability-group-listener-powershell-configure.md#Add-IP).