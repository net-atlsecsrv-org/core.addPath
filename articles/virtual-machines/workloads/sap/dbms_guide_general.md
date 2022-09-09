---
title: Considerations for Azure Virtual Machines DBMS deployment for SAP workload | Microsoft Docs
description: Considerations for Azure Virtual Machines DBMS deployment for SAP workload
author: msjuergent
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: article
ms.date: 09/20/2020
ms.author: juergent
ms.reviewer: cynthn

---

# Considerations for Azure Virtual Machines DBMS deployment for SAP workload
[1114181]:https://launchpad.support.sap.com/#/notes/1114181
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2171857]:https://launchpad.support.sap.com/#/notes/2171857
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[deployment-guide]:deployment-guide.md
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e
[planning-guide]:planning-guide.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png



This guide is part of the documentation on how to implement and deploy SAP software on Microsoft Azure. Before you read this guide, read the [Planning and implementation guide][planning-guide] and articles the planning guide points you to. This document covers the generic deployment aspects of SAP-related DBMS systems on Microsoft Azure virtual machines (VMs) by using the Azure infrastructure as a service (IaaS) capabilities.

The paper complements the SAP installation documentation and SAP Notes, which represent the primary resources for installations and deployments of SAP software on given platforms.

In this document, considerations of running SAP-related DBMS systems in Azure VMs are introduced. There are few references to specific DBMS systems in this chapter. Instead, the specific DBMS systems are handled within this paper, after this document.

## Definitions
Throughout the document, these terms are used:

* **IaaS**: Infrastructure as a service.
* **PaaS**: Platform as a service.
* **SaaS**: Software as a service.
* **SAP component**: An individual SAP application such as ERP Central Component (ECC), Business Warehouse (BW), Solution Manager, or Enterprise Portal (EP). SAP components can be based on traditional ABAP or Java technologies or on a non-NetWeaver-based application such as Business Objects.
* **SAP environment**: One or more SAP components logically grouped to perform a business function such as development, quality assurance, training, disaster recovery, or production.
* **SAP landscape**: This term refers to the entire SAP assets in a customer's IT landscape. The SAP landscape includes all production and nonproduction environments.
* **SAP system**: The combination of a DBMS layer and an application layer of, for example, an SAP ERP development system, an SAP Business Warehouse test system, or an SAP CRM production system. In Azure deployments, dividing these two layers between on-premises and Azure isn't supported. As a result, an SAP system is either deployed on-premises or it's deployed in Azure. You can deploy the different systems of an SAP landscape in Azure or on-premises. For example, you could deploy the SAP CRM development and test systems in Azure but deploy the SAP CRM production system on-premises.
* **Cross-premises**: Describes a scenario where VMs are deployed to an Azure subscription that has site-to-site, multisite, or Azure ExpressRoute connectivity between the on-premises data centers and Azure. In common Azure documentation, these kinds of deployments are also described as cross-premises scenarios. 

    The reason for the connection is to extend on-premises domains, on-premises Active Directory, and on-premises DNS into Azure. The on-premises landscape is extended to the Azure assets of the subscription. With this extension, the VMs can be part of the on-premises domain. Domain users of the on-premises domain can access the servers and run services on those VMs, like DBMS services. Communication and name resolution between VMs deployed on-premises and VMs deployed in Azure is possible. This scenario is the most common scenario in use to deploy SAP assets on Azure. For more information, see [Planning and design for VPN gateway](../../../vpn-gateway/vpn-gateway-about-vpngateways.md).

> [!NOTE]
> Cross-premises deployments of SAP systems are where Azure virtual machines that run SAP systems are members of an on-premises domain and are supported for production SAP systems. Cross-premises configurations are supported for deploying parts or complete SAP landscapes into Azure. Even running the complete SAP landscape in Azure requires those VMs to be part of an on-premises domain and Active Directory/LDAP. 
>
> In previous versions of the documentation, hybrid-IT scenarios were mentioned. The term *hybrid* is rooted in the fact that there's a cross-premises connectivity between on-premises and Azure. In this case, hybrid also means that the VMs in Azure are part of the on-premises Active Directory.
>
>

Some Microsoft documentation describes cross-premises scenarios a bit differently, especially for DBMS high-availability configurations. In the case of the SAP-related documents, the cross-premises scenario boils down to site-to-site or private [ExpressRoute](https://azure.microsoft.com/services/expressroute/) connectivity and an SAP landscape that's distributed between on-premises and Azure.

## Resources
There are other articles available on SAP workload on Azure. Start with [SAP workload on Azure: Get started](./get-started.md) and then choose your area of interest.

The following SAP Notes are related to SAP on Azure in regard to the area covered in this document.

| Note number | Title |
| --- | --- |
| [1928533] |SAP applications on Azure: Supported products and Azure VM types |
| [2015553] |SAP on Microsoft Azure: Support prerequisites |
| [1999351] |Troubleshooting enhanced Azure monitoring for SAP |
| [2178632] |Key monitoring metrics for SAP on Microsoft Azure |
| [1409604] |Virtualization on Windows: Enhanced monitoring |
| [2191498] |SAP on Linux with Azure: Enhanced monitoring |
| [2039619] |SAP applications on Microsoft Azure using the Oracle database: Supported products and versions |
| [2233094] |DB6: SAP applications on Azure using IBM DB2 for Linux, UNIX, and Windows: Additional information |
| [2243692] |Linux on Microsoft Azure (IaaS) VM: SAP license issues |
| [1984787] |SUSE LINUX Enterprise Server 12: Installation notes |
| [2002167] |Red Hat Enterprise Linux 7.x: Installation and upgrade |
| [2069760] |Oracle Linux 7.x SAP installation and upgrade |
| [1597355] |Swap-space recommendation for Linux |
| [2171857] |Oracle Database 12c: File system support on Linux |
| [1114181] |Oracle Database 11g: File system support on Linux |


For information on all the SAP Notes for Linux, see the [SAP community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes).

You need a working knowledge of Microsoft Azure architecture and how Microsoft Azure virtual machines are deployed and operated. For more information, see [Azure documentation](../../../index.yml).

In general, the Windows, Linux, and DBMS installation and configuration are essentially the same as any virtual machine or bare metal machine you install on-premises. There are some architecture and system management implementation decisions that are different when you use Azure IaaS. This document explains the specific architectural and system management differences to be prepared for when you use Azure IaaS.


## <a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>Storage structure of a VM for RDBMS deployments
To follow this chapter, read and understand the information presented in:

- [Azure Virtual Machines planning and implementation for SAP NetWeaver](./planning-guide.md)
- [Azure Storage types for SAP workload](./planning-guide-storage.md)
- [What SAP software is supported for Azure deployments](./sap-supported-product-on-azure.md)
- [SAP workload on Azure virtual machine supported scenarios](./sap-planning-supported-configurations.md) 

You need to understand and know about the different VM-Series and the differences between standard and premium storage before you read this chapter. 

For Azure block storage, the usage of Azure managed disks is highly recommended. For details about Azure managed disks read the article [Introduction to managed disks for Azure VMs](../../managed-disks-overview.md).

In a basic configuration, we usually recommend a deployment structure where the operating system, DBMS, and eventual SAP binaries are separate from the database files. Changing earlier recommendations, we recommend having separate Azure disks for:

- The operating system (base VHD or OS VHD)
- Database management system executables
- SAP executables like /usr/sap

A configuration that separates these components in three different Azure disks can result in higher resiliency since excessive log or dump writes by the DBMS or SAP executables are not interfering with the disk quotas of the OS disk. 

The DBMS data and transaction/redo log files are stored in Azure supported block storage or Azure NetApp Files. They're stored in separate disks and attached as logical disks to the original Azure operating system image VM. For Linux deployments, different recommendations are documented, especially for SAP HANA. Read the article [Azure Storage types for SAP workload](./planning-guide-storage.md) for the capabilities and the support of the different storage types for your scenario. 

When you plan your disk layout, find the best balance between these items:

* The number of data files.
* The number of disks that contain the files.
* The IOPS quotas of a single disk or NFS share.
* The data throughput per disk or NFS share.
* The number of additional data disks possible per VM size.
* The overall storage or network throughput a VM can provide.
* The latency different Azure Storage types can provide.
* VM SLAs.

Azure enforces an IOPS quota per data disk or NFS share. These quotas are different for disks hosted on the different Azure block storage solutions or shares. I/O latency is also different between these different storage types as well. 

Each of the different VM types has a limited number of data disks that you can attach. Another restriction is that only certain VM types can use, for example, premium storage. Typically, you decide to use a certain VM type based on CPU and memory requirements. You also might consider the IOPS, latency, and disk throughput requirements that usually are scaled with the number of disks or the type of premium storage disks. The number of IOPS and the throughput to be achieved by each disk might dictate disk size, especially with premium storage.

> [!NOTE]
> For DBMS deployments, we recommend Azure premium storage, Ultra disk or Azure NetApp Files based NFS shares (exclusively for SAP HANA) for any data, transaction log, or redo files. It doesn't matter whether you want to deploy production or nonproduction systems.

> [!NOTE]
> To benefit from Azure's [single VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/), all disks that are attached must be Azure premium storage or Azure Ultra disk type, which includes the base VHD (Azure premium storage).

> [!NOTE]
> Hosting main database files, such as data and log files, of SAP databases on storage hardware that's located in co-located third-party data centers adjacent to Azure data centers isn't supported. Storage provided through software appliances hosted in Azure VMs, are also not supported for this use case. For SAP DBMS workloads, only storage that's represented as native Azure service is supported for the data and transaction log files of SAP databases in general. Different DBMS might support different Azure storage types. For more details check the article [Azure Storage types for SAP workload](./planning-guide-storage.md)

The placement of the database files and the log and redo files and the type of Azure Storage you use, is defined by IOPS, latency, and throughput requirements. Specifically for Azure premium storage to achieve enough IOPS, you might be forced to use multiple disks or use a larger premium storage disk. If you use multiple disks, build a software stripe across the disks that contain the data files or the log and redo files. In such cases, the IOPS and the disk throughput SLAs of the underlying premium storage disks or the maximum achievable IOPS of standard storage disks are accumulative for the resulting stripe set.

If your IOPS requirement exceeds what a single VHD can provide, balance the number of IOPS that are needed for the database files across a number of VHDs. The easiest way to distribute the IOPS load across disks is to build a software stripe over the different disks. Then place a number of data files of the SAP DBMS on the LUNs carved out of the software stripe. The number of disks in the stripe is driven by IOPS demands, disk throughput demands, and volume demands.


---
> ![Windows storage striping][Logo_Windows] Windows
>
> We recommend that you use Windows Storage Spaces to create stripe sets across multiple Azure VHDs. Use at least Windows Server 2012 R2 or Windows Server 2016.
>
> ![Linux storage striping][Logo_Linux] Linux
>
> Only MDADM and Logical Volume Manager (LVM) are supported to build a software RAID on Linux. For more information, see:
>
> - [Configure software RAID on Linux](/previous-versions/azure/virtual-machines/linux/configure-raid) using MDADM
> - [Configure LVM on a Linux VM in Azure](/previous-versions/azure/virtual-machines/linux/configure-lvm) using LVM
>
>

---

For Azure Ultra disk, striping is not necessary since you can define IOPS and disk throughput independent of the size of the disk.


> [!NOTE]
> Because Azure Storage keeps three images of the VHDs, it doesn't make sense to configure a redundancy when you stripe. You only need to configure striping so that the I/Os are distributed over the different VHDs.
>

### Managed or nonmanaged disks
An Azure storage account is an administrative construct and also a subject of limitations. Limitations differ between standard storage accounts and premium storage accounts. For information on capabilities and limitations, see [Azure Storage scalability and performance targets](../../../storage/common/scalability-targets-standard-account.md).

For standard storage, remember that there's a limit on the IOPS per storage account. See the row that contains **Total Request Rate** in the article [Azure Storage scalability and performance targets](../../../storage/common/scalability-targets-standard-account.md). There's also an initial limit on the number of storage accounts per Azure subscription. Balance VHDs for the larger SAP landscape across different storage accounts to avoid hitting the limits of these storage accounts. This is tedious work when you're talking about a few hundred virtual machines with more than a thousand VHDs.

Using standard storage for DBMS deployments in conjunction with an SAP workload isn't recommended. Therefore, references and recommendations to standard storage are limited to this short [article](/archive/blogs/mast/configuring-azure-virtual-machines-for-optimal-storage-performance)

To avoid the administrative work of planning and deploying VHDs across different Azure storage accounts, Microsoft introduced [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) in 2017. Managed disks are available for standard storage and premium storage. The major advantages of managed disks compared to nonmanaged disks are:

- For managed disks, Azure distributes the different VHDs across different storage accounts automatically at deployment time. In this way, storage account limits for data volume, I/O throughput, and IOPS aren’t hit.
- Using managed disks, Azure Storage honors the concepts of Azure availability sets. If the VM is part of an Azure availability set, the base VHD and attached disk of a VM are deployed into different fault and update domains.


> [!IMPORTANT]
> Given the advantages of Azure Managed Disks, we highly recommend that you use Azure Managed Disks for your DBMS deployments and SAP deployments in general.
>

To convert from unmanaged to managed disks, see:

- [Convert a Windows virtual machine from unmanaged disks to managed disks](../../windows/convert-unmanaged-to-managed-disks.md).
- [Convert a Linux virtual machine from unmanaged disks to managed disks](../../linux/convert-unmanaged-to-managed-disks.md).


### <a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>Caching for VMs and data disks
When you mount disks to VMs, you can choose whether the I/O traffic between the VM and those disks located in Azure storage is cached.

The following recommendations assume these I/O characteristics for standard DBMS:

- It's mostly a read workload against data files of a database. These reads are performance critical for the DBMS system.
- Writing against the data files occurs in bursts based on checkpoints or a constant stream. Averaged over a day, there are fewer writes than reads. Opposite to reads from data files, these writes are asynchronous and don't hold up any user transactions.
- There are hardly any reads from the transaction log or redo files. Exceptions are large I/Os when you perform transaction log backups.
- The main load against transaction or redo log files is writes. Dependent on the nature of the workload, you can have I/Os as small as 4 KB or, in other cases, I/O sizes of 1 MB or more.
- All writes must be persisted on disk in a reliable fashion.

For standard storage, the possible cache types are:

* None
* Read
* Read/Write

To get consistent and deterministic performance, set the caching on standard storage for all disks that contain DBMS-related data files, log and redo files, and table space to **NONE**. The caching of the base VHD can remain with the default.

For Azure premium storage, the following caching options exist:

* None
* Read
* Read/write
* None + Write Accelerator, which is only for Azure M-Series VMs
* Read + Write Accelerator, which is only for Azure M-Series VMs

For premium storage, we recommend that you use **Read caching for data files** of the SAP database and choose **No caching for the disks of log file(s)**.

For M-Series deployments, we recommend that you use Azure Write Accelerator for your DBMS deployment. For details, restrictions, and deployment of Azure Write Accelerator, see [Enable Write Accelerator](../../how-to-enable-write-accelerator.md).

For Ultra disk and Azure NetApp Files, no caching options are offered.


### Azure nonpersistent disks
Azure VMs offer nonpersistent disks after a VM is deployed. In the case of a VM reboot, all content on those drives is wiped out. It's a given that data files and log and redo files of databases should under no circumstances be located on those nonpersisted drives. There might be exceptions for some databases, where these nonpersisted drives could be suitable for tempdb and temp tablespaces. Avoid using those drives for A-Series VMs because those nonpersisted drives are limited in throughput with that VM family. 

For more information, see [Understand the temporary drive on Windows VMs in Azure](/archive/blogs/mast/understanding-the-temporary-drive-on-windows-azure-virtual-machines).

---
> ![Windows nonpersisted disk][Logo_Windows] Windows
>
> Drive D in an Azure VM is a nonpersisted drive, which is backed by some local disks on the Azure compute node. Because it's nonpersisted, any changes made to the content on drive D are lost when the VM is rebooted. Changes include files that were stored, directories that were created, and applications that were installed.
>
> ![Linuxnonpersisted disk][Logo_Linux] Linux
>
> Linux Azure VMs automatically mount a drive at /mnt/resource that's a nonpersisted drive backed by local disks on the Azure compute node. Because it's nonpersisted, any changes made to content in /mnt/resource are lost when the VM is rebooted. Changes include files that were stored, directories that were created, and applications that were installed.
>
>

---



### <a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Microsoft Azure Storage resiliency
Microsoft Azure Storage stores the base VHD, with OS and attached disks or blobs, on at least three separate storage nodes. This type of storage is called locally redundant storage (LRS). LRS is the default for all types of storage in Azure.

There are other redundancy methods. For more information, see [Azure Storage replication](../../../storage/common/storage-redundancy.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

> [!NOTE]
> Azure premium storage, Ultra disk and Azure NetApp Files (exclusively for SAP HANA) are the recommended type of storage for DBMS VMs and disks that store database and log and redo files. The only available redundancy method for these storage types is LRS. As a result, you need to configure database methods to enable database data replication into another Azure region or availability zone. Database methods include SQL Server Always On, Oracle Data Guard, and HANA System Replication.


> [!NOTE]
> For DBMS deployments, the use of geo-redundant storage (GRS) isn't recommended for standard storage. GRS severely affects performance and doesn't honor the write order across different VHDs that are attached to a VM. Not honoring the write order across different VHDs potentially leads to inconsistent databases on the replication target side. This situation occurs if database and log and redo files are spread across multiple VHDs, as is generally the case, on the source VM side.



## VM node resiliency
Azure offers several different SLAs for VMs. For more information, see the most recent release of [SLA for Virtual Machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/). Because the DBMS layer is critical to availability in an SAP system, you need to understand availability sets, Availability Zones, and maintenance events. For more information on these concepts, see [Manage the availability of Windows virtual machines in Azure](../../manage-availability.md) and [Manage the availability of Linux virtual machines in Azure](../../manage-availability.md).

The minimum recommendation for production DBMS scenarios with an SAP workload is to:

- Deploy two VMs in a separate availability set in the same Azure region.
- Run these two VMs in the same Azure virtual network and have NICs attached out of the same subnets.
- Use database methods to keep a hot standby with the second VM. Methods can be SQL Server Always On, Oracle Data Guard, or HANA System Replication.

You also can deploy a third VM in another Azure region and use the same database methods to supply an asynchronous replica in another Azure region.

For information on how to set up Azure availability sets, see [this tutorial](../../windows/tutorial-availability-sets.md).



## Azure network considerations
In large-scale SAP deployments, use the blueprint of [Azure Virtual Datacenter](/azure/architecture/vdc/networking-virtual-datacenter). Use it for your virtual network configuration and permissions and role assignments to different parts of your organization.

These best practices are the result of hundreds of customer deployments:

- The virtual networks the SAP application is deployed into don't have access to the internet.
- The database VMs run in the same virtual network as the application layer, separated in a different subnet from the SAP application layer.
- The VMs within the virtual network have a static allocation of the private IP address. For more information, see [IP address types and allocation methods in Azure](../../../virtual-network/public-ip-addresses.md).
- Routing restrictions to and from the DBMS VMs are *not* set with firewalls installed on the local DBMS VMs. Instead, traffic routing is defined with [network security groups (NSGs)](../../../virtual-network/network-security-groups-overview.md).
- To separate and isolate traffic to the DBMS VM, assign different NICs to the VM. Every NIC gets a different IP address, and every NIC is assigned to a different virtual network subnet. Every subnet has different NSG rules. The isolation or separation of network traffic is a measure for routing. It's not used to set quotas for network throughput.

> [!NOTE]
> Assigning static IP addresses through Azure means to assign them to individual virtual NICs. Don't assign static IP addresses within the guest OS to a virtual NIC. Some Azure services like Azure Backup rely on the fact that at least the primary virtual NIC is set to DHCP and not to static IP addresses. For more information, see [Troubleshoot Azure virtual machine backup](../../../backup/backup-azure-vms-troubleshoot.md#networking). To assign multiple static IP addresses to a VM, assign multiple virtual NICs to a VM.
>


> [!WARNING]
> Configuring [network virtual appliances](https://azure.microsoft.com/solutions/network-appliances/) in the communication path between the SAP application and the DBMS layer of a SAP NetWeaver-, Hybris-, or S/4HANA-based SAP system isn't supported. This restriction is for functionality and performance reasons. The communication path between the SAP application layer and the DBMS layer must be a direct one. The restriction doesn't include [application security group (ASG) and NSG rules](../../../virtual-network/network-security-groups-overview.md) if those ASG and NSG rules allow a direct communication path. 
>
> Other scenarios where network virtual appliances aren't supported are in:
>
> * Communication paths between Azure VMs that represent Linux Pacemaker cluster nodes and SBD devices as described in [High availability for SAP NetWeaver on Azure VMs on SUSE Linux Enterprise Server for SAP Applications](./high-availability-guide-suse.md).
> * Communication paths between Azure VMs and Windows Server Scale-Out File Server (SOFS) set up as described in [Cluster an SAP ASCS/SCS instance on a Windows failover cluster by using a file share in Azure](./sap-high-availability-guide-wsfc-file-share.md). 
>
> Network virtual appliances in communication paths can easily double the network latency between two communication partners. They also can restrict throughput in critical paths between the SAP application layer and the DBMS layer. In some customer scenarios, network virtual appliances can cause Pacemaker Linux clusters to fail. These are cases where communications between the Linux Pacemaker cluster nodes communicate to their SBD device through a network virtual appliance.
>

> [!IMPORTANT]
> Another design that's *not* supported is the segregation of the SAP application layer and the DBMS layer into different Azure virtual networks that aren't [peered](../../../virtual-network/virtual-network-peering-overview.md) with each other. We recommend that you segregate the SAP application layer and DBMS layer by using subnets within an Azure virtual network instead of by using different Azure virtual networks. 
>
> If you decide not to follow the recommendation and instead segregate the two layers into different virtual networks, the two virtual networks must be [peered](../../../virtual-network/virtual-network-peering-overview.md). 
>
> Be aware that network traffic between two [peered](../../../virtual-network/virtual-network-peering-overview.md) Azure virtual networks is subject to transfer costs. Huge data volume that consists of many terabytes is exchanged between the SAP application layer and the DBMS layer. You can accumulate substantial costs if the SAP application layer and DBMS layer are segregated between two peered Azure virtual networks.

Use two VMs for your production DBMS deployment within an Azure availability set or between two Azure Availability Zones. Also use separate routing for the SAP application layer and the management and operations traffic to the two DBMS VMs. See the following image:

![Diagram of two VMs in two subnets](./media/virtual-machines-shared-sap-deployment-guide/general_two_dbms_two_subnets.PNG)


### Use Azure Load Balancer to redirect traffic
The use of private virtual IP addresses used in functionalities like SQL Server Always On or HANA System Replication requires the configuration of an Azure load balancer. The load balancer uses probe ports to determine the active DBMS node and route the traffic exclusively to that active database node. 

If there's a failover of the database node, there's no need for the SAP application to reconfigure. Instead, the most common SAP application architectures reconnect against the private virtual IP address. Meanwhile, the load balancer reacts to the node failover by redirecting the traffic against the private virtual IP address to the second node.

Azure offers two different [load balancer SKUs](../../../load-balancer/load-balancer-overview.md): a basic SKU and a standard SKU. Based on the advantages in setup and functionality, you should use the Standard SKU of the Azure load balancer. One of the large advantages of the Standard version of the load balancer is that the data traffic is not routed through the load balancer itself.

An example how you can configure an internal load balancer can be found in the article [Tutorial: Configure a SQL Server availability group on Azure Virtual Machines manually](../../../azure-sql/virtual-machines/windows/availability-group-manually-configure-tutorial.md#create-an-azure-load-balancer)

> [!NOTE]
> There are differences in behavior of the basic and standard SKU related to the access of public IP addresses. The way how to work around the restrictions of the Standard SKU to access public IP addresses is described in the document [Public endpoint connectivity for Virtual Machines using Azure Standard Load Balancer in SAP high-availability scenarios](./high-availability-guide-standard-load-balancer-outbound-connections.md)


### Azure Accelerated Networking
To further reduce network latency between Azure VMs, we recommend that you choose [Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/). Use it when you deploy Azure VMs for an SAP workload, especially for the SAP application layer and the SAP DBMS layer.

> [!NOTE]
> Not all VM types support Accelerated Networking. The previous article lists the VM types that support Accelerated Networking.
>

---
> ![Windows accelerated networking][Logo_Windows] Windows
>
> To learn how to deploy VMs with Accelerated Networking for Windows, see [Create a Windows virtual machine with Accelerated Networking](../../../virtual-network/create-vm-accelerated-networking-powershell.md).
>
> ![Linux accelerated networking][Logo_Linux] Linux
>
> For more information on Linux distribution, see [Create a Linux virtual machine with Accelerated Networking](../../../virtual-network/create-vm-accelerated-networking-cli.md).
>
>

---

> [!NOTE]
> In the case of SUSE, Red Hat, and Oracle Linux, Accelerated Networking is supported with recent releases. Older releases like SLES 12 SP2 or RHEL 7.2 don't support Azure Accelerated Networking.
>


## Deployment of host monitoring
For production use of SAP applications in Azure virtual machines, SAP requires the ability to get host monitoring data from the physical hosts that run the Azure virtual machines. A specific SAP Host Agent patch level is required that enables this capability in SAPOSCOL and SAP Host Agent. The exact patch level is documented in SAP Note [1409604].

For more information on the deployment of components that deliver host data to SAPOSCOL and SAP Host Agent and the life-cycle management of those components, see the [Deployment guide][deployment-guide].


## Next steps
For more information on a particular DBMS, see:

- [SQL Server Azure Virtual Machines DBMS deployment for SAP workload](dbms_guide_sqlserver.md)
- [Oracle Azure Virtual Machines DBMS deployment for SAP workload](dbms_guide_oracle.md)
- [IBM DB2 Azure Virtual Machines DBMS deployment for SAP workload](dbms_guide_ibm.md)
- [SAP ASE Azure Virtual Machines DBMS deployment for SAP workload](dbms_guide_sapase.md)
- [SAP maxDB, Live Cache, and Content Server deployment on Azure](dbms_guide_maxdb.md)
- [SAP HANA on Azure operations guide](hana-vm-operations.md)
- [SAP HANA high availability for Azure virtual machines](sap-hana-availability-overview.md)
- [Backup guide for SAP HANA on Azure virtual machines](sap-hana-backup-guide.md)