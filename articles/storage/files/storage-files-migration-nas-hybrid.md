---
title: On-premises NAS migration to Azure File Sync
description: Learn how to migrate files from an on-premises Network Attached Storage (NAS) location to a hybrid cloud deployment with Azure File Sync and Azure file shares.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/19/2020
ms.author: fauhse
ms.subservice: files
---

# Migrate from Network Attached Storage (NAS) to a hybrid cloud deployment with Azure File Sync

Azure File Sync works on Direct Attached Storage (DAS) locations and does not support sync to Network Attached Storage (NAS) locations.
This fact makes a migration of your files necessary and this article guides you through the planning and execution of such a migration.

## Migration goals

The goal is to move the shares that you have on your NAS appliance to a Windows Server. Then utilize Azure File Sync for a hybrid cloud deployment. This migration needs to be done in a way that guarantees the integrity of the production data as well as availability during the migration. The latter requires keeping downtime to a minimum, so that it can fit into or only slightly exceed regular maintenance windows.

## Migration overview

As mentioned in the Azure Files [migration overview article](storage-files-migration-overview.md), using the correct copy tool and approach is important. Your NAS appliance is exposing SMB shares directly on your local network. RoboCopy, built-into Windows Server, is the best way to move your files in this migration scenario.

- Phase 1: [Identify how many Azure file shares you need](#phase-1-identify-how-many-azure-file-shares-you-need)
- Phase 2: [Provision a suitable Windows Server on-premises](#phase-2-provision-a-suitable-windows-server-on-premises)
- Phase 3: [Deploy the Azure File Sync cloud resource](#phase-3-deploy-the-azure-file-sync-cloud-resource)
- Phase 4: [Deploy Azure storage resources](#phase-4-deploy-azure-storage-resources)
- Phase 5: [Deploy the Azure File Sync agent](#phase-5-deploy-the-azure-file-sync-agent)
- Phase 6: [Configure Azure File Sync on the Windows Server](#phase-6-configure-azure-file-sync-on-the-windows-server)
- Phase 7: [RoboCopy](#phase-7-robocopy)
- Phase 8: [User cut-over](#phase-8-user-cut-over)

## Phase 1: Identify how many Azure file shares you need

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## Phase 2: Provision a suitable Windows Server on-premises

* Create a Windows Server 2019 - at a minimum 2012R2 - as a virtual machine or physical server. A Windows Server fail-over cluster is also supported.
* Provision or add Direct Attached Storage (DAS as compared to NAS, which is not supported).

    The amount of storage you provision can be smaller than what you are currently using on your NAS appliance, if you use Azure File Syncs [cloud tiering](storage-sync-cloud-tiering.md) feature.
    However, when you copy your files from the larger NAS space to the smaller Windows Server volume in a later phase, you will need to work in batches:

    1. Move a set of files that fits onto the disk
    2. let file sync and cloud tiering engage
    3. when more free space is created on the volume, proceed with the next batch of files. 
    
    You can avoid this batching approach by provisioning the equivalent space on the Windows Server that your files occupy on the NAS appliance. Consider deduplication on NAS / Windows. If you don't want to permanently commit this high amount of storage to your Windows Server, you can reduce the volume size after the migration and before adjusting the cloud tiering policies. That creates a smaller on-premises cache of your Azure file shares.

The resource configuration (compute and RAM) of the Windows Server you deploy depends mostly on the number of items (files and folders) you will be syncing. We recommend going with a higher performance configuration if you have any concerns.

[Learn how to size a Windows Server based on the number of items (files and folders) you need to sync.](storage-sync-files-planning.md#recommended-system-resources)

> [!NOTE]
> The previously linked article presents a table with a range for server memory (RAM). You can orient towards the smaller number for your server but expect that initial sync can take significantly more time.

## Phase 3: Deploy the Azure File Sync cloud resource

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## Phase 4: Deploy Azure storage resources

In this phase, consult the mapping table from Phase 1 and use it to provision the correct number of Azure storage accounts and file shares within them.

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## Phase 5: Deploy the Azure File Sync agent

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## Phase 6: Configure Azure File Sync on the Windows Server

Your registered on-premises Windows Server must be ready and connected to the internet for this process.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> Cloud tiering is the AFS feature that allows the local server to have less storage capacity than is stored in the cloud, yet have the full namespace available. Locally interesting data is also cached locally for fast access performance. Cloud tiering is an optional feature per Azure File Sync "server endpoint".

> [!WARNING]
> If you provisioned less storage on your Windows server volume(s) than your data used on the NAS appliance, then cloud tiering is mandatory. If you do not turn on cloud tiering, your server will not free up space to store all files. Set your tiering policy, temporarily for the migration, to 99% volume free space. Be sure to return to your cloud tiering settings after the migration is complete, and set it to a more long-term useful level.

Repeat the steps of sync group creation and addition of the matching server folder as a server endpoint for all Azure file shares / server locations, that need to be configured for sync.

After the creation of all server endpoints, sync is working. You can create a test file and see it sync up from your server location to the connected Azure file share (as described by the cloud endpoint in the sync group).

Both locations, the server folders and the Azure file shares are otherwise empty and awaiting data in either location. In the next step, you will begin to copy files into the Windows Server for Azure File Sync to move them up to the cloud. In case you've enabled cloud tiering, the server will then begin to tier files, should you run out of capacity on the local volume(s).

## Phase 7: RoboCopy

The basic migration approach is a RoboCopy from your NAS appliance to your Windows Server, and Azure File Sync to Azure file shares.

Run the first local copy to your Windows Server target folder:

* Identify the first location on your NAS appliance.
* Identify the matching folder on the Windows Server, that already has Azure File Sync configured on it.
* Start the copy using RoboCopy

The following RoboCopy command will copy files from your NAS storage to your Windows Server target folder. The Windows Server will sync it to the Azure file share(s). 

If you provisioned less storage on your Windows Server than your files take up on the NAS appliance, then you have configured cloud tiering. As the local Windows Server volume gets full, [cloud tiering](storage-sync-cloud-tiering.md) will kick in and tier files that have successfully synced already. Cloud tiering will generate enough space to continue the copy from the NAS appliance. Cloud tiering checks once an hour to see what has synced and to free up disk space to reach the 99% volume free space.
It is possible, that RoboCopy moves files faster than you can sync to the cloud and tier locally, thus running out of local disk space. RoboCopy will fail. It is recommended that you work through the shares in a sequence that prevents that. For example, not starting RoboCopy jobs for all shares at the same time, or only moving shares that fit on the current amount of free space on the Windows Server, to mention a few.

```console
Robocopy /MT:32 /UNILOG:<file name> /TEE /B /MIR /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

Background:

:::row:::
   :::column span="1":::
      /MT
   :::column-end:::
   :::column span="1":::
      Allows for RoboCopy to run multi-threaded. Default is 8, max is 128.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /UNILOG:\<file name\>
   :::column-end:::
   :::column span="1":::
      Outputs status to LOG file as UNICODE (overwrites existing log).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /TEE
   :::column-end:::
   :::column span="1":::
      Outputs to console window. Used in conjunction with output to a log file.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /B
   :::column-end:::
   :::column span="1":::
      Runs RoboCopy in the same mode a backup application would use. It allows RoboCopy to move files that the current user does not have permissions to.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /MIR
   :::column-end:::
   :::column span="1":::
      Allows to run this RoboCopy command several times, sequentially on the same target / destination. It identifies what has been copied before and omits it. Only changes, additions and "*deletes*" will be processed, that occurred since the last run. If the command wasn't run before, nothing is omitted. The */MIR* flag is an excellent option for source locations that are still actively used and changing.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY:copyflag[s]
   :::column-end:::
   :::column span="1":::
      fidelity of the file copy (default is /COPY:DAT), copy flags: D=Data, A=Attributes, T=Timestamps, S=Security=NTFS ACLs, O=Owner info, U=aUditing info
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPYALL
   :::column-end:::
   :::column span="1":::
      COPY ALL file info (equivalent to /COPY:DATSOU)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY:copyflag[s]
   :::column-end:::
   :::column span="1":::
      fidelity for the copy of directories (default is /DCOPY:DA), copy flags: D=Data, A=Attributes, T=Timestamps
   :::column-end:::
:::row-end:::

## Phase 8: User cut-over

When you run the RoboCopy command for the first time, your users and applications are still accessing files on the NAS and potentially change them. It is possible, that RoboCopy has processed a directory, moves on to the next and then a user on the source location (NAS) adds, changes, or deletes a file that will now not be processed in this current RoboCopy run. This behavior is expected.

The first run is about moving the bulk of the data to your Windows Server and into the cloud via Azure File Sync. This first copy can take a long time, depending on:

* your download bandwidth
* the upload bandwidth
* the local network speed and number of how optimally the number of RoboCopy threads matches it
* the number of items (files and folders), that need to be processed by RoboCopy and Azure File Sync

Once the initial run is complete, run the command again.

The second time it will finish faster, because it only needs to transport changes that happened since the last run. During this second run, still, new changes can accumulate.

Repeat this process until you are satisfied that the amount of time it takes to complete a RoboCopy for a specific location is within an acceptable window for downtime.

When you consider the downtime acceptable and you are prepared to take the NAS location offline: In order to take user access offline, you have the option to change ACLs on the share root such that users can no longer access the location or take any other appropriate step that prevents content to change in this folder on your NAS.

Run one last RoboCopy round. It will pick up any changes, that might have been missed.
How long this final step takes, is dependent on the speed of the RoboCopy scan. You can estimate the time (which is equal to your downtime) by measuring how long the previous run took.

Create a share on the Windows Server folder and possibly adjust your DFS-N deployment to point to it. Be sure to set the same share-level permissions as on your NAS SMB share. If you had an enterprise-class domain-joined NAS, then the user SIDs will automatically match as the users exist in Active Directory and RoboCopy copies files and metadata at full fidelity. If you have used local users on your NAS, you need to re-create these users as Windows Server local users and map the existing SIDs RoboCopy moved over to your Windows Server to the SIDs of your new, Windows Server local users.

You have finished migrating a share / group of shares into a common root or volume. (Depending on your mapping from Phase 1)

You can try to run a few of these copies in parallel. We recommend processing the scope of one Azure file share at a time.

> [!WARNING]
> Once you have moved all the data from you NAS to the Windows Server, and your migration is complete: Return to ***all***  sync groups in the Azure portal and adjust the cloud tiering volume free space percent value to something better suited for cache utilization, say 20%. 

The cloud tiering volume free space policy acts on a volume level with potentially multiple server endpoints syncing from it. If you forget to adjust the free space on even one server endpoint, sync will continue to apply the most restrictive rule and attempt to keep 99% free disk space, making the local cache not performing as you might expect. Unless it is your goal to only have the namespace for a volume that only contains rarely accessed, archival data and you are reserving the rest of the storage space for another scenario.

## Troubleshoot

The most likely issue you can run into, is that the RoboCopy command fails with *"Volume full"* on the Windows Server side. Cloud tiering acts once every hour to evacuate content from the local Windows Server disk, that has synced. Its goal is to reach your 99% free space on the volume.

Let sync progress and cloud tiering free up disk space. You can observe that in File Explorer on your Windows Server.

When your Windows Server has sufficient available capacity, rerunning the command will resolve the problem. Nothing breaks when you get into this situation and you can move forward with confidence. Inconvenience of running the command again is the only consequence.

Check the link in the following section for troubleshooting Azure File Sync issues.

## Next steps

There is more to discover about Azure file shares and Azure File Sync. The following articles help understand advanced options, best practices and also contain troubleshooting help. These articles link to [Azure file share documentation](storage-files-introduction.md) as appropriate.

* [AFS overview](./storage-sync-files-planning.md)
* [AFS deployment guide](storage-files-deployment-guide.md)
* [AFS troubleshooting](storage-sync-files-troubleshoot.md)