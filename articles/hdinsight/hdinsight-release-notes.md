---
title: Release notes for Azure HDInsight 
description: Latest release notes for Azure HDInsight. Get development tips and details for Hadoop, Spark, R Server, Hive, and more.
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/08/2021
---
# Azure HDInsight release notes

This article provides information about the **most recent** Azure HDInsight release updates. For information on earlier releases, see [HDInsight Release Notes Archive](hdinsight-release-notes-archive.md).

## Summary

Azure HDInsight is one of the most popular services among enterprise customers for open-source analytics on Azure.

If you would like to subscribe on release notes, watch releases on [this GitHub repository](https://github.com/hdinsight/release-notes/releases).

## Release date: 02/05/2021

This release applies for both HDInsight 3.6 and HDInsight 4.0. HDInsight release is made available to all regions over several days. The release date here indicates the first region release date. If you don't see below changes, wait for the release being live in your region in several days.

## New features
### Dav4-series support
HDInsight added Dav4-series support in this release. Learn more about [Dav4-series here](/azure/virtual-machines/dav4-dasv4-series).

### Kafka REST Proxy GA 
Kafka REST Proxy enables you to interact with your Kafka cluster via a REST API over HTTPS. Kafka Rest Proxy is general available starting from this release. Learn more about [Kafka REST Proxy here](/azure/hdinsight/kafka/rest-proxy).

### Moving to Azure virtual machine scale sets
HDInsight now uses Azure virtual machines to provision the cluster. The service is gradually migrating to [Azure virtual machine scale sets](../virtual-machine-scale-sets/overview.md). The entire process may take months. After your regions and subscriptions are migrated, newly created HDInsight clusters will run on virtual machine scale sets without customer actions. No breaking change is expected.

## Deprecation
### Disabled VM sizes
Starting form January 9 2021, HDInsight will block all customers creating clusters using standand_A8, standand_A9, standand_A10 and standand_A11 VM sizes. Existing clusters will run as is. Consider moving to HDInsight 4.0 to avoid potential system/support interruption.

## Behavior changes
### Default cluster VM size changes to Ev3-series 
Default cluster VM sizes will be changed from D-series to Ev3-series. This change applies to head nodes and worker nodes. To avoid this change impacting your tested workflows, specify the VM sizes that you want to use in the ARM template.

### Network interface resource not visible for clusters running on Azure virtual machine scale sets
HDInsight is gradually migrating to Azure virtual machine scale sets. Network interfaces for virtual machines are no longer visible to customers for clusters that use Azure virtual machine scale sets.


### Breaking change for .NET for Apache Spark 1.0.0
With the latest release, HDInsight introduces the first official version v1.0.0 of the [“.NET for Apache Spark”](https://github.com/dotnet/spark) library. It provides DataFrame API completeness for Spark 2.4.x and Spark 3.0.x along with a host of [other features](https://github.com/dotnet/spark/blob/master/docs/release-notes/1.0.0/release-1.0.0.md). There will be breaking changes for this major version, refer to [the .NET for Apache Spark migration guide](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10) to understand steps needed to update your code and pipelines. To learn more, refer to this [.NET for Apache Spark v1.0 on Azure HDInsight guide](/azure/hdinsight/spark/spark-dotnet-version-update#using-net-for-apache-spark-v10-in-hdinsight).


## Upcoming changes
The following changes will happen in upcoming releases.

### Default cluster version will be changed to 4.0
Starting February 2021, the default version of HDInsight cluster will be changed from 3.6 to 4.0. For more information about available versions, see [available versions](./hdinsight-component-versioning.md). Learn more about what is new in [HDInsight 4.0](./hdinsight-version-release.md).

### OS version upgrade
HDInsight is upgrading OS version from Ubuntu 16.04 to 18.04. The upgrade will complete before April 2021.

### HDInsight 3.6 end of support on June 30 2021
HDInsight 3.6 will be end of support. Starting form June 30 2021, customers can't create new HDInsight 3.6 clusters. Existing clusters will run as is without the support from Microsoft. Consider moving to HDInsight 4.0 to avoid potential system/support interruption.

## Bug fixes
HDInsight continues to make cluster reliability and performance improvements. 

## Component version change
No component version change for this release. You can find the current component versions for HDInsight 4.0 and HDInsight 3.6 in [this doc](./hdinsight-component-versioning.md).
