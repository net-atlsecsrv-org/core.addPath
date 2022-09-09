---
title: Upload a vhd to Azure using Azure PowerShell
description: Learn how to upload a vhd to an Azure managed disk and copy a managed disk across regions, using Azure PowerShell.    
author: roygara
ms.author: rogarana
ms.date: 05/06/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.tgt_pltfrm: linux
ms.subservice: disks
---

# Upload a vhd to Azure using Azure PowerShell

This article explains how to upload a vhd from your local machine to an Azure managed disk. Previously, you had to follow a more involved process that included staging your data in a storage account, and managing that storage account. Now, you no longer need to manage a storage account, or stage data in it to upload a vhd. Instead, you create an empty managed disk, and upload a vhd directly to it. This simplifies uploading on-premises VMs to Azure and enables you to upload a vhd up to 32 TiB directly into a large managed disk.

If you are providing a backup solution for IaaS VMs in Azure, we recommend you use direct upload to restore customer backups to managed disks. If you are uploading a VHD from a machine external to Azure, speeds with depend on your local bandwidth. If you are using an Azure VM, then your bandwidth will be the same as standard HDDs.

Currently, direct upload is supported for standard HDD, standard SSD, and premium SSD managed disks. It is not yet supported for ultra SSDs.

## Prerequisites

- Download the latest [version of AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Install Azure PowerShell module](/powershell/azure/install-Az-ps).
- If you intend to upload a vhd from on-pem: A vhd that [has been prepared for Azure](prepare-for-upload-vhd-image.md), stored locally.
- Or, a managed disk in Azure, if you intend to perform a copy action.

## Create an empty managed disk

To upload your vhd to Azure, you'll need to create an empty managed disk that is configured for this upload process. Before you create one, there's some additional information you should know about these disks.

This kind of managed disk has two unique states:

- ReadToUpload, which means the disk is ready to receive an upload but, no [secure access signature](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) (SAS) has been generated.
- ActiveUpload, which means that the disk is ready to receive an upload and the SAS has been generated.

While in either of these states, the managed disk will be billed at [standard HDD pricing](https://azure.microsoft.com/pricing/details/managed-disks/), regardless of the actual type of disk. For example, a P10 will be billed as an S10. This will be true until `revoke-access` is called on the managed disk, which is required in order to attach the disk to a VM.

Before you create an empty standard HDD for uploading, you'll need the file size in bytes of the vhd you want to upload. The example code will get that for you but, to do it yourself you can use: `$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length`. This value is used when specifying the **-UploadSizeInBytes** parameter.

Now, on your local shell, create an empty standard HDD for uploading by specifying the **Upload** setting in the **-CreateOption** parameter as well as the **-UploadSizeInBytes** parameter in the [New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azdiskconfig?view=azps-1.8.0) cmdlet. Then call [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/new-azdisk?view=azps-1.8.0) to create the disk:

```powershell
$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length

$diskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -OsType 'Windows' -UploadSizeInBytes $vhdSizeBytes -Location 'West US' -CreateOption 'Upload'

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName' -Disk $diskconfig
```

If you would like to upload either a premium SSD or a standard SSD, replace **Standard_LRS** with either **Premium_LRS** or **StandardSSD_LRS**. Ultra SSD is not yet supported.

You have now created an empty managed disk that is configured for the upload process. To upload a vhd to the disk, you'll need a writeable SAS, so that you can reference it as the destination for your upload.

To generate a writable SAS of your empty managed disk, use the following command:

```powershell
$diskSas = Grant-AzDiskAccess -ResourceGroupName 'myResouceGroup' -DiskName 'myDiskName' -DurationInSecond 86400 -Access 'Write'

$disk = Get-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## Upload vhd

Now that you have a SAS for your empty managed disk, you can use it to set your managed disk as the destination for your upload command.

Use AzCopy v10 to upload your local VHD file to a managed disk by specifying the SAS URI you generated.

This upload has the same throughput as the equivalent [standard HDD](disks-types.md#standard-hdd). For example, if you have a size that equates to S4, you will have a throughput of up to 60 MiB/s. But, if you have a size that equates to S70, you will have a throughput of up to 500 MiB/s.

```
AzCopy.exe copy "c:\somewhere\mydisk.vhd" $diskSas.AccessSAS --blob-type PageBlob
```

If your SAS expires during the upload, and you haven't called `revoke-access` yet, you can get a new SAS to continue the upload using `grant-access`, again.

After the upload is complete, and you no longer need to write any more data to the disk, revoke the SAS. Revoking the SAS will change the state of the managed disk and allow you to attach the disk to a VM.

```powershell
Revoke-AzDiskAccess -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## Copy a managed disk

Direct upload also simplifies the process of copying a managed disk. You can either copy within the same region or cross-region (to another region).

The follow script will do this for you, the process is similar to the steps described earlier, with some differences since you're working with an existing disk.

> [!IMPORTANT]
> You need to add an offset of 512 when you're providing the disk size in bytes of a managed disk from Azure. This is because Azure omits the footer when returning the disk size. The copy will fail if you do not do this. The following script already does this for you.

Replace the `<sourceResourceGroupHere>`, `<sourceDiskNameHere>`, `<targetDiskNameHere>`, `<targetResourceGroupHere>`, `<yourOSTypeHere>` and `<yourTargetLocationHere>` (an example of a location value would be uswest2) with your values, then run the following script in order to copy a managed disk.

```powershell

$sourceRG = <sourceResourceGroupHere>
$sourceDiskName = <sourceDiskNameHere>
$targetDiskName = <targetDiskNameHere>
$targetRG = <targetResourceGroupHere>
$targetLocate = <yourTargetLocationHere>
#Expected value for OS is either "Windows" or "Linux"
$targetOS = <yourOSTypeHere>

$sourceDisk = Get-AzDisk -ResourceGroupName $sourceRG -DiskName $sourceDiskName

# Adding the sizeInBytes with the 512 offset, and the -Upload flag
$targetDiskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -osType $targetOS -UploadSizeInBytes $($sourceDisk.DiskSizeBytes+512) -Location $targetLocate -CreateOption 'Upload'

$targetDisk = New-AzDisk -ResourceGroupName $targetRG -DiskName $targetDiskName -Disk $targetDiskconfig

$sourceDiskSas = Grant-AzDiskAccess -ResourceGroupName $sourceRG -DiskName $sourceDiskName -DurationInSecond 86400 -Access 'Read'

$targetDiskSas = Grant-AzDiskAccess -ResourceGroupName $targetRG -DiskName $targetDiskName -DurationInSecond 86400 -Access 'Write'

azcopy copy $sourceDiskSas.AccessSAS $targetDiskSas.AccessSAS --blob-type PageBlob

Revoke-AzDiskAccess -ResourceGroupName $sourceRG -DiskName $sourceDiskName

Revoke-AzDiskAccess -ResourceGroupName $targetRG -DiskName $targetDiskName 
```

## Next steps

Now that you've successfully uploaded a vhd to a managed disk, you can attach your disk to a VM and begin using it.

To learn how to attach a disk to a VM, see our article on the subject: [Attach a data disk to a Windows VM with PowerShell](attach-disk-ps.md).
