---
title: Planning for an Azure File Sync deployment | Microsoft Docs
description: Learn what to consider when planning for an Azure Files deployment.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 2/7/2019
ms.author: rogarana
ms.subservice: files
---

# Planning for an Azure File Sync deployment
Use Azure File Sync to centralize your organization's file shares in Azure Files, while keeping the flexibility, performance, and compatibility of an on-premises file server. Azure File Sync transforms Windows Server into a quick cache of your Azure file share. You can use any protocol that's available on Windows Server to access your data locally, including SMB, NFS, and FTPS. You can have as many caches as you need across the world.

This article describes important considerations for an Azure File Sync deployment. We recommend that you also read [Planning for an Azure Files deployment](storage-files-planning.md). 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## Azure File Sync terminology
Before getting into the details of planning for an Azure File Sync deployment, it's important to understand the terminology.

### Storage Sync Service
The Storage Sync Service is the top-level Azure resource for Azure File Sync. The Storage Sync Service resource is a peer of the storage account resource, and can similarly be deployed to Azure resource groups. A distinct top-level resource from the storage account resource is required because the Storage Sync Service can create sync relationships with multiple storage accounts via multiple sync groups. A subscription can have multiple Storage Sync Service resources deployed.

### Sync group
A sync group defines the sync topology for a set of files. Endpoints within a sync group are kept in sync with each other. If, for example, you have two distinct sets of files that you want to manage with Azure File Sync, you would create two sync groups and add different endpoints to each sync group. A Storage Sync Service can host as many sync groups as you need.  

### Registered server
The registered server object represents a trust relationship between your server (or cluster) and the Storage Sync Service. You can register as many servers to a Storage Sync Service instance as you want. However, a server (or cluster) can be registered with only one Storage Sync Service at a time.

### Azure File Sync agent
The Azure File Sync agent is a downloadable package that enables Windows Server to be synced with an Azure file share. The Azure File Sync agent has three main components: 
- **FileSyncSvc.exe**: The background Windows service that is responsible for monitoring changes on server endpoints, and for initiating sync sessions to Azure.
- **StorageSync.sys**: The Azure File Sync file system filter, which is responsible for tiering files to Azure Files (when cloud tiering is enabled).
- **PowerShell management cmdlets**: PowerShell cmdlets that you use to interact with the Microsoft.StorageSync Azure resource provider. You can find these at the following (default) locations:
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll

### Server endpoint
A server endpoint represents a specific location on a registered server, such as a folder on a server volume. Multiple server endpoints can exist on the same volume if their namespaces do not overlap (for example, `F:\sync1` and `F:\sync2`). You can configure cloud tiering policies individually for each server endpoint. 

You can create a server endpoint via a mountpoint. Note, mountpoints within the server endpoint are skipped.  

You can create a server endpoint on the system volume but, there are two limitations if you do so:
* Cloud tiering cannot be enabled.
* Rapid namespace restore (where the system quickly brings down the entire namespace and then starts to recall content) is not performed.


> [!Note]  
> Only non-removable volumes are supported.  Drives mapped from a remote share are not supported for a server endpoint path.  In addition, a server endpoint may be located on the Windows system volume though cloud tiering is not supported on the system volume.

If you add a server location that has an existing set of files as a server endpoint to a sync group, those files are merged with any other files that are already on other endpoints in the sync group.

### Cloud endpoint
A cloud endpoint is an Azure file share that is part of a sync group. The entire Azure file share syncs, and an Azure file share can be a member of only one cloud endpoint. Therefore, an Azure file share can be a member of only one sync group. If you add an Azure file share that has an existing set of files as a cloud endpoint to a sync group, the existing files are merged with any other files that are already on other endpoints in the sync group.

> [!Important]  
> Azure File Sync supports making changes to the Azure file share directly. However, any changes made on the Azure file share first need to be discovered by an Azure File Sync change detection job. A change detection job is initiated for a cloud endpoint only once every 24 hours. In addition, changes made to an Azure file share over the REST protocol will not update the SMB last modified time and will not be seen as a change by sync. For more information, see [Azure Files frequently asked questions](storage-files-faq.md#afs-change-detection).

### Cloud tiering 
Cloud tiering is an optional feature of Azure File Sync in which frequently accessed files are cached locally on the server while all other files are tiered to Azure Files based on policy settings. For more information, see [Understanding Cloud Tiering](storage-sync-cloud-tiering.md).

## Azure File Sync system requirements and interoperability 
This section covers Azure File Sync agent system requirements and interoperability with Windows Server features and roles and third-party solutions.

### Evaluation cmdlet
Before deploying Azure File Sync, you should evaluate whether it is compatible with your system using the Azure File Sync evaluation cmdlet. This cmdlet checks for potential issues with your file system and dataset, such as unsupported characters or an unsupported OS version. Note that its checks cover most but not all of the features mentioned below; we recommend you read through the rest of this section carefully to ensure your deployment goes smoothly. 

The evaluation cmdlet can be installed by installing the Az PowerShell module, which can be installed by following the instructions here: [Install and configure Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).

#### Usage  
You can invoke the evaluation tool in a few different ways: you can perform the system checks, the dataset checks, or both. To perform both the system and dataset checks: 

```powershell
    Invoke-AzStorageSyncCompatibilityCheck -Path <path>
```

To test only your dataset:
```powershell
    Invoke-AzStorageSyncCompatibilityCheck -Path <path> -SkipSystemChecks
```
 
To test system requirements only:
```powershell
    Invoke-AzStorageSyncCompatibilityCheck -ComputerName <computer name>
```
 
To display the results in CSV:
```powershell
    $errors = Invoke-AzStorageSyncCompatibilityCheck […]
    $errors | Select-Object -Property Type, Path, Level, Description | Export-Csv -Path <csv path>
```

### System Requirements
- A server running Windows Server 2012 R2, Windows Server 2016 or Windows Server 2019:

    | Version | Supported SKUs | Supported deployment options |
    |---------|----------------|------------------------------|
    | Windows Server 2019 | Datacenter and Standard | Full and Core |
    | Windows Server 2016 | Datacenter and Standard | Full and Core |
    | Windows Server 2012 R2 | Datacenter and Standard | Full and Core |

    Future versions of Windows Server will be added as they are released.

    > [!Important]  
    > We recommend keeping all servers that you use with Azure File Sync up to date with the latest updates from Windows Update. 

- A server with a minimum of 2 GiB of memory.

    > [!Important]  
    > If the server is running in a virtual machine with dynamic memory enabled, the VM should be configured with a minimum 2048 MiB of memory.
    
- A locally attached volume formatted with the NTFS file system.

### File system features

| Feature | Support status | Notes |
|---------|----------------|-------|
| Access control lists (ACLs) | Fully supported | Windows ACLs are preserved by Azure File Sync, and are enforced by Windows Server on server endpoints. Windows ACLs are not (yet) supported by Azure Files if files are accessed directly in the cloud. |
| Hard links | Skipped | |
| Symbolic links | Skipped | |
| Mount points | Partially supported | Mount points might be the root of a server endpoint, but they are skipped if they are contained in a server endpoint's namespace. |
| Junctions | Skipped | For example, Distributed File System DfrsrPrivate and DFSRoots folders. |
| Reparse points | Skipped | |
| NTFS compression | Fully supported | |
| Sparse files | Fully supported | Sparse files sync (are not blocked), but they sync to the cloud as a full file. If the file contents change in the cloud (or on another server), the file is no longer sparse when the change is downloaded. |
| Alternate Data Streams (ADS) | Preserved, but not synced | For example, classification tags created by the File Classification Infrastructure are not synced. Existing classification tags on files on each of the server endpoints are left untouched. |

> [!Note]  
> Only NTFS volumes are supported. ReFS, FAT, FAT32, and other file systems are not supported.

### Files skipped

| File/folder | Note |
|-|-|
| Desktop.ini | File specific to system |
| ethumbs.db$ | Temporary file for thumbnails |
| ~$\*.\* | Office temporary file |
| \*.tmp | Temporary file |
| \*.laccdb | Access DB locking file|
| 635D02A9D91C401B97884B82B3BCDAEA.* | Internal Sync file|
| \\System Volume Information | Folder specific to volume |
| $RECYCLE.BIN| Folder |
| \\SyncShareState | Folder for Sync |

### Failover Clustering
Windows Server Failover Clustering is supported by Azure File Sync for the "File Server for general use" deployment option. Failover Clustering is not supported on "Scale-Out File Server for application data" (SOFS) or on Clustered Shared Volumes (CSVs).

> [!Note]  
> The Azure File Sync agent must be installed on every node in a Failover Cluster for sync to work correctly.

### Data Deduplication
**Agent version 5.0.2.0 or newer**   
Data Deduplication is supported on volumes with cloud tiering enabled on Windows Server 2016 and Windows Server 2019. Enabling Data Deduplication on a volume with cloud tiering enabled lets you cache more files on-premises without provisioning more storage. 

When Data Deduplication is enabled on a volume with cloud tiering enabled, Dedup optimized files within the server endpoint location will be tiered similar to a normal file based on the cloud tiering policy settings. Once the Dedup optimized files have been tiered, the Data Deduplication garbage collection job will run automatically to reclaim disk space by removing unnecessary chunks that are no longer referenced by other files on the volume.

Note the volume savings only apply to the server; your data in the Azure file share will not be deduped.

**Windows Server 2012 R2 or older agent versions**  
For volumes that don't have cloud tiering enabled, Azure File Sync supports Windows Server Data Deduplication being enabled on the volume.

**Notes**
- If Data Deduplication is installed prior to installing the Azure File Sync agent, a restart is required to support Data Deduplication and cloud tiering on the same volume.
- If Data Deduplication is enabled on a volume after cloud tiering is enabled, the initial Deduplication optimization job will optimize files on the volume which are not already tiered and will have the following impact on cloud tiering:
    - Free space policy will continue to tier files as per the free space on the volume by using the heatmap.
    - Date policy will skip tiering of files that may have been otherwise eligible for tiering due to the Deduplication optimization job accessing the files.
- For ongoing Deduplication optimization jobs, cloud tiering with date policy will get delayed by the Data Deduplication [MinimumFileAgeDays](https://docs.microsoft.com/powershell/module/deduplication/set-dedupvolume?view=win10-ps) setting, if the file is not already tiered. 
    - Example: If the MinimumFileAgeDays setting is 7 days and cloud tiering date policy is 30 days, the date policy will tier files after 37 days.
    - Note: Once a file is tiered by Azure File Sync, the Deduplication optimization job will skip the file.
- If a server running Windows Server 2012 R2 with the Azure File Sync agent installed is upgraded to Windows Server 2016 or Windows Server 2019, the following steps must be performed to support Data Deduplication and cloud tiering on the same volume:  
    - Uninstall the Azure File Sync agent for Windows Server 2012 R2 and restart the server.
    - Download the Azure File Sync agent for the new server OS version (Windows Server 2016 or Windows Server 2019).
    - Install the Azure File Sync agent and restart the server.  
    
    Note: The Azure File Sync configuration settings on the server are retained when the agent is uninstalled and reinstalled.

### Distributed File System (DFS)
Azure File Sync supports interop with DFS Namespaces (DFS-N) and DFS Replication (DFS-R).

**DFS Namespaces (DFS-N)**: Azure File Sync is fully supported on DFS-N servers. You can install the Azure File Sync agent on one or more DFS-N members to sync data between the server endpoints and the cloud endpoint. For more information, see [DFS Namespaces overview](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview).
 
**DFS Replication (DFS-R)**: Since DFS-R and Azure File Sync are both replication solutions, in most cases, we recommend replacing DFS-R with Azure File Sync. There are however several scenarios where you would want to use DFS-R and Azure File Sync together:

- You are migrating from a DFS-R deployment to an Azure File Sync deployment. For more information, see [Migrate a DFS Replication (DFS-R) deployment to Azure File Sync](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync).
- Not every on-premises server which needs a copy of your file data can be connected directly to the internet.
- Branch servers consolidate data onto a single hub server, for which you would like to use Azure File Sync.

For Azure File Sync and DFS-R to work side-by-side:

1. Azure File Sync cloud tiering must be disabled on volumes with DFS-R replicated folders.
2. Server endpoints should not be configured on DFS-R read-only replication folders.

For more information, see [DFS Replication overview](https://technet.microsoft.com/library/jj127250).

### Sysprep
Using sysprep on a server which has the Azure File Sync agent installed is not supported and can lead to unexpected results. Agent installation and server registration should occur after deploying the server image and completing sysprep mini-setup.

### Windows Search
If cloud tiering is enabled on a server endpoint, files that are tiered are skipped and not indexed by Windows Search. Non-tiered files are indexed properly.

### Antivirus solutions
Because antivirus works by scanning files for known malicious code, an antivirus product might cause the recall of tiered files. In versions 4.0 and above of the Azure File Sync agent, tiered files have the secure Windows attribute FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS set. We recommend consulting with your software vendor to learn how to configure their solution to skip reading files with this attribute set (many do it automatically). 

Microsoft's in-house antivirus solutions, Windows Defender and System Center Endpoint Protection (SCEP), both automatically skip reading files that have this attribute set. We have tested them and identified one minor issue: when you add a server to an existing sync group, files smaller than 800 bytes are recalled (downloaded) on the new server. These files will remain on the new server and will not be tiered since they do not meet the tiering size requirement (>64kb).

> [!Note]  
> Antivirus vendors can check compatibility between their product and Azure File Sync using the [Azure File Sync Antivirus Compatibility Test Suite](https://www.microsoft.com/download/details.aspx?id=58322), which is available for download on the Microsoft Download Center.

### Backup solutions
Like antivirus solutions, backup solutions might cause the recall of tiered files. We recommend using a cloud backup solution to back up the Azure file share instead of an on-premises backup product.

If you are using an on-premises backup solution, backups should be performed on a server in the sync group which has cloud tiering disabled. When performing a restore, use the volume-level or file-level restore options. Files restored using the file-level restore option will be synced to all endpoints in the sync group and existing files will be replaced with the version restored from backup.  Volume-level restores will not replace newer file versions in the Azure file share or other server endpoints.

> [!Note]  
> Bare-metal (BMR) restore can cause unexpected results and is not currently supported.

> [!Note]  
> VSS snapshots (including Previous Versions tab) are not currently supported on volumes which have cloud tiering enabled. If cloud tiering is enabled, use the Azure file share snapshots to restore a file from backup.

### Encryption solutions
Support for encryption solutions depends on how they are implemented. Azure File Sync is known to work with:

- BitLocker encryption
- Azure Information Protection, Azure Rights Management Services (Azure RMS), and Active Directory RMS

Azure File Sync is known not to work with:

- NTFS Encrypted File System (EFS)

In general, Azure File Sync should support interoperability with encryption solutions that sit below the file system, such as BitLocker, and with solutions that are implemented in the file format, such as Azure Information Protection. No special interoperability has been made for solutions that sit above the file system (like NTFS EFS).

### Other Hierarchical Storage Management (HSM) solutions
No other HSM solutions should be used with Azure File Sync.

## Region availability
Azure File Sync is available only in the following regions:

| Region | Datacenter location |
|--------|---------------------|
| Australia East | New South Wales |
| Australia Southeast | Victoria |
| Brazil South | Sao Paolo State |
| Canada Central | Toronto |
| Canada East | Quebec City |
| Central India | Pune |
| Central US | Iowa |
| East Asia | Hong Kong SAR |
| East US | Virginia |
| East US2 | Virginia |
| France Central | Paris |
| Korea Central| Seoul |
| Korea South| Busan |
| Japan East | Tokyo, Saitama |
| Japan West | Osaka |
| North Central US | Illinois |
| North Europe | Ireland |
| South Central US | Texas |
| South India | Chennai |
| Southeast Asia | Singapore |
| UK South | London |
| UK West | Cardiff |
| US Gov Arizona | Arizona |
| US Gov Texas | Texas |
| US Gov Virginia | Virginia |
| West Europe | Netherlands |
| West Central US | Wyoming |
| West US | California |
| West US 2 | Washington |

Azure File Sync supports syncing only with an Azure file share that's in the same region as the Storage Sync Service.

### Azure disaster recovery
To protect against the loss of an Azure region, Azure File Sync integrates with the [geo-redundant storage redundancy](../common/storage-redundancy-grs.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) (GRS) option. GRS storage works by using asynchronous block replication between storage in the primary region, with which you normally interact, and storage in the paired secondary region. In the event of a disaster which causes an Azure region to go temporarily or permanently offline, Microsoft will failover storage to the paired region. 

> [!Warning]  
> If you are using your Azure file share as a cloud endpoint in a GRS storage account, you shouldn't initiate storage account failover. Doing so will cause sync to stop working and may also cause unexpected data loss in the case of newly tiered files. In the case of loss of an Azure region, Microsoft will trigger the storage account failover in a way that is compatible with Azure File Sync.

To support the failover integration between geo-redundant storage and Azure File Sync, all Azure File Sync regions are paired with a secondary region that matches the secondary region used by storage. These pairs are as follows:

| Primary region      | Paired region      |
|---------------------|--------------------|
| Australia East      | Australia Southeast|
| Australia Southeast | Australia East     |
| Brazil South        | South Central US   |
| Canada Central      | Canada East        |
| Canada East         | Canada Central     |
| Central India       | South India        |
| Central US          | East US 2          |
| East Asia           | Southeast Asia     |
| East US             | West US            |
| East US 2           | Central US         |
| France Central      | France South       |
| Japan East          | Japan West         |
| Japan West          | Japan East         |
| Korea Central       | Korea South        |
| Korea South         | Korea Central      |
| North Europe        | West Europe        |
| North Central US    | South Central US   |
| South Central US    | North Central US   |
| South India         | Central India      |
| Southeast Asia      | East Asia          |
| UK South            | UK West            |
| UK West             | UK South           |
| US Gov Arizona      | US Gov Texas       |
| US Gov Iowa         | US Gov Virginia    |
| US Gov Virginia      | US Gov Texas       |
| West Europe         | North Europe       |
| West Central US     | West US 2          |
| West US             | East US            |
| West US 2           | West Central US    |

## Azure File Sync agent update policy
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## Next steps
* [Consider firewall and proxy settings](storage-sync-files-firewall-and-proxy.md)
* [Planning for an Azure Files deployment](storage-files-planning.md)
* [Deploy Azure Files](storage-files-deployment-guide.md)
* [Deploy Azure File Sync](storage-sync-files-deployment-guide.md)
* [Monitor Azure File Sync](storage-sync-files-monitoring.md)
