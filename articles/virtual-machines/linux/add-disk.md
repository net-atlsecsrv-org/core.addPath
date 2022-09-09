---
title: Add a data disk to Linux VM using the Azure CLI 
description: Learn to add a persistent data disk to your Linux VM with the Azure CLI
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 08/20/2020
ms.author: cynthn
ms.subservice: disks
---
# Add a disk to a Linux VM

This article shows you how to attach a persistent disk to your VM so that you can preserve your data - even if your VM is reprovisioned due to maintenance or resizing.


## Attach a new disk to a VM

If you want to add a new, empty data disk on your VM, use the [az vm disk attach](/cli/azure/vm/disk?view=azure-cli-latest) command with the `--new` parameter. If your VM is in an Availability Zone, the disk is automatically created in the same zone as the VM. For more information, see [Overview of Availability Zones](../../availability-zones/az-overview.md). The following example creates a disk named *myDataDisk* that is 50 Gb in size:

```azurecli
az vm disk attach \
   -g myResourceGroup \
   --vm-name myVM \
   --name myDataDisk \
   --new \
   --size-gb 50
```

## Attach an existing disk

To attach an existing disk, find the disk ID and pass the ID to the [az vm disk attach](/cli/azure/vm/disk?view=azure-cli-latest) command. The following example queries for a disk named *myDataDisk* in *myResourceGroup*, then attaches it to the VM named *myVM*:

```azurecli
diskId=$(az disk show -g myResourceGroup -n myDataDisk --query 'id' -o tsv)

az vm disk attach -g myResourceGroup --vm-name myVM --name $diskId
```

## Format and mount the disk

To partition, format, and mount your new disk so your Linux VM can use it, SSH into your VM. For more information, see [How to use SSH with Linux on Azure](mac-create-ssh-keys.md). The following example connects to a VM with the public IP address of *10.123.123.25* with the username *azureuser*:

```bash
ssh azureuser@10.123.123.25
```

### Find the disk

Once connected to your VM, you need to find the disk. In this example, we are using `lsblk` to list the disks. 

```bash
lsblk -o NAME,HCTL,SIZE,MOUNTPOINT | grep -i "sd"
```

The output is similar to the following example:

```bash
sda     0:0:0:0      30G
├─sda1             29.9G /
├─sda14               4M
└─sda15             106M /boot/efi
sdb     1:0:1:0      14G
└─sdb1               14G /mnt
sdc     3:0:0:0      50G
```

Here, `sdc` is the disk that we want, because it is 50G. If you aren't sure which disk it is based on size alone, you can go to the VM page in the portal, select **Disks**, and check the LUN number for the disk under **Data disks**. 


### Format the disk

Format the disk with `parted`, if the disk size is 2 tebibytes (TiB) or larger then you must use GPT partitioning, if it is under 2TiB, then you can use either MBR or GPT partitioning. 

> [!NOTE]
> It is recommended that you use the latest version `parted` that is available for your distro.
> If the disk size is 2 tebibytes (TiB) or larger, you must use GPT partitioning. If disk size is under 2 TiB, then you can use either MBR or GPT partitioning.  


The following example uses `parted` on `/dev/sdc`, which is where the first data disk will typically be on most VMs. Replace `sdc` with the correct option for your disk. We are also formatting it using the [XFS](https://xfs.wiki.kernel.org/) filesystem.

```bash
sudo parted /dev/sdc --script mklabel gpt mkpart xfspart xfs 0% 100%
sudo mkfs.xfs /dev/sdc1
sudo partprobe /dev/sdc1
```

Use the [`partprobe`](https://linux.die.net/man/8/partprobe) utility to make sure the kernel is aware of the new partition and filesystem. Failure to use `partprobe` can cause the blkid or lslbk commands to not return the UUID for the new filesystem immediately.


### Mount the disk

Now, create a directory to mount the file system using `mkdir`. The following example creates a directory at `/datadrive`:

```bash
sudo mkdir /datadrive
```

Use `mount` to then mount the filesystem. The following example mounts the `/dev/sdc1` partition to the `/datadrive` mount point:

```bash
sudo mount /dev/sdc1 /datadrive
```

### Persist the mount

To ensure that the drive is remounted automatically after a reboot, it must be added to the */etc/fstab* file. It is also highly recommended that the UUID (Universally Unique Identifier) is used in */etc/fstab* to refer to the drive rather than just the device name (such as, */dev/sdc1*). If the OS detects a disk error during boot, using the UUID avoids the incorrect disk being mounted to a given location. Remaining data disks would then be assigned those same device IDs. To find the UUID of the new drive, use the `blkid` utility:

```bash
sudo blkid
```

The output looks similar to the following example:

```bash
/dev/sda1: LABEL="cloudimg-rootfs" UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4" PARTUUID="1a1b1c1d-11aa-1234-1a1a1a1a1a1a"
/dev/sda15: LABEL="UEFI" UUID="BCD7-96A6" TYPE="vfat" PARTUUID="1e1g1cg1h-11aa-1234-1u1u1a1a1u1u"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4" TYPE="ext4" PARTUUID="1a2b3c4d-01"
/dev/sda14: PARTUUID="2e2g2cg2h-11aa-1234-1u1u1a1a1u1u"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="xfs" PARTLABEL="xfspart" PARTUUID="c1c2c3c4-1234-cdef-asdf3456ghjk"
```

> [!NOTE]
> Improperly editing the **/etc/fstab** file could result in an unbootable system. If unsure, refer to the distribution's documentation for information on how to properly edit this file. It is also recommended that a backup of the /etc/fstab file is created before editing.

Next, open the */etc/fstab* file in a text editor as follows:

```bash
sudo nano /etc/fstab
```

In this example, use the UUID value for the `/dev/sdc1` device that was created in the previous steps, and the mountpoint of `/datadrive`. Add the following line to the end of the `/etc/fstab` file:

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   xfs   defaults,nofail   1   2
```

In this example, we are using the nano editor, so when you are done editing the file, use `Ctrl+O` to write the file and `Ctrl+X` to exit the editor.

> [!NOTE]
> Later removing a data disk without editing fstab could cause the VM to fail to boot. Most distributions provide either the *nofail* and/or *nobootwait* fstab options. These options allow a system to boot even if the disk fails to mount at boot time. Consult your distribution's documentation for more information on these parameters.
>
> The *nofail* option ensures that the VM starts even if the filesystem is corrupt or the disk does not exist at boot time. Without this option, you may encounter behavior as described in [Cannot SSH to Linux VM due to FSTAB errors](/archive/blogs/linuxonazure/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting)
>
> The Azure VM Serial Console can be used for console access to your VM if modifying fstab has resulted in a boot failure. More details are available in the [Serial Console documentation](../troubleshooting/serial-console-linux.md).

### TRIM/UNMAP support for Linux in Azure
Some Linux kernels support TRIM/UNMAP operations to discard unused blocks on the disk. This feature is primarily useful in standard storage to inform Azure that deleted pages are no longer valid and can be discarded, and can save money if you create large files and then delete them.

There are two ways to enable TRIM support in your Linux VM. As usual, consult your distribution for the recommended approach:

* Use the `discard` mount option in */etc/fstab*, for example:

    ```bash
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   xfs   defaults,discard   1   2
    ```
* In some cases, the `discard` option may have performance implications. Alternatively, you can run the `fstrim` command manually from the command line, or add it to your crontab to run regularly:

    **Ubuntu**

    ```bash
    sudo apt-get install util-linux
    sudo fstrim /datadrive
    ```

    **RHEL/CentOS**

    ```bash
    sudo yum install util-linux
    sudo fstrim /datadrive
    ```

## Troubleshooting

[!INCLUDE [virtual-machines-linux-lunzero](../../../includes/virtual-machines-linux-lunzero.md)]

## Next steps

* To ensure your Linux VM is configured correctly, review the [Optimize your Linux machine performance](optimization.md) recommendations.
* Expand your storage capacity by adding additional disks and [configure RAID](configure-raid.md) for additional performance.
