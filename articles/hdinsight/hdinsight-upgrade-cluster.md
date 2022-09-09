---
title: Upgrade HDInsight cluster to a newer version -Azure 
description: Learn guidelines to upgrade your Azure HDInsight cluster to a newer version.
author: omidm1
ms.author: omidm
ms.reviewer: jasonh 
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
---

# Upgrade HDInsight cluster to a newer version

To take advantage of the latest HDInsight features, we recommend that HDInsight clusters be upgraded to latest version. Follow the below guidelines to upgrade your HDInsight cluster versions.

> [!NOTE]  
> For information on supported versions of HDInsight, see [HDInsight component versions](hdinsight-component-versioning.md#supported-hdinsight-versions).

## Upgrade tasks

The workflow to upgrade HDInsight Cluster is as follows.
![HDInsight upgrade workflow diagram](./media/hdinsight-upgrade-cluster/upgrade-workflow-diagram.png)

1. Read each section of this document to understand changes that may be required when upgrading your HDInsight cluster.
2. Create a cluster as a test/quality assurance environment. For more information on creating a cluster, see [Learn how to create Linux-based HDInsight clusters](hdinsight-hadoop-provision-linux-clusters.md)
3. Copy existing jobs, data sources, and sinks to the new environment.
4. Perform validation testing to make sure that your jobs work as expected on the new cluster.

Once you have verified that everything works as expected, schedule downtime for the migration. During this downtime, do the following actions:

1. Back up any transient data stored locally on the cluster nodes. For example, if you have data stored directly on a head node.
1. [Delete the existing cluster](./hdinsight-delete-cluster.md).
1. Create a cluster in the same VNET subnet with latest (or supported) HDI version using the same default data store that the previous cluster used. This allows the new cluster to continue working against your existing production data.
1. Import any transient data you backed up.
1. Start jobs/continue processing using the new cluster.

## Next Steps

* [Learn how to create Linux-based HDInsight clusters](hdinsight-hadoop-provision-linux-clusters.md)
* [Connect to HDInsight using SSH](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Manage a Linux-based cluster using Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [HDInsight release notes](./hdinsight-version-release.md)
