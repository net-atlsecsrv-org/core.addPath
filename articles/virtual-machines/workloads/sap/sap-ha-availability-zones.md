---
title: SAP workload configurations with Azure Availability Zones  | Microsoft Docs
description: High-availability architecture and scenarios for SAP NetWeaver using Azure Availability Zones
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/29/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017

---

# SAP workload configurations with Azure Availability Zones
Additionally to the deployment of the different SAP architecture layers in Azure availability sets, the more recently introduced [Azure Availability Zones](../../../availability-zones/az-overview.md) can be used for SAP workload deployments as well. An Azure Availability Zone is defined as: "Unique physical locations within a region. Each zone is made up of one or more datacenters equipped with independent power, cooling, and networking". Azure Availability Zones are not available in all regions. For Azure regions that provide Availability Zones, check the [Azure region map](https://azure.microsoft.com/global-infrastructure/geographies/). This map is going to show you which regions provide or are announced to provide Availability Zones. 

As of the typical SAP NetWeaver or S/4HANA architecture, you need to protect three different layers:

- SAP application layer, which can be one to a few dozen VMs. You want to minimize the chance of VMs getting deployed on the same host server. You also want those VMs in an acceptable proximity to the DBMS layer to keep network latency in an acceptable window
- SAP ASCS/SCS layer that is representing a single point of failure in the SAP NetWeaver and S/4HANA architecture. You usually look at two VMs that you want to cover with a failover framework. Therefore, these VMs should be allocated in different infrastructure fault and update domains
- SAP DBMS layer, which represents a single point of failure as well. In the usual cases, it consists out of two VMs that are covered by a failover framework. Therefore, these VMs should be allocated in different infrastructure fault and update domains. Exceptions are SAP HANA scale-out deployments where more than two VMs are can be used

The major differences between deploying your critical VMs through availability sets or Availability Zones are:

- Deploying with an availability set is lining up the VMs within the set in a single zone or datacenter (whatever applies for the specific region). As a result the deployment through the availability set is not protected by power, cooling or networking issues that affect the dataceter(s) of the zone as a whole. On the plus side, the VMs are aligned with update and fault domains within that zone or datacenter. Specifically for the SAP ASCS or DBMS layer where we protect two VMs per availability set, the alignment with fault and update domains prevents that both VMs are ending up on the same host hardware 
- Deploying VMs through an Azure Availability Zones and choosing different zones (maximum of three possible so far), is going to deploy the VMs across the different physical locations and with that add the additional protection from power, cooling or networking issues that affect the dataceter(s) of the zone as a whole. However, as you deploy more than one VM of the same VM family into the same Availability Zone, there is no protection from those VMs ending up on the same host. As a result, deploying through Availability Zones is ideal for the SAP ASCS and DBMS layer where we usually look at two VMs each. For the SAP application layer, which can be drastically more than two VMs, you might need to fall back to a different deployment model (see later)

Your motivation for a deployment across Azure Availability Zones should be that you, on top of covering failure of a single critical VM or ability to reduce downtime for software patching within a critical, want to protect from larger infrastructure issues that might affect the availability of one or multiple Azure datacenters. 

## Considerations for deploying across Availability Zones


Consider the following when you use Availability Zones:

- There are no guarantees regarding the distances between various Availability Zones within an Azure region.
- Availability Zones are not an ideal DR solution. Natural disasters can cause widespread damage in world regions, including heavy damage to power infrastructures. The distances between various zones might not be large enough to constitute a proper DR solution.
- The network latency across Availability Zones is not the same in all Azure regions. In some cases, you can deploy and run the SAP application layer across different zones because the network latency from one zone to the active DBMS VM is acceptable. But in some Azure regions, the latency between the active DBMS VM and the SAP application instance, when deployed in different zones, might not be acceptable for SAP business processes. In these cases, the deployment architecture needs to be different, with an active/active architecture for the application, or an active/passive architecture where cross-zone network latency is too high.
- When deciding where to use Availability Zones, base your decision on the network latency between the zones. Network latency plays an important role in two areas:
	- Latency between the two DBMS instances that need to have synchronous replication. The higher the network latency, the more likely it will affect the scalability of your workload.
	- The difference in network latency between a VM running an SAP dialog instance in-zone with the active DBMS instance and a similar VM in another zone. As this difference increases, the influence on the running time of business processes and batch jobs also increases, dependent on whether they run in-zone with the DBMS or in a different zone.

When you deploy Azure VMs across Availability Zones and establish failover solutions within the same Azure region, some restrictions apply:

- You must use [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) when you deploy to Azure Availability Zones. 
- The mapping of zone enumerations to the physical zones is fixed on an Azure subscription basis. If you're using different subscriptions to deploy your SAP systems, you need to define the ideal zones for each subscription.
- You can't deploy Azure availability sets within an Azure Availability Zone unless you use [Azure Proximity Placement Group](../../co-location.md). The way how you can deploy the SAP DBMS layer and the central services across zones and at the same time deploy the SAP application layer using availability sets and still achieve close proximity of the VMs is documented in the article [Azure Proximity Placement Groups for optimal network latency with SAP applications](sap-proximity-placement-scenarios.md). If you are not using Azure proximity placement groups, you need to choose one or the other as a deployment framework for virtual machines.
- You can't use an [Azure Basic Load Balancer](../../../load-balancer/load-balancer-overview.md) to create failover cluster solutions based on Windows Server Failover Clustering or Linux Pacemaker. Instead, you need to use the [Azure Standard Load Balancer SKU](../../../load-balancer/load-balancer-standard-availability-zones.md).



## The ideal Availability Zones combination
If you want to deploy an SAP NetWeaver or S/4HANA system across zones, there are two principle architectures you can deploy:

- Active/active: The pair of VMs running ASCS/SCS and the pair of VMS running the DBMS layer are distributed across two zones. The number of VMs running the SAP application layer are deployed to an even numbers across the same two zones. If a DBMS or ASCS/SCS VM is failing over, some of the open and active transactions might be rolled back. But users are remaining logged in. It does not really matter in which of the zones the active DBMS VM and the application instances run. This architecture is the preferred architecture to deploy across zones.
- Active/passive: The pair of VMs running ASCS/SCS and the pair of VMS running the DBMS layer are distributed across two zones. The number of VMs running the SAP application layer are deployed into one of the Availability Zones. You run the application layer in the same zone as the active ASCS/SCS and DBMS instance. You use this deployment architecture if the network latency across the different zones is too high to run the application layer distributed across the zones. Instead the SAP application layer needs to run in the same zone as the active ASCS/SCS and/or DBMS instance. If an ASCS/SCS or DBMS VM fails over to the secondary zone, you might encounter higher network latency and with that a reduction of throughput. And you are required to fail back the previously failed over VM as soon as possible to get back to the previous throughput levels. If a zonal outage occurs, the application layer needs to be failed over to the secondary zone. An activity that users experience as complete system shutdown. In some of the Azure regions, this architecture is the only viable architecture when you want to use Availability Zones. If you can't accept the potential impact of an ASCS/SCS or DBMS VMS failing over to the secondary zone, you might be better of staying with availability set deployments


So before you decide how to use Availability Zones, you need to determine:

- The network latency among the three zones of an Azure region. Knowing the network latency between the zones of a region is going to enable you to choose the zones with the least network latency in cross-zone network traffic.
- The difference between VM-to-VM latency within one of the zones, of your choosing, and the network latency across two zones of your choosing.
- A determination of whether the VM types that you need to deploy are available in the two zones that you selected. With some VMs, especially M-Series VMs, you might encounter situations in which some SKUs are available in only two of the three zones.

## Network latency between and within zones
To determine the latency between the different zones, you need to:

- Deploy the VM SKU you want to use for your DBMS instance in all three zones. Make sure [Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) is enabled when you take this measurement.
- When you find the two zones with the least network latency, deploy another three VMs of the VM SKU that you want to use as the application layer VM across the three Availability Zones. Measure the network latency against the two DBMS VMs in the two DBMS zones that you selected. 
- Use **`niping`** as a measuring tool. This tool, from SAP, is described in SAP support notes [#500235](https://launchpad.support.sap.com/#/notes/500235) and [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Focus on the commands documented for latency measurements. Because **ping** doesn't work through the Azure Accelerated Networking code paths, we don't recommend that you use it.

You don't need to perform these tests manually. You can find a PowerShell procedure [Availability Zone Latency Test](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test) that automates the latency tests described. 

Based on your measurements and the availability of your VM SKUs in the Availability Zones, you need to make some decisions:

- Define the ideal zones for the DBMS layer.
- Determine whether you want to distribute your active SAP application layer across one, two, or all three zones, based on differences of network latency in-zone versus across zones.
- Determine whether you want to deploy an active/passive configuration or an active/active configuration, from an application point of view. (These configurations are explained later in this article.)

In making these decisions, also take into account SAP's network latency recommendations, as documented in SAP note [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E).

> [!IMPORTANT]
> The measurements and decisions you make are valid for the Azure subscription you used when you took the measurements. If you use another Azure subscription, you need to repeat the measurements. The mapping of enumerated zones might be different for another Azure subscription.


> [!IMPORTANT]
> It's expected that the measurements described earlier will provide different results in every Azure region that supports [Availability Zones](https://azure.microsoft.com/global-infrastructure/geographies/). Even if your network latency requirements are the same, you might need to adopt different deployment strategies in different Azure regions because the network latency between zones can be different. In some Azure regions, the network latency among the three different zones can be vastly different. In other regions, the network latency among the three different zones might be more uniform. The claim that there is always a network latency  between 1 and 2 milliseconds  is not correct. The network latency across Availability Zones in Azure regions can't be generalized.

## Active/Active deployment
This deployment architecture is called active/active because you deploy your active SAP application servers across two or three zones. The SAP Central Services instance that uses enqueue replication will be deployed between two zones. The same is true for the DBMS layer, which will be deployed across the same zones as SAP Central Service. When considering this configuration, you need to find the two Availability Zones in your region that offer cross-zone network latency that's acceptable for your workload and your synchronous DBMS replication. You also want to be sure the delta between network latency within the zones you selected and the cross-zone network latency isn't too large. 

Nature of the SAP architecture is that, unless you configure it differently, users and batch jobs can be executed in the different application instances. The side effect of this fact with the active/active deployment is that batch jobs might be executed by any SAP application instances independent on whether those run in the same zone with the active DBMS or not. If the difference in network latency between the difference zones is small compared to network latency within a zone, the difference in run times of batch jobs might not be significant. However, the larger the difference of network latency within a zone compared to across zone network traffic is, the run time of batch jobs can be impacted more if the job got executed in a zone where the DBMS instance is not active. It is on you as a customer to decide what acceptable differences in run time are. And with that what the tolerable network latency for cross zones traffic is.

Azure regions where such an active/active deployment should be possible without large differences in run time and throughput within the application layer deployed across different Availability Zones, list like:

- West US2 (all three zones)
- East US2 (all three zones)
- Central US (all three zones)
- North Europe (all three zones)
- West Europe (two of the three zones)
- East US (two of the three zones)
- South Central US (two of the three zones)
- UK South (two of the three zones)

Azure regions where this SAP deployment architecture across zones is not recommended are:

- France Central 
- South Africa North
- Canada Central
- Japan East

Dependent on what you are willing to tolerate on run time differences other regions not listed could qualify as well.


A simplified schema of an active/active deployment across two zones could look like this:

![Active/Active zone deployment](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

The following considerations apply for this configuration:

- Not using [Azure Proximity Placement Group](../../co-location.md), you treat the Azure Availability Zones as fault and update domains for all the VMs because availability sets can't be deployed in Azure Availability Zones.
- If you want to combine zonal deployments for the DBMS layer and central services, but want to use Azure availability sets for the application layer, you need to use Azure proximity groups as described in the article [Azure Proximity Placement Groups for optimal network latency with SAP applications](sap-proximity-placement-scenarios.md).
- For the load balancers of the failover clusters of SAP Central Services and the DBMS layer, you need to use the [Standard SKU Azure Load Balancer](../../../load-balancer/load-balancer-standard-availability-zones.md). The Basic Load Balancer won't work across zones.
- The Azure virtual network that you deployed to host the SAP system, together with its subnets, is stretched across zones. You don't need separate virtual networks for each zone.
- For all virtual machines you deploy, you need to use [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/). Unmanaged disks aren't supported for zonal deployments.
- Azure Premium Storage, [Ultra SSD storage](../../disks-types.md#ultra-disk), or ANF don't support any type of storage replication across zones. The application (DBMS or SAP Central Services) must replicate important data.
- The same is true for the shared sapmnt directory, which is a shared disk (Windows), a CIFS share (Windows), or an NFS share (Linux). You need to use a technology that replicates these shared disks or shares between the zones. These technologies are supported:
  - For Windows, a cluster solution that uses SIOS DataKeeper, as documented in [Cluster an SAP ASCS/SCS instance on a Windows failover cluster by using a cluster shared disk in Azure](./sap-high-availability-guide-wsfc-shared-disk.md).
  - For SUSE Linux, an NFS share that's built as documented in [High availability for NFS on Azure VMs on SUSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md).
	
    Currently, the solution that uses Microsoft Scale-Out File Server, as documented in [Prepare Azure infrastructure for SAP high availability by using a Windows failover cluster and file share for SAP ASCS/SCS instances](./sap-high-availability-infrastructure-wsfc-file-share.md), is not supported across zones.
- The third zone is used to host the SBD device if you build a [SUSE Linux Pacemaker cluster](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device) and use SBD devices instead of the Azure Fencing Agent. Or for additional application instances.
- To achieve run time consistency for critical business processes, you can try to direct certain batch jobs and users to application instances that are in-zone with the active DBMS instance by using SAP batch server groups, SAP logon groups, or RFC groups. However, in the case of a zonal failover, you would need to manually move these groups to instances running on VMs that are in-zone with the active DB VM.  
- You might want to deploy dormant dialog instances in each of the zones. 

> [!IMPORTANT]
> In this active/active scenario additional charges for bandwidth are announced by Microsoft from 04/01/2020 on. Check the document [Bandwidth Pricing Details](https://azure.microsoft.com/pricing/details/bandwidth/). The data transfer between the SAP application layer and SAP DBMS layer is quite intensive. Therefore the active/active scenario can contribute to costs quite a bit. Keep checking this article to get the exact costs 


## Active/Passive deployment
If you can't find an acceptable delta between the network latency within one zone and the latency of cross-zone network traffic, you can deploy an architecture that has an active/passive character from the SAP application layer point of view. You define an *active* zone, which is the zone where you deploy the complete application layer and where you attempt to run both the active DBMS and the SAP Central Services instance. With such a configuration, you need to make sure you don't have extreme run time variations, depending on whether a job runs in-zone with the active DBMS instance or not, in business transactions and batch jobs.

Azure regions where this type of deployment architecture across different zones may be preferable are:

- Southeast Asia
- Australia East
- Brazil South
- Germany West Central
- South Africa North
- France Central 
- Canada Central
- Japan East


The basic layout of the architecture looks like this:

![Active/Passive zone deployment](./media/sap-ha-availability-zones/active_passive_zones_deployment.png)

The following considerations apply for this configuration:

- Availability sets can't be deployed in Azure Availability Zones. To compensate for that, you can use Azure proximity placement groups as documented in  the article [Azure Proximity Placement Groups for optimal network latency with SAP applications](sap-proximity-placement-scenarios.md).
- When you use this architecture, you need to monitor the status closely and try to keep the active DBMS and SAP Central Services instances in the same zone as your deployed application layer. In case of a failover of SAP Central Service or the DBMS instance, you want to make sure that you can manually fail back into the zone with the SAP application layer deployed as quickly as possible.
- For the load balancers of the failover clusters of SAP Central Services and the DBMS layer, you need to use the [Standard SKU Azure Load Balancer](../../../load-balancer/load-balancer-standard-availability-zones.md). The Basic Load Balancer won't work across zones.
- The Azure virtual network that you deployed to host the SAP system, together with its subnets, is stretched across zones. You don't need separate virtual networks for each zone.
- For all virtual machines you deploy, you need to use [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/). Unmanaged disks aren't supported for zonal deployments.
- Azure Premium Storage, [Ultra SSD storage](../../disks-types.md#ultra-disk), or ANF don't support any type of storage replication across zones. The application (DBMS or SAP Central Services) must replicate important data.
- The same is true for the shared sapmnt directory, which is a shared disk (Windows), a CIFS share (Windows), or an NFS share (Linux). You need to use a technology that replicates these shared disks or shares between the zones. These technologies are supported:
	- For Windows, a cluster solution that uses SIOS DataKeeper, as documented in [Cluster an SAP ASCS/SCS instance on a Windows failover cluster by using a cluster shared disk in Azure](./sap-high-availability-guide-wsfc-shared-disk.md).
	- For SUSE Linux, an NFS share that's built as documented in [High availability for NFS on Azure VMs on SUSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md).
	
  Currently, the solution that uses Microsoft Scale-Out File Server, as documented in [Prepare Azure infrastructure for SAP high availability by using a Windows failover cluster and file share for SAP ASCS/SCS instances](./sap-high-availability-infrastructure-wsfc-file-share.md), is not supported across zones.
- The third zone is used to host the SBD device if you build a [SUSE Linux Pacemaker cluster](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device) and use SBD devices instead of the Azure Fencing Agent. Or for  additional application instances.
- You should deploy dormant VMs in the passive zone (from a DBMS point of view) so you can start application resources for the case of a zone failure.
	- [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) is currently unable to replicate active VMs to dormant VMs between zones. 
- You should invest in automation that allows you to automatically start the SAP application layer in the second zone if a zonal outage occurs.

## Combined high availability and disaster recovery configuration
Microsoft doesn't share any information about geographical distances between the facilities that host different Azure Availability Zones in an Azure region. Still, some customers are using zones for a combined HA and DR configuration that promises a recovery point objective (RPO) of zero. An RPO of zero  means that you shouldn't lose any committed database transactions even in the case of disaster recovery. 

> [!NOTE]
> We recommend that you use a configuration like this only in certain circumstances. For example, you might use it when data can't leave the Azure region for security or compliance reasons. 

Here's one example of how such a configuration might look:

![Combined high-availability DR in zones](./media/sap-ha-availability-zones/combined_ha_dr_in_zones.png)

The following considerations apply for this configuration:

- You're either assuming that there's a significant distance between the facilities hosting an Availability Zone or you're forced to stay within a certain Azure region. Availability sets can't be deployed in Azure Availability Zones. To compensate for that, you can use Azure proximity placement groups as documented in  the article [Azure Proximity Placement Groups for optimal network latency with SAP applications](sap-proximity-placement-scenarios.md).
- When you use this architecture, you need to monitor the status closely and try to keep the active DBMS and SAP Central Services instances in the same zone as your deployed application layer. In case of a failover of SAP Central Service or the DBMS instance, you want to make sure that you can manually fail back into the zone with the SAP application layer deployed as quickly as possible.
- You should have production application instances pre-installed in the VMs that run the active QA application instances.
- In a zonal failure case, shut down the QA application instances and start the production instances instead. You need to use virtual names for the application instances to make this work.
- For the load balancers of the failover clusters of SAP Central Services and the DBMS layer, you need to use the [Standard SKU Azure Load Balancer](../../../load-balancer/load-balancer-standard-availability-zones.md). The Basic Load Balancer won't work across zones.
- The Azure virtual network that you deployed to host the SAP system, together with its subnets, is stretched across zones. You don't need separate virtual networks for each zone.
- For all virtual machines you deploy, you need to use [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/). Unmanaged disks aren't supported for zonal deployments.
- Azure Premium Storage and [Ultra SSD storage](../../disks-types.md#ultra-disk) don't support any type of storage replication across zones. The application (DBMS or SAP Central Services) must replicate important data.
- The same is true for the shared sapmnt directory, which is a shared disk (Windows), a CIFS share (Windows), or an NFS share (Linux). You need to use a technology that replicates these shared disks or shares between the zones. These technologies are supported:
	- For Windows, a cluster solution that uses SIOS DataKeeper, as documented in [Cluster an SAP ASCS/SCS instance on a Windows failover cluster by using a cluster shared disk in Azure](./sap-high-availability-guide-wsfc-shared-disk.md).
	- For SUSE Linux, an NFS share that's built as documented in [High availability for NFS on Azure VMs on SUSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md).

  Currently, the solution that uses Microsoft Scale-Out File Server, as documented in [Prepare Azure infrastructure for SAP high availability by using a Windows failover cluster and file share for SAP ASCS/SCS instances](./sap-high-availability-infrastructure-wsfc-file-share.md), is not supported across zones.
- The third zone is used to host the SBD device if you build a [SUSE Linux Pacemaker cluster](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device) and use SBD devices instead of the Azure Fencing Agent. 





## Next steps
Here are some next steps for deploying across Azure Availability Zones:

- [Cluster an SAP ASCS/SCS instance on a Windows failover cluster by using a cluster shared disk in Azure](./sap-high-availability-guide-wsfc-shared-disk.md)
- [Prepare Azure infrastructure for SAP high availability by using a Windows failover cluster and file share for SAP ASCS/SCS instances](./sap-high-availability-infrastructure-wsfc-file-share.md)