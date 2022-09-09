---
title: Add storage to an Azure HPC Cache (preview)
description: How to define storage targets so that your Azure HPC Cache can use your on-premises NFS system or Azure Blob containers for long-term file storage 
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: rohogue
---

# Add storage targets

*Storage targets* are back-end storage for files that are accessed through an Azure HPC Cache instance. You can add NFS storage (like an on-premises hardware system), or store data in Azure Blob.

You can define up to ten different storage targets for one cache. The cache presents all of the storage targets in one aggregated namespace.

Remember that the storage exports must be accessible from your cache's virtual network. For on-premises hardware storage, you might need to set up a DNS server that can resolve hostnames for NFS storage access. Read more in [DNS access](hpc-cache-prereqs.md#dns-access).

Add storage targets after creating your cache. The procedure is slightly different depending on whether you're adding Azure Blob storage or an NFS export. Details for each are below.

## Open the storage targets page

From the Azure portal, open your cache instance and click **Storage targets** on the left sidebar. The storage targets page lists all existing targets and gives a link to add a new one.

![screenshot of the storage targets link on the sidebar, under the heading Configure, which is between the category headings Settings and Monitoring](media/hpc-cache-storage-targets-sidebar.png)

## Add a new Azure Blob storage target

A new Blob storage target needs an empty Blob container or a container that is populated with data in the Azure HPC Cache cloud file system format. Read more about pre-loading a Blob container in [Move data to Azure Blob storage](hpc-cache-ingest.md).

To define an Azure Blob container, enter this information.

![screenshot of the add storage target page, populated with information for a new Azure Blob storage target](media/hpc-cache-add-blob.png)

<!-- need to replace screenshot after note text is updated with both required RBAC roles and also with correct search term -->

* **Storage target name** - Set a name that identifies this storage target in the Azure HPC Cache.
* **Target type** - Choose **Blob**.
* **Storage account** - Select the account with the container to reference.

  You will need to authorize the cache instance to access the storage account as described in [Add the access roles](#add-the-access-control-roles-to-your-account).

  For information about the kind of storage account you can use, read [Blob storage requirements](hpc-cache-prereqs.md#blob-storage-requirements).

* **Storage container** - Select the Blob container for this target.

* **Virtual namespace path** - Set the client-facing file path for this storage target. Read [Configure aggregated namespace](hpc-cache-namespace.md) to learn more about the virtual namespace feature.

When finished, click **OK** to add the storage target.

### Add the access control roles to your account

Azure HPC Cache uses [role-based access control (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/index) to authorize the cache application to access your storage account for Azure Blob storage targets.

The storage account owner must explicitly add the roles [Storage Account Contributor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-contributor) and [Storage Blob Data Contributor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) for the user "HPC Cache Resource Provider".

You can do this ahead of time, or by clicking a link on the page where you add a Blob storage target.

Steps to add the RBAC roles:

1. Open the **Access control (IAM)** page for the storage account. (The link in the **Add storage target** page automatically opens this page for the selected account.)

1. Click the **+** at the top of the page and choose **Add a role assignment**.

1. Select the role "Storage Account Contributor" from the list.

1. In the **Assign access to** field, leave the default value selected ("Azure AD user, group, or service principal").  

1. In the **Select** field, search for "hpc".  This string should match one service principal, named "HPC Cache Resource Provider". Click that principal to select it.

   > [!NOTE]
   > If a search for "hpc" doesn't work, try using the string "storagecache" instead. Users who joined the preview early might need to use the older name for the service principal.

1. Click the **Save** button to add the role assignment to the storage account.

1. Repeat this process to assign the role "Storage Blob Data Contributor".  

![screenshot of add role assignment GUI](media/hpc-cache-add-role.png)

## Add a new NFS storage target

An NFS storage target has some extra fields to specify how to reach the storage export and how to efficiently cache its data. Also, you can create multiple namespace paths from one NFS host if it has more than one export available.

![Screenshot of add storage target page with NFS target defined](media/hpc-cache-add-nfs-target.png)

Provide this information for an NFS-backed storage target:

* **Storage target name** - Set a name that identifies this storage target in the Azure HPC Cache.

* **Target type** - Choose **NFS**.

* **Hostname** - Enter the IP address or fully qualified domain name for your NFS storage system. (Use a domain name only if your cache has access to a DNS server that can resolve the name.)

* **Usage model** - Choose one of the data caching profiles based on your workflow, described in [Choose a usage model, below](#choose-a-usage-model).

### NFS namespace paths

An NFS storage target can have multiple virtual paths, as long as each path represents a different export or subdirectory on the same storage system.

Create all of the paths from one storage target.
<!-- You can create multiple namespace paths to represent different exports on the same NFS storage system, but you must create them all from one storage target. -->

Fill in these values for each namespace path:

* **Virtual namespace path** - Set the client-facing file path for this storage target. Read [Configure aggregated namespace](hpc-cache-namespace.md) to learn more about the virtual namespace feature.

<!--  The virtual path should start with a slash ``/``. -->

* **NFS export path** - Enter the path to the NFS export.

* **Subdirectory path** - If you want to mount a specific subdirectory of the export, enter it here. If not, leave this field blank.

When finished, click **OK** to add the storage target.

### Choose a usage model
<!-- referenced from GUI - update aka.ms link if you change this heading -->

When you create a storage target that points to an NFS storage system, you need to choose the *usage model* for that target. This model determines how your data is cached.

* Read heavy - If you mostly use the cache to speed up data read access, choose this option.

* Read/write - If clients use the cache to read and write, choose this option.

* Clients bypass the cache - Choose this option if your clients write data directly to the storage system without first writing to the cache.

## Next steps

After creating storage targets, consider one of these tasks:

* [Mount the Azure HPC Cache](hpc-cache-mount.md)
* [Move data to Azure Blob storage](hpc-cache-ingest.md)
