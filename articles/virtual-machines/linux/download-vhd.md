---
title: Download a Linux VHD from Azure 
description: Download a Linux VHD using the Azure CLI and the Azure portal.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 08/21/2019
ms.author: cynthn
---

# Download a Linux VHD from Azure

In this article, you learn how to download a Linux virtual hard disk (VHD) file from Azure using the Azure CLI and Azure portal. 

If you haven't already done so, install [Azure CLI](/cli/azure/install-az-cli2).

## Stop the VM

A VHD can’t be downloaded from Azure if it's attached to a running VM. You need to stop the VM to download a VHD. If you want to use a VHD as an [image](tutorial-custom-images.md) to create other VMs with new disks, you need to deprovision and generalize the operating system contained in the file and stop the VM. To use the VHD as a disk for a new instance of an existing VM or data disk, you only need to stop and deallocate the VM.

To use the VHD as an image to create other VMs, complete these steps:

1. Use SSH, the account name, and the public IP address of the VM to connect to it and deprovision it. You can find the public IP address with [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). The +user parameter also removes the last provisioned user account. If you are baking account credentials in to the VM, leave out this +user parameter. The following example removes the last provisioned user account:

    ```bash
    ssh azureuser@<publicIpAddress>
    sudo waagent -deprovision+user -force
    exit 
    ```

2. Sign in to your Azure account with [az login](/cli/azure/reference-index).
3. Stop and deallocate the VM.

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

4. Generalize the VM. 

    ```azurecli
    az vm generalize --resource-group myResourceGroup --name myVM
    ``` 

To use the VHD as a disk for a new instance of an existing VM or data disk, complete these steps:

1.	Sign in to the [Azure portal](https://portal.azure.com/).
2.	On the left menu, select **Virtual Machines**.
3.	Select the VM from the list.
4.	On the page for the VM, select **Stop**.

    ![Stop VM](./media/download-vhd/export-stop.png)

## Generate SAS URL

To download the VHD file, you need to generate a [shared access signature (SAS)](../../storage/common/storage-sas-overview.md?toc=/azure/virtual-machines/windows/toc.json) URL. When the URL is generated, an expiration time is assigned to the URL.

1.	On the menu of the page for the VM, select **Disks**.
2.	Select the operating system disk for the VM, and then select **Disk Export**.
3.	Select **Generate URL**.

    ![Generate URL](./media/download-vhd/export-generate.png)

## Download VHD

1.	Under the URL that was generated, select **Download the VHD file**.
**
    ![Download VHD](./media/download-vhd/export-download.png)

2.	You may need to select **Save** in the browser to start the download. The default name for the VHD file is *abcd*.

    ![Select Save in the browser](./media/download-vhd/export-save.png)

## Next steps

- Learn how to [upload and create a Linux VM from custom disk with the Azure CLI](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 
- [Manage Azure disks the Azure CLI](tutorial-manage-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
