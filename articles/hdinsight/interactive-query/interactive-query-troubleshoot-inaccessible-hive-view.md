---
title: Apache Hive connections to Apache Zookeeper - Azure HDInsight
description: Apache Hive View inaccessible due to Apache Zookeeper issues in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
---

# Scenario: Apache Hive fails to establish a connection to Apache Zookeeper in Azure HDInsight

This article describes troubleshooting steps and possible resolutions for issues when using Interactive Query components in Azure HDInsight clusters.

## Issue

The Hive View is inaccessible, and the logs in `/var/log/hive` show an error similar to the following:

```
ERROR [Curator-Framework-0]: curator.ConnectionState (ConnectionState.java:checkTimeouts(200)) - Connection timed out for connection string (zk0-cluster.cloud.wbmi.com:2181,zk1-cluster.cloud.wbmi.com:2181,zk2-cluster.cloud.wbmi.com:2181) and timeout (15000) / elapsed (21852)
```

## Cause

It is possible that Hive may fail to establish a connection to Zookeeper, which prevents the Hive View from launching.

## Resolution

1. Check that the Zookeeper service is healthy.

1. Check if the Zookeeper service has a ZNode entry for Hive Server2. The value will be missing or incorrect.

    ```
    /usr/hdp/2.6.2.25-1/zookeeper/bin/zkCli.sh -server zk1-wbwdhs
    [zk: zk0-cluster(CONNECTED) 0] ls /hiveserver2-hive2
    ```

1. To re-establish connectivity, reboot the Zookeeper nodes, and reboot HiveServer2.

## Next steps

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]