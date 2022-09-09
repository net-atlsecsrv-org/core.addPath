---
title: Storage configuration
description: Explains Azure Arc enabled data services storage configuration options
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: uc-msft
ms.author: umajay
ms.reviewer: mikeray
ms.date: 10/12/2020
ms.topic: conceptual
---

# Storage Configuration

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## Kubernetes storage concepts

Kubernetes provides an infrastructure abstraction layer over the underlying virtualization tech stack (optional) and hardware. The way that Kubernetes abstracts away storage is through **[Storage Classes](https://kubernetes.io/docs/concepts/storage/storage-classes/)**. At the time of provisioning a pod, a storage class can be specified to be used for each volume. At the time the pod is provisioned, the storage class **[provisioner](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/)** is called to provision the storage and then a **[persistent volume](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)** is created on that provisioned storage and then the pod is mounted to the persistent volume by a **[persistent volume claim](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistentvolumeclaims)**.

Kubernetes provides a way for storage infrastructure providers to plug in drivers (also called "Addons") that extend Kubernetes. Storage addons must comply with the **[Container Storage Interface standard](https://kubernetes.io/blog/2019/01/15/container-storage-interface-ga/)**. There are dozens of addons that can be found in this non-definitive **[list of CSI drivers](https://kubernetes-csi.github.io/docs/drivers.html)**. Which CSI driver you use will depend on factors such as whether you are running in a cloud-hosted, managed Kubernetes service or which OEM provider you are using for your hardware.

You can view which storage classes are configured in your Kubernetes cluster by running this command:

```console
kubectl get storageclass
```

Example output from an Azure Kubernetes Service (AKS) cluster:

```console
NAME                PROVISIONER                AGE
azurefile           kubernetes.io/azure-file   15d
azurefile-premium   kubernetes.io/azure-file   15d
default (default)   kubernetes.io/azure-disk   4d3h
managed-premium     kubernetes.io/azure-disk   4d3h
```

You can get details about a storage class by running this command:

```console
kubectl describe storageclass/<storage class name>
```

Example:

```console
kubectl describe storageclass/azurefile

Name:            azurefile
IsDefaultClass:  No
Annotations:     kubectl.kubernetes.io/last-applied-configuration={"allowVolumeExpansion":true,"apiVersion":"storage.k8s.io/v1beta1","kind":"StorageClass","metadata":{"annotations":{},"labels":{"kubernetes.io/cluster-service":"true"},"name":"azurefile"},"parameters":{"sku
Name":"Standard_LRS"},"provisioner":"kubernetes.io/azure-file"}

Provisioner:           kubernetes.io/azure-file
Parameters:            skuName=Standard_LRS
AllowVolumeExpansion:  True
MountOptions:          <none>
ReclaimPolicy:         Delete
VolumeBindingMode:     Immediate
Events:                <none>
```

You can see the currently provisioned persistent volumes and persistent volume claims by running the following commands:

```console
kubectl get persistentvolumes -n <namespace>

kubectl get persistentvolumeclaims -n <namespace>
```

Example of showing persistent volumes:

```console

kubectl get persistentvolumes -n arc

NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                      STORAGECLASS   REASON   AGE
pvc-07fc7b9f-9a37-4796-9442-4405147120da   15Gi       RWO            Delete           Bound    arc/sqldemo11-data-claim   default                 7d3h
pvc-3e772f20-ed89-4642-b34d-8bb11b088afa   15Gi       RWO            Delete           Bound    arc/data-metricsdb-0       default                 7d14h
pvc-41b33bbd-debb-4153-9a41-02ce2bf9c665   10Gi       RWO            Delete           Bound    arc/sqldemo11-logs-claim   default                 7d3h
pvc-4ccda3e4-fee3-4a89-b92d-655c04fa62ad   15Gi       RWO            Delete           Bound    arc/data-controller        default                 7d14h
pvc-63e6bb4c-7240-4de5-877e-7e9ea4e49c91   10Gi       RWO            Delete           Bound    arc/logs-controller        default                 7d14h
pvc-8a1467fe-5eeb-4d73-b99a-f5baf41eb493   10Gi       RWO            Delete           Bound    arc/logs-metricsdb-0       default                 7d14h
pvc-8e2cacbc-e953-4901-8591-e77df9af309c   10Gi       RWO            Delete           Bound    arc/sqldemo10-logs-claim   default                 7d14h
pvc-9fb79ba3-bd3e-42aa-aa09-3090135d4513   15Gi       RWO            Delete           Bound    arc/sqldemo10-data-claim   default                 7d14h
pvc-a39c85d4-5cd9-4249-9915-68a70a9bb5e5   15Gi       RWO            Delete           Bound    arc/data-controldb         default                 7d14h
pvc-c9cbd74a-76ca-4be5-b598-0c7a45749bfb   10Gi       RWO            Delete           Bound    arc/logs-controldb         default                 7d14h
pvc-d576e9d4-0a09-4dd7-b806-be8ed461f8a4   10Gi       RWO            Delete           Bound    arc/logs-logsdb-0          default                 7d14h
pvc-ecd7d07f-2c2c-421d-98d7-711ec5d4a0cd   15Gi       RWO            Delete           Bound    arc/data-logsdb-0          default                 7d14h
```

Example of showing persistent volume claims:

```console

kubectl get persistentvolumeclaims -n arc

NAME                   STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
data-controldb         Bound    pvc-a39c85d4-5cd9-4249-9915-68a70a9bb5e5   15Gi       RWO            default        7d14h
data-controller        Bound    pvc-4ccda3e4-fee3-4a89-b92d-655c04fa62ad   15Gi       RWO            default        7d14h
data-logsdb-0          Bound    pvc-ecd7d07f-2c2c-421d-98d7-711ec5d4a0cd   15Gi       RWO            default        7d14h
data-metricsdb-0       Bound    pvc-3e772f20-ed89-4642-b34d-8bb11b088afa   15Gi       RWO            default        7d14h
logs-controldb         Bound    pvc-c9cbd74a-76ca-4be5-b598-0c7a45749bfb   10Gi       RWO            default        7d14h
logs-controller        Bound    pvc-63e6bb4c-7240-4de5-877e-7e9ea4e49c91   10Gi       RWO            default        7d14h
logs-logsdb-0          Bound    pvc-d576e9d4-0a09-4dd7-b806-be8ed461f8a4   10Gi       RWO            default        7d14h
logs-metricsdb-0       Bound    pvc-8a1467fe-5eeb-4d73-b99a-f5baf41eb493   10Gi       RWO            default        7d14h
sqldemo10-data-claim   Bound    pvc-9fb79ba3-bd3e-42aa-aa09-3090135d4513   15Gi       RWO            default        7d14h
sqldemo10-logs-claim   Bound    pvc-8e2cacbc-e953-4901-8591-e77df9af309c   10Gi       RWO            default        7d14h
sqldemo11-data-claim   Bound    pvc-07fc7b9f-9a37-4796-9442-4405147120da   15Gi       RWO            default        7d4h
sqldemo11-logs-claim   Bound    pvc-41b33bbd-debb-4153-9a41-02ce2bf9c665   10Gi       RWO            default        7d4h

```

## Factors to consider when choosing your storage configuration

Selecting the right storage class is important to data resiliency and performance. Choosing the wrong storage class can put your data at risk of total data loss in the event of a hardware failure or could result in less optimal performance.

There are generally two types of storage:

- **Local storage** - storage provisioned on local hard drives on a given node. This kind of storage can be ideal in terms of performance, but requires specifically designing for data redundancy by replicating the data across multiple nodes.
- **Remote, shared storage** - storage provisioned on some remote storage device - for example, a SAN, NAS, or cloud storage service like EBS or Azure Files. This kind of storage generally provides for data redundancy automatically, but is not as fast as local storage can be.

> [!NOTE]
> For now, if you are using NFS, you need to set allowRunAsRoot to true in your deployment profile file before deploying the Azure Arc data controller.

### Data controller storage configuration

Some services in Azure Arc for data services depend upon being configured to use remote, shared storage because the services don't have an ability to replicate the data. These services are found in the collection of data controller pods:

|**Service**|**Persistent Volume Claims**|
|---|---|
|**ElasticSearch**|`<namespace>/logs-logsdb-0`, `<namespace>/data-logsdb-0`|
|**InfluxDB**|`<namespace>/logs-metricsdb-0`, `<namespace>/data-metricsdb-0`|
|**Controller SQL instance**|`<namespace>/logs-controldb`, `<namespace>/data-controldb`|
|**Controller API service**|`<namespace>/data-controller`|

At the time the data controller is provisioned, the storage class to be used for each of these persistent volumes is specified by either passing the --storage-class | -sc parameter to the `azdata arc dc create` command or by setting the storage classes in the control.json deployment template file that is used.

The deployment templates that are provided out of the box have a default storage class specified that is appropriate for the target environment, but it can be overridden during deployment. See the detailed steps to [alter the deployment profile](create-data-controller.md) to change the storage class configuration for the data controller pods at deployment time.

If you set the storage class using the --storage-class | -sc parameter the storage class will be used for both log and data storage classes. If you set the storage classes in the deployment template file, you can specify different storage classes for logs and data.

Important factors to consider when choosing a storage class for the data controller pods:

- You **must** use a remote, shared storage class in order to ensure data durability and so that if a pod or node dies that when the pod is brought back up it can connect again to the persistent volume.
- The data being written to the controller SQL instance, metrics DB, and logs DB is typically fairly low volume and not sensitive to latency so ultra-fast performance storage is not critical. If you have users that are frequently using the Grafana and Kibana interfaces and you have a large number of database instances, then your users might benefit from faster performing storage.
- The storage capacity required is variable with the number of database instances that you have deployed because logs and metrics are collected for each database instance. Data is retained in the logs and metrics DB for two (2) weeks before it is purged. 
- Changing the storage class post deployment is difficult, not documented, and not supported. Be sure to choose the storage class correctly at deployment time.

> [!NOTE]
> If no storage class is specified the default storage class will be used. There can be only one default storage class per Kubernetes cluster. You can [change the default storage class](https://kubernetes.io/docs/tasks/administer-cluster/change-default-storage-class/).

### Database instance storage configuration

Each database instance has data, logs, and backup persistent volumes. The storage classes for these persistent volumes can be specified at deployment time. If no storage class is specified the default storage class will be used.

When creating an instance using either `azdata arc sql mi create` or `azdata arc postgres server create`, there are two parameters that can be used to set the storage classes:

> [!NOTE]
> Some of these parameters are in development and will become available on `azdata arc sql mi create` and `azdata arc postgres server create` in the upcoming releases.

|Parameter name, short name|Used for|
|---|---|
|`--storage-class-data`, `-scd`|Used to specify the storage class for all data files including transaction log files|
|`--storage-class-logs`, `-scl`|Used to specify the storage class for all log files|
|`--storage-class-data-logs`, `-scdl`|Used to specify the storage class for the database transaction log files. **Note: Not available yet.**|
|`--storage-class-backups`, `-scb`|Used to specify the storage class for all backup files. **Note: Not available yet.**|

The table below lists the paths inside the Azure SQL Managed Instance container that is mapped to the persistent volume for data and logs:

|Parameter name, short name|Path inside mssql-miaa container|Description|
|---|---|---|
|`--storage-class-data`, `-scd`|/var/opt|Contains directories for the mssql installation and other system processes. The mssql directory contains default data (including transaction logs), error log & backup directories|
|`--storage-class-logs`, `-scl`|/var/log|Contains directories that store console output (stderr, stdout), other logging information of processes inside the container|

The table below lists the paths inside the PostgreSQL instance container that is mapped to the persistent volume for data and logs:

|Parameter name, short name|Path inside postgres container|Description|
|---|---|---|
|`--storage-class-data`, `-scd`|/var/opt/postgresql|Contains data and log directories for the postgres installation|
|`--storage-class-logs`, `-scl`|/var/log|Contains directories that store console output (stderr, stdout), other logging information of processes inside the container|

Each database instance will have a separate persistent volume for data files, logs, and backups. This means that there will be separation of the I/O for each of these types of files subject to how the volume provisioner will provision storage. Each database instance has its own persistent volume claims and persistent volumes.

If there are multiple databases on a given database instance, all of the databases will use the same persistent volume claim, persistent volume, and storage class. All backups - both differential log backups and full backups will use the same persistent volume claim and persistent volume. The persistent volume claims for the database instance pods are shown below:

|**Instance**|**Persistent Volume Claims**|
|---|---|
|**Azure SQL Managed Instance**|`<namespace>/logs-<instance name>-0`, `<namespace>/data-<instance name>-0`|
|**Azure database for PostgreSQL instance**|`<namespace>/logs--<instance name>-0`, `<namespace>/data--<instance name>-0`|
|**Azure PostgreSQL HyperScale**|`<namespace>/logs-<instance namme>-<ordinal>`, `<namespace>/data-<instance namme>-<ordinal>` *(Ordinal ranges from 0 to W where W is the number of workers)*|

Important factors to consider when choosing a storage class for the database instance pods:

- Database instances can be deployed in either a single pod pattern or a multiple pod pattern. An example of a single pod pattern is a developer instance of Azure SQL managed instance or a general purpose pricing tier Azure SQL managed instance. An example of a multiple pod pattern is a highly available business critical pricing tier Azure SQL managed instance. (Note: pricing tiers are in development and not available to customers yet.)  Database instances deployed with the single pod pattern **must** use a remote, shared storage class in order to ensure data durability and so that if a pod or node dies that when the pod is brought back up it can connect again to the persistent volume. In contrast, a highly available Azure SQL managed instance uses Always On Availability Groups to replicate the data from one instance to another either synchronously or asynchronously. Especially in the case where the data is replicated synchronously, there is always multiple copies of the data - typically three(3) copies. Because of this, it is possible to use local storage or remote, shared storage classes for data and log files. If utilizing local storage, the data is still preserved even in the case of a failed pod, node, or storage hardware. Given this flexibility, you might choose to use local storage for better performance.
- Database performance is largely a function of the I/O throughput of a given storage device. If your database is heavy reads or heavy writes, then you should choose a storage class with hardware designed for that type of workload. For example, if your database is mostly used for writes, you might choose local storage with RAID 0. If your database is mostly used for reads of a small amount of "hot data", but there is a large overall storage volume of cold data, then you might choose a SAN device capable of tiered storage. Choosing the right storage class is not that much different than choosing the type of storage you would use for any database.
- If you are using a local storage volume provisioner, ensure that the local volumes that are provisioned for data, logs, and backups are each landing on different underlying storage devices to avoid contention on disk I/O. The OS should also be on a volume that is mounted to a separate disk(s). This is essentially the same guidance as would be followed for a database instance on physical hardware.
- Because all databases on a given instance share a persistent volume claim and persistent volume, be sure not to colocate busy database instances on the same database instance. If possible, separate busy databases on to their own database instances to avoid I/O contention. Further, use node label targeting to land database instances onto separate nodes so as to distribute overall I/O traffic across multiple nodes. If you are using virtualization, be sure to consider distributing I/O traffic not just at the node level but also the combined I/O activity happening by all the node VMs on a given physical host.

## Estimating storage requirements
Every pod that contains stateful data uses two persistent volumes in this release - one persistent volume for data and another persistent volume for logs. The table below lists the number of persistent volumes required for a single Data Controller, Azure SQL Managed instance, Azure Database for PostgreSQL instance and Azure PostgreSQL HyperScale instance:

|Resource Type|Number of stateful pods|Required number of persistent volumes|
|---|---|---|
|Data Controller|4 (`control`, `controldb`, `logsdb`, `metricsdb`)|4 * 2 = 8|
|Azure SQL Managed Instance|1|2|
|Azure Database for PostgreSQL instance|1| 2|
|Azure PostgreSQL HyperScale|1 + W (W = number of workers)|2 * (1 + W)|

The table below shows the total number of persistent volume required for a sample deployment:

|Resource Type|Number of instances|Required number of persistent volumes|
|---|---|---|
|Data Controller|1|4 * 2 = 8|
|Azure SQL Managed Instance|5|5 * 2 = 10|
|Azure Database for PostgreSQL instance|5| 5 * 2 = 10|
|Azure PostgreSQL HyperScale|2 (Number of workers = 4 per instance)|2 * 2 * (1 + 4) = 20|
|***Total Number of persistent volumes***||8 + 10 + 10 + 20 = 48|

This calculation can be used to plan the storage for your Kubernetes cluster based on the storage provisioner or environment. For example, if local storage provisioner is used for a Kubernetes cluster with five (5) nodes then for the sample deployment above every node requires at least storage for 10 persistent volumes. Similarly, when provisioning an Azure Kubernetes Service (AKS) cluster with five (5) nodes picking an appropriate VM size for the node pool such that 10 data disks can be attached is critical. More details on how to size the nodes for storage needs for AKS nodes can be found [here](../../aks/operator-best-practices-storage.md#size-the-nodes-for-storage-needs).

## Choosing the right storage class

### On-premises and edge sites

Microsoft and its OEM, OS, and Kubernetes partners are working on a certification program for Azure Arc data services. This program will provide customers comparable test results from a certification testing toolkit. The tests will evaluate feature compatibility, stress testing results, and performance and scalability. Each of these test results will indicate the OS used, Kubernetes distribution used, HW used, the CSI add-on used, and the storage classes used. This will help customers choose the best storage class, OS, Kubernetes distribution, and HW for their requirements. More information on this program and initial test results will be provided closer to the General Availability of Azure Arc data services.

#### Public cloud, managed Kubernetes services

For public cloud-based, managed Kubernetes services we can make the following recommendations:

|Public cloud service|Recommendation|
|---|---|
|**Azure Kubernetes Service (AKS)**|Azure Kubernetes Service (AKS) has two types of storage - Azure Files and Azure Managed Disks. Each type of storage has two pricing/performance tiers - standard (HDD) and premium (SSD). Thus, the four storage classes provided in AKS are `azurefile` (Azure Files standard tier), `azurefile-premium` (Azure Files premium tier), `default` (Azure Disks standard tier), and `managed-premium` (Azure Disks premium tier). The default storage class is `default` (Azure Disks standard tier). There are substantial **[pricing differences](https://azure.microsoft.com/en-us/pricing/details/storage/)** between the types and tiers which should be factored into your decision. For production workloads with high-performance requirements, we recommend using `managed-premium` for all storage classes. For dev/test workloads, proofs of concept, etc. where cost is a consideration, then `azurefile` is the least expensive option. All four of the options can be used for situations requiring remote, shared storage as they are all network-attached storage devices in Azure. Read more about [AKS Storage](../../aks/concepts-storage.md).|
|**AWS Elastic Kubernetes Service (EKS)**| Amazon's Elastic Kubernetes Service has one primary storage class - based on the [EBS CSI storage driver](https://docs.aws.amazon.com/eks/latest/userguide/ebs-csi.html). This is recommended for production workloads. There is a new storage driver - [EFS CSI storage driver](https://docs.aws.amazon.com/eks/latest/userguide/efs-csi.html) - that can be added to an EKS cluster, but it is currently in a beta stage and subject to change. Although AWS says that this storage driver is supported for production, we don't recommend using it because it is still in beta and subject to change. The EBS storage class is the default and is called `gp2`. Read more about [EKS Storage](https://docs.aws.amazon.com/eks/latest/userguide/storage-classes.html).|
|**Google Kubernetes Engine (GKE)**|Google Kubernetes Engine (GKE) has just one storage class called `standard`, which is used for [GCE persistent disks](https://kubernetes.io/docs/concepts/storage/volumes/#gcepersistentdisk). Being the only one, it is also the default. Although there is a [local, static volume provisioner](https://cloud.google.com/kubernetes-engine/docs/how-to/persistent-volumes/local-ssd#run-local-volume-static-provisioner) for GKE that you can use with direct-attached SSDs, we don't recommend using it as it is not maintained or supported by Google. Read more about [GKE storage](https://cloud.google.com/kubernetes-engine/docs/concepts/persistent-volumes).
