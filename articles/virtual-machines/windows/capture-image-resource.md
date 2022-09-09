---
title: Create a managed image in Azure 
description: Create a managed image of a generalized VM or VHD in Azure. Images can be used to create multiple VMs that use managed disks. 
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/27/2018
ms.author: cynthn
ms.custom: legacy

---
# Create a managed image of a generalized VM in Azure

A managed image resource can be created from a generalized virtual machine (VM) that is stored as either a managed disk or an unmanaged disk in a storage account. The image can then be used to create multiple VMs. For information on how managed images are billed, see [Managed Disks pricing](https://azure.microsoft.com/pricing/details/managed-disks/). 

One managed image supports up to 20 simultaneous deployments. Attempting to create more than 20 VMs concurrently, from the same managed image, may result in provisioning timeouts due to the storage performance limitations of a single VHD. To create more than 20 VMs concurrently, use a [Shared Image Galleries](shared-image-galleries.md) image configured with 1 replica for every 20 concurrent VM deployments.

## Generalize the Windows VM using Sysprep

Sysprep removes all your personal account and security information, and then prepares the machine to be used as an image. For information about Sysprep, see [Sysprep overview](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Make sure the server roles running on the machine are supported by Sysprep. For more information, see [Sysprep support for server roles](/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles) and [Unsupported scenarios](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview#unsupported-scenarios). 

> [!IMPORTANT]
> After you have run Sysprep on a VM, that VM is considered *generalized* and cannot be restarted. The process of generalizing a VM is not reversible. If you need to keep the original VM functioning, you should create a [copy of the VM](create-vm-specialized.md#option-3-copy-an-existing-azure-vm) and generalize its copy. 
>
>Sysprep requires the drives to be fully decrypted. If you have enabled encryption on your VM, disable encryption before you run Sysprep.
>
> If you plan to run Sysprep before uploading your virtual hard disk (VHD) to Azure for the first time, make sure you have [prepared your VM](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).  
> 
> 

To generalize your Windows VM, follow these steps:

1. Sign in to your Windows VM.
   
2. Open a Command Prompt window as an administrator. 

3. Delete the panther directory (C:\Windows\Panther). Then change the directory to %windir%\system32\sysprep, and then run `sysprep.exe`.
   
4. In the **System Preparation Tool** dialog box, select **Enter System Out-of-Box Experience (OOBE)** and select the **Generalize** check box.
   
5. For **Shutdown Options**, select **Shutdown**.
   
6. Select **OK**.
   
    ![Start Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)

6. When Sysprep completes, it shuts down the VM. Do not restart the VM.

> [!TIP]
> **Optional** Use [DISM](/windows-hardware/manufacture/desktop/dism-optimize-image-command-line-options) to optimize your image and reduce your VM's first boot time.
>
> To optimize your image, mount your VHD by double-clicking on it in Windows explorer, and then run DISM with the `/optimize-image` parameter.
>
> ```cmd
> DISM /image:D:\ /optimize-image /boot
> ```
> Where D: is the mounted VHD's path.
>
> Running `DISM /optimize-image` should be the last modification you make to your VHD. If you make any changes to your VHD prior to deployment, you'll have to run `DISM /optimize-image` again.

## Create a managed image in the portal 

1. Go to the [Azure portal](https://portal.azure.com) to manage the VM image. Search for and select **Virtual machines**.

2. Select your VM from the list.

3. In the **Virtual machine** page for the VM, on the upper menu, select **Capture**.

   The **Create image** page appears.

4. For **Name**, either accept the pre-populated name or enter a name that you would like to use for the image.

5. For **Resource group**, either select **Create new** and enter a name, or select a resource group to use from the drop-down list.

6. If you want to delete the source VM after the image has been created, select **Automatically delete this virtual machine after creating the image**.

7. If you want the ability to use the image in any [availability zone](../../availability-zones/az-overview.md), select **On** for **Zone resiliency**.

8. Select **Create** to create the image.

After the image is created, you can find it as an **Image** resource in the list of resources in the resource group.



## Create an image of a VM using PowerShell

 

Creating an image directly from the VM ensures that the image includes all of the disks associated with the VM, including the OS disk and any data disks. This example shows how to create a managed image from a VM that uses managed disks.

Before you begin, make sure that you have the latest version of the Azure PowerShell module. To find the version, run `Get-Module -ListAvailable Az` in PowerShell. If you need to upgrade, see [Install Azure PowerShell on Windows with PowerShellGet](/powershell/azure/install-az-ps). If you are running PowerShell locally, run `Connect-AzAccount` to create a connection with Azure.


> [!NOTE]
> If you would like to store your image in zone-redundant storage, you need to create it in a region that supports [availability zones](../../availability-zones/az-overview.md) and include the `-ZoneResilient` parameter in the image configuration (`New-AzImageConfig` command).

To create a VM image, follow these steps:

1. Create some variables.

    ```azurepowershell-interactive
	$vmName = "myVM"
	$rgName = "myResourceGroup"
	$location = "EastUS"
	$imageName = "myImage"
	```
2. Make sure the VM has been deallocated.

    ```azurepowershell-interactive
	Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
	```
	
3. Set the status of the virtual machine to **Generalized**. 
   
    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized
	```
	
4. Get the virtual machine. 

    ```azurepowershell-interactive
	$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName
	```

5. Create the image configuration.

    ```azurepowershell-interactive
	$image = New-AzImageConfig -Location $location -SourceVirtualMachineId $vm.Id 
	```
6. Create the image.

    ```azurepowershell-interactive
    New-AzImage -Image $image -ImageName $imageName -ResourceGroupName $rgName
    ```	

## Create an image from a managed disk using PowerShell

If you want to create an image of only the OS disk, specify the managed disk ID as the OS disk:

	
1. Create some variables. 

    ```azurepowershell-interactive
	$vmName = "myVM"
	$rgName = "myResourceGroup"
	$location = "EastUS"
	$imageName = "myImage"
	```

2. Get the VM.

   ```azurepowershell-interactive
   $vm = Get-AzVm -Name $vmName -ResourceGroupName $rgName
   ```

3. Get the ID of the managed disk.

    ```azurepowershell-interactive
	$diskID = $vm.StorageProfile.OsDisk.ManagedDisk.Id
	```
   
3. Create the image configuration.

    ```azurepowershell-interactive
	$imageConfig = New-AzImageConfig -Location $location
	$imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -ManagedDiskId $diskID
	```
	
4. Create the image.

    ```azurepowershell-interactive
    New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```	


## Create an image from a snapshot using PowerShell

You can create a managed image from a snapshot of a generalized VM by following these steps:

	
1. Create some variables. 

    ```azurepowershell-interactive
	$rgName = "myResourceGroup"
	$location = "EastUS"
	$snapshotName = "mySnapshot"
	$imageName = "myImage"
	```

2. Get the snapshot.

   ```azurepowershell-interactive
   $snapshot = Get-AzSnapshot -ResourceGroupName $rgName -SnapshotName $snapshotName
   ```
   
3. Create the image configuration.

    ```azurepowershell-interactive
	$imageConfig = New-AzImageConfig -Location $location
	$imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -SnapshotId $snapshot.Id
	```
4. Create the image.

    ```azurepowershell-interactive
    New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```	


## Create an image from a VM that uses a storage account

To create a managed image from a VM that doesn't use managed disks, you need the URI of the OS VHD in the storage account, in the following format: https://*mystorageaccount*.blob.core.windows.net/*vhdcontainer*/*vhdfilename.vhd*. In this example, the VHD is in *mystorageaccount*, in a container named *vhdcontainer*, and the VHD filename is *vhdfilename.vhd*.


1.  Create some variables.

    ```azurepowershell-interactive
	$vmName = "myVM"
	$rgName = "myResourceGroup"
	$location = "EastUS"
	$imageName = "myImage"
	$osVhdUri = "https://mystorageaccount.blob.core.windows.net/vhdcontainer/vhdfilename.vhd"
    ```
2. Stop/deallocate the VM.

    ```azurepowershell-interactive
	Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
	```
	
3. Mark the VM as generalized.

    ```azurepowershell-interactive
	Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized	
	```
4.  Create the image by using your generalized OS VHD.

    ```azurepowershell-interactive
	$imageConfig = New-AzImageConfig -Location $location
	$imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
	$image = New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

	
## Next steps
- [Create a VM from a managed image](create-vm-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).	
