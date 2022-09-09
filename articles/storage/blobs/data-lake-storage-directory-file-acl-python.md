---
title: Azure Data Lake Storage Gen2 Python SDK for files & ACLs
description: Use Python manage directories and file and directory access control lists (ACL) in storage accounts that has hierarchical namespace (HNS) enabled.
author: normesta
ms.service: storage
ms.date: 09/10/2020
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-python
---

# Use Python to manage directories, files, and ACLs in Azure Data Lake Storage Gen2

This article shows you how to use Python to create and manage directories, files, and permissions in storage accounts that has hierarchical namespace (HNS) enabled. 

[Package (Python Package Index)](https://pypi.org/project/azure-storage-file-datalake/) | [Samples](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples) | [API reference](/python/api/azure-storage-file-datalake/azure.storage.filedatalake) | [Gen1 to Gen2 mapping](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md) | [Give Feedback](https://github.com/Azure/azure-sdk-for-python/issues)

## Prerequisites

> [!div class="checklist"]
> * An Azure subscription. See [Get Azure free trial](https://azure.microsoft.com/pricing/free-trial/).
> * A storage account that has hierarchical namespace (HNS) enabled. Follow [these](data-lake-storage-quickstart-create-account.md) instructions to create one.

## Set up your project

Install the Azure Data Lake Storage client library for Python by using [pip](https://pypi.org/project/pip/).

```
pip install azure-storage-file-datalake
```

Add these import statements to the top of your code file.

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
from azure.core._match_conditions import MatchConditions
from azure.storage.filedatalake._models import ContentSettings
```

## Connect to the account

To use the snippets in this article, you'll need to create a **DataLakeServiceClient** instance that represents the storage account. 

### Connect by using an account key

This is the easiest way to connect to an account. 

This example creates a **DataLakeServiceClient** instance by using an account key.

```python
try:  
    global service_client
        
    service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
        "https", storage_account_name), credential=storage_account_key)
    
except Exception as e:
    print(e)
```
 
- Replace the `storage_account_name` placeholder value with the name of your storage account.

- Replace the `storage_account_key` placeholder value with your storage account access key.

### Connect by using Azure Active Directory (AD)

You can use the [Azure identity client library for Python](https://pypi.org/project/azure-identity/) to authenticate your application with Azure AD.

This example creates a **DataLakeServiceClient** instance by using a client ID, a client secret, and a tenant ID.  To get these values, see [Acquire a token from Azure AD for authorizing requests from a client application](../common/storage-auth-aad-app.md).

```python
def initialize_storage_account_ad(storage_account_name, client_id, client_secret, tenant_id):
    
    try:  
        global service_client

        credential = ClientSecretCredential(tenant_id, client_id, client_secret)

        service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
            "https", storage_account_name), credential=credential)
    
    except Exception as e:
        print(e)
```

> [!NOTE]
> For more examples, see the [Azure identity client library for Python](https://pypi.org/project/azure-identity/) documentation.

## Create a container

A container acts as a file system for your files. You can create one by calling the **FileSystemDataLakeServiceClient.create_file_system** method.

This example creates a container named `my-file-system`.

```python
def create_file_system():
    try:
        global file_system_client

        file_system_client = service_client.create_file_system(file_system="my-file-system")
    
    except Exception as e:
        print(e) 
```


## Create a directory

Create a directory reference by calling the **FileSystemClient.create_directory** method.

This example adds a directory named `my-directory` to a container. 

```python
def create_directory():
    try:
        file_system_client.create_directory("my-directory")
    
    except Exception as e:
     print(e) 
```

## Rename or move a directory

Rename or move a directory by calling the **DataLakeDirectoryClient.rename_directory** method. Pass the path of the desired directory a parameter. 

This example renames a sub-directory to the name `my-subdirectory-renamed`.

```python
def rename_directory():
    try:
       
       file_system_client = service_client.get_file_system_client(file_system="my-file-system")
       directory_client = file_system_client.get_directory_client("my-directory")
       
       new_dir_name = "my-directory-renamed"
       directory_client.rename_directory(rename_destination=directory_client.file_system_name + '/' + new_dir_name)

    except Exception as e:
     print(e) 
```

## Delete a directory

Delete a directory by calling the **DataLakeDirectoryClient.delete_directory** method.

This example deletes a directory named `my-directory`.  

```python
def delete_directory():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")
        directory_client = file_system_client.get_directory_client("my-directory")

        directory_client.delete_directory()
    except Exception as e:
     print(e) 
```

## Manage directory permissions

Get the access control list (ACL) of a directory by calling the **DataLakeDirectoryClient.get_access_control** method and set the ACL by calling the **DataLakeDirectoryClient.set_access_control** method.

> [!NOTE]
> If your application authorizes access by using Azure Active Directory (Azure AD), then make sure that the security principal that your application uses to authorize access has been assigned the [Storage Blob Data Owner role](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). To learn more about how ACL permissions are applied and the effects of changing them, see  [Access control in Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

This example gets and sets the ACL of a directory named `my-directory`. The string `rwxr-xrw-` gives the owning user read, write, and execute permissions, gives the owning group only read and execute permissions, and gives all others read and write permission.

```python
def manage_directory_permissions():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])
        
        new_dir_permissions = "rwxr-xrw-"
        
        directory_client.set_access_control(permissions=new_dir_permissions)
        
        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])
    
    except Exception as e:
     print(e) 
```

You can also get and set the ACL of the root directory of a container. To get the root directory, call the **FileSystemClient._get_root_directory_client** method.

## Upload a file to a directory 

First, create a file reference in the target directory by creating an instance of the **DataLakeFileClient** class. Upload a file by calling the **DataLakeFileClient.append_data** method. Make sure to complete the upload by calling the **DataLakeFileClient.flush_data** method.

This example uploads a text file to a directory named `my-directory`.   

```python
def upload_file_to_directory():
    try:

        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.create_file("uploaded-file.txt")
        local_file = open("C:\\file-to-upload.txt",'rb')

        file_contents = local_file.read()

        file_client.append_data(data=file_contents, offset=0, length=len(file_contents))

        file_client.flush_data(len(file_contents))

    except Exception as e:
      print(e) 
```

> [!TIP]
> If your file size is large, your code will have to make multiple calls to the **DataLakeFileClient.append_data** method. Consider using the **DataLakeFileClient.upload_data** method instead. That way, you can upload the entire file in a single call. 

## Upload a large file to a directory

Use the **DataLakeFileClient.upload_data** method to upload large files without having to make multiple calls to the **DataLakeFileClient.append_data** method.

```python
def upload_file_to_directory_bulk():
    try:

        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.get_file_client("uploaded-file.txt")

        local_file = open("C:\\file-to-upload.txt",'rb')

        file_contents = local_file.read()

        file_client.upload_data(file_contents, overwrite=True)

    except Exception as e:
      print(e) 
```

## Manage file permissions

Get the access control list (ACL) of a file by calling the **DataLakeFileClient.get_access_control** method and set the ACL by calling the **DataLakeFileClient.set_access_control** method.

> [!NOTE]
> If your application authorizes access by using Azure Active Directory (Azure AD), then make sure that the security principal that your application uses to authorize access has been assigned the [Storage Blob Data Owner role](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). To learn more about how ACL permissions are applied and the effects of changing them, see  [Access control in Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

This example gets and sets the ACL of a file named `my-file.txt`. The string `rwxr-xrw-` gives the owning user read, write, and execute permissions, gives the owning group only read and execute permissions, and gives all others read and write permission.

```python
def manage_file_permissions():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.get_file_client("uploaded-file.txt")

        acl_props = file_client.get_access_control()
        
        print(acl_props['permissions'])
        
        new_file_permissions = "rwxr-xrw-"
        
        file_client.set_access_control(permissions=new_file_permissions)
        
        acl_props = file_client.get_access_control()
        
        print(acl_props['permissions'])

    except Exception as e:
     print(e) 
```

## Download from a directory 

Open a local file for writing. Then, create a **DataLakeFileClient** instance that represents the file that you want to download. Call the **DataLakeFileClient.read_file** to read bytes from the file and then write those bytes to the local file. 

```python
def download_file_from_directory():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        local_file = open("C:\\file-to-download.txt",'wb')

        file_client = directory_client.get_file_client("uploaded-file.txt")

        download = file_client.download_file()

        downloaded_bytes = download.readall()

        local_file.write(downloaded_bytes)

        local_file.close()

    except Exception as e:
     print(e)
```
## List directory contents

List directory contents by calling the **FileSystemClient.get_paths** method, and then enumerating through the results.

This example, prints the path of each subdirectory and file that is located in a directory named `my-directory`.

```python
def list_directory_contents():
    try:
        
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        paths = file_system_client.get_paths(path="my-directory")

        for path in paths:
            print(path.name + '\n')

    except Exception as e:
     print(e) 
```

## Set an ACL recursively (preview)

You can add, update, and remove ACLs recursively on the existing child items of a parent directory without having to make these changes individually for each child item. For more information, see [Set access control lists (ACLs) recursively for Azure Data Lake Storage Gen2](recursive-access-control-lists.md).

## See also

* [API reference documentation](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)
* [Package (Python Package Index)](https://pypi.org/project/azure-storage-file-datalake/)
* [Samples](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
* [Gen1 to Gen2 mapping](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [Known issues](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Give Feedback](https://github.com/Azure/azure-sdk-for-python/issues)
