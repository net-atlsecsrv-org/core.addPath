---
title: Using the Azure classic CLI with Azure Storage | Microsoft Docs
description: Learn how to use the Azure classic Command-Line Interface (CLI) with Azure Storage to create and manage storage accounts and work with Azure blobs and files.
services: storage
author: tamram

ms.service: storage
ms.topic: article
ms.date: 01/30/2017
ms.author: tamram
ms.reviewer: seguler
ms.subservice: common
---

# Using the Azure classic CLI with Azure Storage

## Overview

The Azure classic CLI provides a set of open source, cross-platform commands for working with the Azure Platform. It provides much of the same functionality found in the [Azure portal](https://portal.azure.com) as well as rich data access functionality.

In this guide, we'll explore how to use [Azure classic CLI](../../cli-install-nodejs.md) to perform a variety of development and administration tasks with Azure Storage. We recommend that you download and install or upgrade to the latest classic CLI before using this guide.

This guide assumes that you understand the basic concepts of Azure Storage. The guide provides a number of scripts to demonstrate the usage of the classic CLI with Azure Storage. Be sure to update the script variables based on your configuration before running each script.

> [!NOTE]
> The guide provides the Azure classic CLI command and script examples for classic storage accounts. See [Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management](../../virtual-machines/azure-cli-arm-commands.md#azure-storage-commands-to-manage-your-storage-objects) for Azure classic CLI commands for Resource Manager storage accounts.
>
>

[!INCLUDE [storage-cli-versions](../../../includes/storage-cli-versions.md)]

## Get started with Azure Storage and the Azure classic CLI in 5 minutes
This guide uses Ubuntu for examples, but other OS platforms should perform similarly.

**New to Azure:** Get a Microsoft Azure subscription and a Microsoft account associated with that subscription. For information on Azure purchase options, see [Free Trial](https://azure.microsoft.com/pricing/free-trial/), [Purchase Options](https://azure.microsoft.com/pricing/purchase-options/), and [Member Offers](https://azure.microsoft.com/pricing/member-offers/) (for members of MSDN, Microsoft Partner Network, and BizSpark, and other Microsoft programs).

See [Assigning administrator roles in Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) for more information about Azure subscriptions.

**After creating a Microsoft Azure subscription and account:**

1. Download and install the Azure classic CLI following the instructions outlined in [Install the Azure classic CLI](../../cli-install-nodejs.md).
2. Once the classic CLI has been installed, you will be able to use the azure command from your command-line interface (Bash, Terminal, Command prompt) to access the classic CLI commands. Type the _azure_ command and you should see the following output.

    ![Azure Command Output](./media/storage-azure-cli/azure_command.png)   
3. In the command-line interface, type `azure storage` to list out all the azure storage commands and get a first impression of the functionalities the classic CLI provides. You can type command name with **-h** parameter (for example, `azure storage share create -h`) to see details of command syntax.
4. Now, we'll give you a simple script that shows basic classic CLI commands to access Azure Storage. The script will first ask you to set two variables for your storage account and key. Then, the script will create a new container in this new storage account and upload an existing image file (blob) to that container. After the script lists all blobs in that container, it will download the image file to the destination directory which exists on the local computer.

    ```azurecli
    #!/bin/bash
    # A simple Azure storage example

    export AZURE_STORAGE_ACCOUNT=<storage_account_name>
    export AZURE_STORAGE_ACCESS_KEY=<storage_account_key>

    export container_name=<container_name>
    export blob_name=<blob_name>
    export image_to_upload=<image_to_upload>
    export destination_folder=<destination_folder>

    echo "Creating the container..."
    azure storage container create $container_name

    echo "Uploading the image..."
    azure storage blob upload $image_to_upload $container_name $blob_name

    echo "Listing the blobs..."
    azure storage blob list $container_name

    echo "Downloading the image..."
    azure storage blob download $container_name $blob_name $destination_folder

    echo "Done"
    ```

5. In your local computer, open your preferred text editor (vim for example). Type the above script into your text editor.
6. Now, you need to update the script variables based on your configuration settings.

   * **<storage_account_name>** Use the given name in the script or enter a new name for your storage account. **Important:** The name of the storage account must be unique in Azure. It must be lowercase, too!
   * **<storage_account_key>** The access key of your storage account.
   * **<container_name>** Use the given name in the script or enter a new name for your container.
   * **<image_to_upload>** Enter a path to a picture on your local computer, such as: "~/images/HelloWorld.png".
   * **<destination_folder>** Enter a path to a local directory to store files downloaded from Azure Storage, such as: "~/downloadImages".
7. After you've updated the necessary variables in vim, press key combinations `ESC`, `:`, `wq!` to save the script.
8. To run this script, simply type the script file name in the bash console. After this script runs, you should have a local destination folder that includes the downloaded image file. The following screenshot shows an example output:

After the script runs, you should have a local destination folder that includes the downloaded image file.

## Manage storage accounts with the Azure classic CLI
### Connect to your Azure subscription
While most of the storage commands will work without an Azure subscription, we recommend you to connect to your subscription from the classic CLI.

### Create a new storage account
To use Azure storage, you will need a storage account. You can create a new Azure storage account after you have configured your computer to connect to your subscription.

```azurecli
azure storage account create <account_name>
```

The name of your storage account must be between 3 and 24 characters in length and use numbers and lower-case letters only.

### Set a default Azure storage account in environment variables
You can have multiple storage accounts in your subscription. You can choose one of them and set it in the environment variables for all the storage commands in the same session. This enables you to run the classic CLI storage commands without specifying the storage account and key explicitly.

```azurecli
export AZURE_STORAGE_ACCOUNT=<account_name>
export AZURE_STORAGE_ACCESS_KEY=<key>
```

Another way to set a default storage account is using connection string. Firstly get the connection string by command:

```azurecli
azure storage account connectionstring show <account_name>
```

Then copy the output connection string and set it to environment variable:

```azurecli
export AZURE_STORAGE_CONNECTION_STRING=<connection_string>
```

## Create and manage blobs
Azure Blob storage is a service for storing large amounts of unstructured data, such as text or binary data, that can be accessed from anywhere in the world via HTTP or HTTPS. This section assumes that you are already familiar with the Azure Blob storage concepts. For detailed information, see [Get started with Azure Blob storage using .NET](../blobs/storage-dotnet-how-to-use-blobs.md) and [Blob Service Concepts](https://msdn.microsoft.com/library/azure/dd179376.aspx).

### Create a container
Every blob in Azure storage must be in a container. You can create a private container using the `azure storage container create` command:

```azurecli
azure storage container create mycontainer
```

> [!NOTE]
> There are three levels of anonymous read access: **Off**, **Blob**, and **Container**. To prevent anonymous access to blobs, set the Permission parameter to **Off**. By default, the new container is private and can be accessed only by the account owner. To allow anonymous public read access to blob resources, but not to container metadata or to the list of blobs in the container, set the Permission parameter to **Blob**. To allow full public read access to blob resources, container metadata, and the list of blobs in the container, set the Permission parameter to **Container**. For more information, see [Manage anonymous read access to containers and blobs](../blobs/storage-manage-access-to-resources.md).
>
>

### Upload a blob into a container
Azure Blob Storage supports block blobs and page blobs. For more information, see [Understanding Block Blobs, Append Blobs, and Page Blobs](https://msdn.microsoft.com/library/azure/ee691964.aspx).

To upload blobs in to a container, you can use the `azure storage blob upload`. By default, this command uploads the local files to a block blob. To specify the type for the blob, you can use the `--blobtype` parameter.

```azurecli
azure storage blob upload '~/images/HelloWorld.png' mycontainer myBlockBlob
```

### Download blobs from a container
The following example demonstrates how to download blobs from a container.

```azurecli
azure storage blob download mycontainer myBlockBlob '~/downloadImages/downloaded.png'
```

### Copy blobs
You can copy blobs within or across storage accounts and regions asynchronously.

The following example demonstrates how to copy blobs from one storage account to another. In this sample we create a container where blobs are publicly, anonymously accessible.

```azurecli
azure storage container create mycontainer2 -a <accountName2> -k <accountKey2> -p Blob

azure storage blob upload '~/Images/HelloWorld.png' mycontainer2 myBlockBlob2 -a <accountName2> -k <accountKey2>

azure storage blob copy start 'https://<accountname2>.blob.core.windows.net/mycontainer2/myBlockBlob2' mycontainer
```

This sample performs an asynchronous copy. You can monitor the status of each copy operation by running the `azure storage blob copy show` operation.

Note that the source URL provided for the copy operation must either be publicly accessible, or include a SAS (shared access signature) token.

### Delete a blob
To delete a blob, use the below command:

```azurecli
azure storage blob delete mycontainer myBlockBlob2
```

## Create and manage file shares
Azure Files offers shared storage for applications using the standard SMB protocol. Microsoft Azure virtual machines and cloud services, as well as on-premises applications, can share file data via mounted shares. You can manage file shares and file data via the classic CLI. For more information on Azure Files, see [Introduction to Azure Files](../files/storage-files-introduction.md).

### Create a file share
An Azure file share is an SMB file share in Azure. All directories and files must be created in a file share. An account can contain an unlimited number of shares, and a share can store an unlimited number of files, up to the capacity limits of the storage account. The following example creates a file share named **myshare**.

```azurecli
azure storage share create myshare
```

### Create a directory
A directory provides an optional hierarchical structure for an Azure file share. The following example creates a directory named **myDir** in the file share.

```azurecli
azure storage directory create myshare myDir
```

Note that directory path can include multiple levels, *e.g.*, **a/b**. However, you must ensure that all parent directories exist. For example, for path **a/b**, you must create directory **a** first, then create directory **b**.

### Upload a local file to directory
The following example uploads a file from **~/temp/samplefile.txt** to the **myDir** directory. Edit the file path so that it points to a valid file on your local machine:

```azurecli
azure storage file upload '~/temp/samplefile.txt' myshare myDir
```

Note that a file in the share can be up to 1 TB in size.

### List the files in the share root or directory
You can list the files and subdirectories in a share root or a directory using the following command:

```azurecli
azure storage file list myshare myDir
```

Note that the directory name is optional for the listing operation. If omitted, the command lists the contents of the root directory of the share.

### Copy files
Beginning with version 0.9.8 of the classic CLI, you can copy a file to another file, a file to a blob, or a blob to a file. Below we demonstrate how to perform these copy operations using CLI commands. To copy a file to the new directory:

```azurecli
azure storage file copy start --source-share srcshare --source-path srcdir/hello.txt --dest-share destshare
    --dest-path destdir/hellocopy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString
```

To copy a blob to a file directory:

```azurecli
azure storage file copy start --source-container srcctn --source-blob hello2.txt --dest-share hello
    --dest-path hellodir/hello2copy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString
```

## Next Steps

You can find Azure classic CLI command reference for working with Storage resources here:

* [Azure classic CLI commands in Resource Manager mode](../../virtual-machines/azure-cli-arm-commands.md#azure-storage-commands-to-manage-your-storage-objects)
* [Azure classic CLI commands in Azure Service Management mode](../../cli-install-nodejs.md)

You may also like to try the latest version of the [Azure CLI](../storage-azure-cli.md), for use with the Resource Manager deployment model.
