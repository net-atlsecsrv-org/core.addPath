---
title: Azure Quickstart - Create and use an Azure Files share on Windows VMs | Microsoft Docs
description: In this quickstart, you setup an Azure Files share in the Azure portal and connect it to a Windows virtual machine. You connect to the Files share, upload a file to the Files share. Then you take a snapshot of the Files share, modify the file in the Files share, and restore a previous snapshot of the Files share.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: rogarana
ms.subservice: files
#Customer intent: As an IT admin new to Azure Files, I want to try out Azure file share so I can determine whether I want to subscribe to the service.
---

# Quickstart: Create and manage Azure Files share with Windows virtual machines

The article demonstrates the basic steps for creating and using an Azure Files share. In this quickstart, the emphasis is on quickly setting up an Azure Files share so you can experience how the service works. If you need more detailed instructions for creating and using Azure file shares in your own environment, see [Use an Azure file share with Windows](storage-how-to-use-files-windows.md).

If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.

## Sign in to Azure

Sign in to the [Azure portal](https://portal.azure.com).

## Prepare your environment

In this quickstart, you set up the following items:

- An Azure storage account and an Azure file share
- A Windows Server 2016 Datacenter VM

### Create a storage account

Before you can work with an Azure file share, you have to create an Azure storage account. A general-purpose v2 storage account provides access to all of the Azure Storage services: blobs, files, queues, and tables. The quickstart creates a general-purpose v2 storage account but, the steps to create any type of storage account are similar. A storage account can contain an unlimited number of shares. A share can store an unlimited number of files, up to the capacity limits of the storage account.

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

### Create an Azure file share

Next, you create a file share.

1. When the Azure storage account deployment is complete, select **Go to resource**.
1. Select **Files** from the storage account pane.

    ![Select Files](./media/storage-files-quick-create-use-windows/click-files.png)

1. Select **File Share**.

    ![Select the add file share button](./media/storage-files-quick-create-use-windows/create-file-share.png)

1. Name the new file share *qsfileshare* > enter "1" for the **Quota** > select **Create**. The quota can be a maximum of 5 TiB, but you only need 1 GiB for this quickstart.
1. Create a new txt file called *qsTestFile* on your local machine.
1. Select the new file share, then on the file share location, select **Upload**.

    ![Upload a file](./media/storage-files-quick-create-use-windows/create-file-share-portal5.png)

1. Browse to the location where you created your .txt file > select *qsTestFile.txt* > select **Upload**.

So far, you've created an Azure storage account and a file share with one file in it in Azure. Next you'll create the Azure VM with Windows Server 2016 Datacenter to represent the on-premises server in this quickstart.

### Deploy a VM

1. Next, expand the menu on the left side of the portal and choose **Create a resource** in the upper left-hand corner of the Azure portal.
1. In the search box above the list of **Azure Marketplace** resources, search for and select **Windows Server 2016 Datacenter**, then choose **Create**.
1. In the **Basics** tab, under **Project details**, select the resource group you created for this quickstart.

   ![Enter basic information about your VM in the portal blade](./media/storage-files-quick-create-use-windows/vm-resource-group-and-subscription.png)

1. Under **Instance details**, name the VM *qsVM*.
1. Leave the default settings for **Region**, **Availability options**, **Image**, and **Size**.
1. Under **Administrator account**, add *VMadmin* as the **Username** and enter a **Password** for the VM.
1. Under **Inbound port rules**, choose **Allow selected ports** and then select **RDP (3389)** and **HTTP** from the drop-down.
1. Select **Review + create**.
1. Select **Create**. Creating a new VM will take a few minutes to complete.

1. Once your VM deployment is complete, select **Go to resource**.

At this point, you've created a new virtual machine and attached a data disk. Now you need to connect to the VM.

### Connect to your VM

1. Select **Connect** on the virtual machine properties page.

   ![Connect to an Azure VM from the portal](./media/storage-files-quick-create-use-windows/connect-vm.png)

1. In the **Connect to virtual machine** page, keep the default options to connect by **IP address** over **port number** *3389* and select **Download RDP file**.
1. Open the downloaded RDP file and select **Connect** when prompted.
1. In the **Windows Security** window, select **More choices** and then **Use a different account**. Type the username as *localhost\username*, where &lt;username&gt; is the VM admin username you created for the virtual machine. Enter the password you created for the virtual machine, and then select **OK**.

   ![More choices](./media/storage-files-quick-create-use-windows/local-host2.png)

1. You may receive a certificate warning during the sign-in process. select **Yes** or **Continue** to create the connection.

## Map the Azure file share to a Windows drive

1. In the Azure portal, navigate to the *qsfileshare* fileshare and select **Connect**.
1. Copy the contents of the second box and paste it in **Notepad**.

   ![The UNC path from the Azure Files Connect pane](./media/storage-files-quick-create-use-windows/portal_netuse_connect2.png)

1. In the VM, open **File Explorer** and select **This PC** in the window. This selection will change the menus available on the ribbon. On the **Computer** menu, select **Map network drive**.
1. Select the drive letter and enter the UNC path. If you've followed the naming suggestions in this quickstart, copy *\\qsstorageacct.file.core.windows.net\qsfileshare* from **Notepad**.

   Make sure both checkboxes are checked.

   ![A screenshot of the "Map Network Drive" dialog](./media/storage-files-quick-create-use-windows/mountonwindows10.png)

1. Select **Finish**.
1. In the **Windows Security** dialog box:

   - From Notepad, copy the storage account name prepended with AZURE\ and paste it in the **Windows Security** dialog box as the username. If you've followed the naming suggestions in this quickstart, copy *AZURE\qsstorageacct*.
   - From Notepad, copy the storage account key and paste it in the **Windows Security** dialog box as the password.

      ![The UNC path from the Azure Files Connect pane](./media/storage-files-quick-create-use-windows/portal_netuse_connect3.png)

## Create a share snapshot

Now that you've mapped the drive, you can create a snapshot.

1. In the portal, navigate to your file share and select **Create snapshot**.

   ![Create a snapshot](./media/storage-files-quick-create-use-windows/create-snapshot.png)

1. In the VM, open the *qstestfile.txt* and type "this file has been modified" > Save and close the file.
1. Create another snapshot.

## Browse a share snapshot

1. On your file share, select **View snapshots**.
1. On the **File share snapshots** pane, select the first snapshot in the list.

   ![Selected snapshot in the list of time stamps](./media/storage-files-quick-create-use-windows/snapshot-list.png)

1. On the pane for that snapshot, select *qsTestFile.txt*.

## Restore from a snapshot

1. From the file share snapshot blade, right-click the *qsTestFile*, and select the **Restore** button.
1. Select **Overwrite original file**.

   ![Download and Restore buttons](./media/storage-files-quick-create-use-windows/snapshot-download-restore-portal.png)

1. In the VM, open the file. The unmodified version has been restored.

## Delete a share snapshot

1. On your file share, select **View snapshots**.
1. On the **File share snapshots** pane, select the last snapshot in the list and click **Delete**.

   ![Delete button](./media/storage-files-quick-create-use-windows/portal-snapshots-delete.png)

## Use a share snapshot in Windows

Just like with on-premises VSS snapshots, you can view the snapshots from your mounted Azure file share by using the Previous Versions tab.

1. In File Explorer, locate the mounted share.

   ![Mounted share in File Explorer](./media/storage-files-quick-create-use-windows/snapshot-windows-mount.png)

1. Select *qsTestFile.txt* and > right-click and select **Properties** from the menu.

   ![Right-click menu for a selected directory](./media/storage-files-quick-create-use-windows/snapshot-windows-previous-versions.png)

1. Select **Previous Versions** to see the list of share snapshots for this directory.

1. Select **Open** to open the snapshot.

   ![Previous Versions tab](./media/storage-files-quick-create-use-windows/snapshot-windows-list.png)

## Restore from a previous version

1. Select **Restore**. This action copies the contents of the entire directory recursively to the original location at the time the share snapshot was created.

   ![Restore button in warning message](./media/storage-files-quick-create-use-windows/snapshot-windows-restore.png)

## Clean up resources

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## Next steps

> [!div class="nextstepaction"]
> [Use an Azure file share with Windows](storage-how-to-use-files-windows.md)
