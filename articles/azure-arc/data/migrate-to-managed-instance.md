---
title: Migrate a database from SQL Server to Azure Arc enabled SQL Managed Instance
description: Migrate database from SQL Server to Azure Arc enabled SQL Managed Instance
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
---

# Migrate: SQL Server to Azure Arc enabled SQL Managed Instance

This scenario walks you through the steps for migrating a database from a SQL Server instance to Azure SQL managed instance in Azure Arc via two different backup and restore methods.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## Use Azure blob storage 

Use Azure blob storage for migrating to Azure Arc enabled SQL Managed Instance.

This method uses Azure Blob Storage as a temporary storage location that you can back up to and then restore from.

### Prerequisites

- [Install Azure Data Studio](install-client-tools.md)
- [Install Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)
- Azure subscription

### Step 1: Provision Azure blob storage

1. Follow the steps described in [Create an Azure Blob Storage account](../../storage/blobs/storage-blob-create-account-block-blob.md?tabs=azure-portal)
1. Launch Azure Storage Explorer
1. [Sign in to Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#sign-in-to-azure) to access the blob storage created in previous step
1. Right-click on the blob storage account and select **Create Blob Container** to create a new container where the backup file will be stored

### Step 2: Get storage blob credentials

1. In Azure Storage Explorer, right-click on the blob container that was just created and select **Get Shared Access Signature**

1. Select the **Read**, **Write** and **List**

1. Select **Create**

   Take note of the URI and the Query String from this screen. These will be needed in later steps. Click on the **Copy** button to save to a Notepad/OneNote etc.

1. Close the **Shared Access Signature** window.

### Step 3: Backup database file to Azure Blob Storage

In this step, we will connect to the source SQL Server and create the backup file of the database that we want to migrate to SQL Managed Instance - Azure Arc.

1. Launch Azure Data Studio
1. Connect to the SQL Server instance that has the database you want to migrate to SQL Managed Instance - Azure Arc
1. Right-click on the database and select **New Query**
1. Prepare your query in the following format replacing the placeholders indicated by the `<...>` using the information from the shared access signature in earlier steps.  Once you have substituted the values, run the query.

   ```sql
   IF NOT EXISTS  
   (SELECT * FROM sys.credentials
   WHERE name = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>')  
   CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
     WITH IDENTITY = 'SHARED ACCESS SIGNATURE',  
      SECRET = '<SAS_TOKEN>';  
   ```

1. Similarly, prepare the **BACKUP DATABASE** command as follows to create a backup file to the blob container.  Once you have substituted the values, run the query.

   ```sql
   BACKUP DATABASE <database name> TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>'
   ```

1. Open Azure Storage Explorer and validate that the backup file created in previous step is visible in the Blob container

### Step 4: Restore the database from Azure blob storage to SQL Managed Instance - Azure Arc

1. From Azure Data Studio, login and connect to the SQL Managed Instance - Azure Arc.
1. Expand the **System Databases**, right-click on **master** database and select **New Query**.
1. In the query editor window, prepare and run the same query from previous step to create the credentials.

   ```sql
   IF NOT EXISTS  
   (SELECT * FROM sys.credentials
   WHERE name = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>')  
   CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
     WITH IDENTITY = 'SHARED ACCESS SIGNATURE',  
     SECRET = '<SAS_TOKEN>';  
   ```

1. Prepare and run the below command to verify the backup file is readable, and intact.

   ```console
   RESTORE FILELISTONLY FROM URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>/<file name>.bak'
   ```

1. Prepare and run the **RESTORE DATABASE** command as follows to restore the backup file to a database on SQL Managed Instance - Azure Arc

   ```sql
   RESTORE DATABASE <database name> FROM URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>/<file name>'
   WITH MOVE 'Test' to '/var/opt/mssql/data/<file name>.mdf'
   ,MOVE 'Test_log' to '/var/opt/mssql/data/<file name>.ldf'
   ,RECOVERY  
   ,REPLACE  
   ,STATS = 5;  
   GO
   ```

Learn more about backup to URL here:

[Backup to URL docs](/sql/relational-databases/backup-restore/sql-server-backup-to-url)

[Backup to URL using SQL Server Management Studio (SSMS)](/sql/relational-databases/tutorial-sql-server-backup-and-restore-to-azure-blob-storage-service)

-------

## Method 2: Copy the backup file into an Azure SQL Managed Instance - Azure Arc pod using kubectl

This method shows you how to take a backup file that you create via any method and then copy it into local storage in the Azure SQL managed instance pod so you can restore from there much like you would on a typical file system on Windows or Linux. In this scenario, you will be using the command `kubectl cp` to copy the file from one place into the pod's file system.

### Prerequisites

- Install and configure kubectl to point to your Kubernetes cluster where Azure Arc data services is deployed
- Have a tool like Azure Data Studio or SQL Server Management Server installed and connected to the SQL Server where you want to create the backup file OR have an existing .bak file already created on your local file system.

### Step 1: Backup the database if you haven't already

Backup the SQL Server database to your local file path like any typical SQL Server backup to disk:

 ```sql
BACKUP DATABASE Test
TO DISK = 'c:\tmp\test.bak'
WITH FORMAT, MEDIANAME = 'Test’ ;
GO
```

### Step 2: Copy the backup file into the pod's file system

Find the name of the pod where the sql instance is deployed. Typically it should look like `pod/<sqlinstancename>-0`

Get the list of all pods by running:

 ```console
kubectl get pods -n <namespace of data controller>
```

Example:

Copy the backup file from the local storage to the sql pod in the cluster.

 ```console
kubectl cp <source file location> <pod name>:var/opt/mssql/data/<file name> -n <namespace name>

#Example:
kubectl cp C:\Backupfiles\test.bak sqlinstance1-0:var/opt/mssql/data/test.bak -n arc
```

### Step 3: Restore the database

Prepare and run the RESTORE command to restore the backup file to the Azure SQL managed instance - Azure Arc

```sql
RESTORE DATABASE test FROM DISK = '/var/opt/mssql/data/<file name>.bak'
WITH MOVE '<database name>' to '/var/opt/mssql/data/<file name>.mdf'  
,MOVE '<database name>' to '/var/opt/mssql/data/<file name>_log.ldf'  
,RECOVERY  
,REPLACE  
,STATS = 5;  
GO
```

Example:

```sql
RESTORE DATABASE test FROM DISK = '/var/opt/mssql/data/test.bak'
WITH MOVE 'test' to '/var/opt/mssql/data/test.mdf'  
,MOVE 'test' to '/var/opt/mssql/data/test_log.ldf'  
,RECOVERY  
,REPLACE  
,STATS = 5;  
GO
```


## Next steps

[Learn more about Features and Capabilities of Azure Arc enabled SQL Managed Instance](managed-instance-features.md)

[Start by creating a Data Controller](create-data-controller.md)

[Already created a Data Controller? Create an Azure Arc enabled SQL Managed Instance](create-sql-managed-instance.md)