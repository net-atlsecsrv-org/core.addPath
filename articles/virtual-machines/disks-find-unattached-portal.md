---
title: Identify unattached Azure disks - Azure portal
description: How to find unattached Azure managed and unmanaged (VHDs/page blobs) disks by using the Azure portal.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 06/01/2020
ms.author: rogarana
ms.subservice: disks
---

# Find and delete unattached Azure managed and unmanaged disks - Azure portal

When you delete a virtual machine (VM) in Azure, by default, any disks that are attached to the VM aren't deleted. This helps to prevent data loss due to the unintentional deletion of VMs. After a VM is deleted, you will continue to pay for unattached disks. This article shows you how to find and delete any unattached disks using the Azure portal, and reduce unnecessary costs.

## Managed disks: Find and delete unattached disks

If you have unattached managed disks and no longer need the data on them, the following process explains how to find them from the Azure portal:

1. Sign in to the [Azure portal](https://portal.azure.com/).
1. Search for and select **Disks**.

    On the **Disks** blade, you are presented with a list of all your disks. Any disk that has "**-**" in the **Owner** column is an unattached disk.

    [![](media/disks-find-unattached-portal/managed-disk-unattached-owner.png "Screenshot of the managed disks blade, if a disk has - in the Owner column, it is an unattached disk")](media/disks-find-unattached-portal/managed-disk-owner-unattached.png#lightbox)

1. Select the unattached disk you'd like to delete, this opens the disk's blade.
1. On the disk's blade, you can confirm the disk state is unattached, then select **Delete**.

    :::image type="content" source="media/disks-find-unattached-portal/delete-managed-disk-unattached.png" alt-text="Screenshot of an individual managed disks blade. This blade will show unattached in the disk state if it is unattached. You can delete this disk if you do not need to preserve its data any longer":::

## Unmanaged disks: Find and delete unattached disks

Unmanaged disks are VHD files that are stored as [page blobs](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) in [Azure storage accounts](../storage/common/storage-account-overview.md).

If you have unmanaged disks that aren't attached to a VM, no longer need the data on them, and would like to delete them, the following process explains how to do so from the Azure portal:

1. Sign in to the [Azure portal](https://portal.azure.com/).
1. Search for and select **Disks (Classic)**.

    You are presented with a list of all your unmanaged disks. Any disk that has "**-**" in the **Attached to** column is an unattached disk.

    :::image type="content" source="media/disks-find-unattached-portal/unmanaged-disk-unattached-attached-to.png" alt-text="Screenshot of the unmanaged disks blade. Disks in this blade that have - in the attached to column are unattached.":::

1. Select the unattached disk you'd like to delete, this brings up the disk's blade.

1. On the disk's blade, you can confirm it is unattached, since **Attached to** will still be **-**.

    :::image type="content" source="media/disks-find-unattached-portal/unmanaged-disk-unattached-select-blade.png" alt-text="Screenshot of an individual unmanaged disk blade. It will have - as the attached to value if it is unattached. If you no longer need this disks data, you can delete it.":::

1. Select **Delete**.

    :::image type="content" source="media/disks-find-unattached-portal/delete-unmanaged-disk-unattached.png" alt-text="Screenshot of an individual unmanaged disk blade, highlighting delete.":::

## Next steps

If you'd like an automated way of finding and deleting unattached storage accounts, see our [CLI](linux/find-unattached-disks.md) or [PowerShell](windows/find-unattached-disks.md) articles.

For more information, see [Delete a storage account](../storage/common/storage-account-create.md#delete-a-storage-account) and [Identify Orphaned Disks Using PowerShell](https://blogs.technet.microsoft.com/ukplatforms/2018/02/21/azure-cost-optimisation-series-identify-orphaned-disks-using-powershell/)