---
title: Apache Hadoop components and versions - Azure HDInsight 
description: Learn the Apache Hadoop components and versions in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 04/09/2020
---

# What are the Apache Hadoop components and versions available with HDInsight?

Learn about the [Apache Hadoop](https://hadoop.apache.org/) environment components and versions in Microsoft Azure HDInsight, and the Enterprise Security Package. Also, learn how to check Hadoop component versions in HDInsight.

## Apache Hadoop components available with different HDInsight versions

Azure HDInsight supports multiple Hadoop cluster versions that can be deployed at any time. On April 4, 2017, the default cluster version used by Azure HDInsight is 3.6.

The component versions associated with HDInsight cluster versions are listed in the following table:

> [!NOTE]  
> The default version for the HDInsight service might change without notice. If you have a version dependency, specify the HDInsight version when you create your clusters with the .NET SDK with Azure PowerShell and Azure Classic CLI.

| Component              | HDInsight 4.0 | HDInsight 3.6 (Default)     |
|------------------------|---------------|-----------------------------|
| Apache Hadoop and YARN | 3.1.1         | 2.7.3                       |
| Apache Tez             | 0.9.1         | 0.7.0                       |
| Apache Pig             | 0.16.0        | 0.16.0                      |
| Apache Hive            | 3.1.0         | 1.2.1 (2.1.0 on ESP Interactive Query) |
| Apache Tez Hive2       | -             | 0.8.4                       |
| Apache Ranger          | 1.1.0         | 0.7.0                       |
| Apache HBase           | 2.0.2         | 1.1.2                       |
| Apache Sqoop           | 1.4.7         | 1.4.6                       |
| Apache Oozie           | 4.3.1         | 4.2.0                       |
| Apache Zookeeper       | 3.4.6         | 3.4.6                       |
| Apache Storm           | -             | 1.1.0                       |
| Apache Mahout          | -             | 0.9.0+                      |
| Apache Phoenix         | 5             | 4.7.0                       |
| Apache Spark           | 2.3.1, 2.4    | 2.3.0, 2.2.0, 2.1.0         |
| Apache Livy            | 0.5           | 0.4, 0.4, 0.3               |
| Apache Kafka           | 1.1.1, 2.1    | 1.1, 1.0 * (See Note below) |
| Apache Ambari          | 2.7.0         | 2.6.0                       |
| Apache Zeppelin        | 0.8.0         | 0.7.3                       |
| Mono                   | 4.2.1         | 4.2.1                       |

> [!NOTE]
> Due to system performance considerations, support for Kafka version 0.10 was expired in March 2019.

## Check for current Hadoop component version information

The Hadoop environment component versions associated with HDInsight cluster versions can change with updates to HDInsight. To check the Hadoop components and to verify which versions are being used for a cluster, use the Ambari REST API. The **GetComponentInformation** command retrieves information about service components. For details, see the [Apache Ambari documentation](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

### Release notes

See [HDInsight release notes](hdinsight-release-notes.md) for additional release notes on the latest versions of HDInsight.

## Supported HDInsight versions

### Support expiration and retirement for HDInsight versions

**Support expiration** means Microsoft will no longer provide support for the specified HDInsight version. And it will no longer be available through the Azure portal for cluster creation. However, these versions can still be created using the Azure CLI or the various SDKs.

**Retirement** of an HDInsight version means that existing clusters will continue to run as-is. However, new clusters of this version can't be created through any means (including CLI and SDKs). Other control plane features (such as manual scaling and Autoscaling) may also not work after version retirement. Support isn't available for retired versions.

The following tables list the versions of HDInsight. The support expiration and retirement dates are also provided, when they're known.

### Available versions

The following table lists the versions of HDInsight that are available in the Azure portal and other deployment methods like PowerShell and .NET SDK.

| HDInsight version | VM OS | Release date | Support expiration date | Retirement date | High availability |  Availability in the Azure portal |
| --- | --- | --- | --- | --- | --- | --- |
| HDInsight 4.0 |Ubuntu 16.0.4 LTS |September 24, 2018 | | |Yes |Yes |
| HDInsight 3.6 |Ubuntu 16.0.4 LTS |April 4, 2017 | December 31, 2020 |December 31, 2020 |Yes |Yes |

Spark 2.1, 2.2 & Kafka 1.0 support will expire on June 30, 2020.

> [!NOTE]  
> After support for a version has expired, it might not be available through the Microsoft Azure portal. However, cluster versions continue to be available using the `Version` parameter in the Windows PowerShell [New-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) command and the .NET SDK until the version retirement date.

### Retired versions

The following table lists the versions of HDInsight that **aren't** available in the Azure portal.

| HDInsight version | HDP version | VM OS | Release date | Support expiration date | Retirement date | High availability |  Availability on the Azure portal |
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 3.5 |HDP 2.5 |Ubuntu 16.0.4 LTS |September 30, 2016 |September 5, 2017 |June 28, 2018 |Yes |No |
| HDInsight 3.4 |HDP 2.4 |Ubuntu 14.0.4 LTS |March 29, 2016 |December 29, 2016 |January 9, 2018 |Yes |No |
| HDInsight 3.3 |HDP 2.3 |Windows Server 2012 R2 |December 2, 2015 |June 27, 2016 |July 31, 2018 |Yes |No |
| HDInsight 3.3 |HDP 2.3 |Ubuntu 14.0.4 LTS |December 2, 2015 |June 27, 2016 |July 31, 2017 |Yes |No |
| HDInsight 3.2 |HDP 2.2 |Ubuntu 12.04 LTS or Windows Server 2012 R2 |February 18, 2015 |March 1, 2016 |April 1, 2017 |Yes |No |
| HDInsight 3.1 |HDP 2.1 |Windows Server 2012 R2 |June 24, 2014 |May 18, 2015 |June 30, 2016 |Yes |No |
| HDInsight 3.0 |HDP 2.0 |Windows Server 2012 R2 |February 11, 2014 |September 17, 2014 |June 30, 2015 |Yes |No |
| HDInsight 2.1 |HDP 1.3 |Windows Server 2012 R2 |October 28, 2013 |May 12, 2014 |May 31, 2015 |Yes |No |
| HDInsight 1.6 |HDP 1.1 | |October 28, 2013 |April 26, 2014 |May 31, 2015 |No |No |

> [!NOTE]  
> Highly available clusters with two head nodes are deployed by default for HDInsight version 2.1 and later. They are not available for HDInsight version 1.6 clusters.

## Enterprise Security Package for HDInsight

Enterprise Security is an optional package that you can add on your HDInsight cluster as part of create cluster workflow. The Enterprise Security Package supports:

- Integration with Active Directory for authentication.

    In the past, you created HDInsight clusters with local admin user and local SSH user. The local admin user can access all the files, folders, tables, and columns.  With  Enterprise Security Package, you enable role-based access control by integrating HDInsight with your Active Directory. Which includes on-premises Active Directory, Azure Active Directory Domain Services. Or Active Directory on IaaS virtual machine. Domain administrator on the cluster can grant users to use their own corporate (domain) user-name and password.

    For more information, see:

    - [An introduction to Apache Hadoop security with domain-joined HDInsight clusters](./domain-joined/hdinsight-security-overview.md)
    - [Plan Azure domain-joined Apache Hadoop clusters in HDInsight](./domain-joined/apache-domain-joined-architecture.md)
    - [Configure domain-joined sandbox environment](./domain-joined/apache-domain-joined-configure.md)
    - [Configure Domain-joined HDInsight clusters using Azure Active Directory Domain Services](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)

- Authorization for data

  - Integration with Apache Ranger for authorization for Hive, Spark SQL, and Yarn Queues.
  - You can set access control on files and folders.

    For more information, see:

  - [Configure Apache Hive policies in Domain-joined HDInsight](./domain-joined/apache-domain-joined-run-hive.md)

- View the audit logs to monitor accesses and the configured policies.

### Supported cluster types

Currently, only the following cluster types support the Enterprise Security Package:

- Hadoop (HDInsight 3.6 only)
- Spark
- Kafka
- HBase
- Interactive Query

### Support for Azure Data Lake Storage

The Enterprise Security Package supports using Azure Data Lake Storage as both the primary storage and the add-on storage.

### Pricing and service level agreement (SLA)

For information on pricing and SLA for the Enterprise Security Package, see [HDInsight pricing](https://azure.microsoft.com/pricing/details/hdinsight/).

## Service level agreement for HDInsight cluster versions

The service level agreement (SLA) is defined as a _support window_. Support window is the time period an HDInsight  version is supported by `Microsoft Customer Service and Support`. If the version has a passed _support expiration date_, the HDInsight cluster is outside the support window. Support expiration for HDInsight version X (after a newer X+1 version is available) is the later:  

- Formula 1: Add 180 days to the date when the HDInsight cluster version X was released.
- Formula 2: Add 90 days to the date when the HDInsight cluster version X+1 is made available in Azure portal.

The _retirement date_ is the date after which the cluster version can't be created on HDInsight. Starting July 31, 2017, you can't resize an HDInsight cluster after its retirement date.

## Default node configuration and virtual machine sizes for clusters

For more information on which virtual machine SKUs to select for your cluster, see [Azure HDInsight cluster configuration details](hdinsight-supported-node-configuration.md).

## Next steps

- [Cluster setup for Apache Hadoop, Spark, and more on HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Work in Apache Hadoop on HDInsight from a Windows PC](hdinsight-hadoop-windows-tools.md)
- [Hortonworks release notes associated with Azure HDInsight versions](./hortonworks-release-notes.md)
