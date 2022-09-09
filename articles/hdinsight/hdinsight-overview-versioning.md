---
title: Versioning introduction - Azure HDInsight
description: Learn how versioning works in Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
author: deshriva
ms.author: deshriva
ms.date: 02/08/2021
---

# How versioning works in HDInsight

HDInsight service has two main components: a Resource provider and Apache Hadoop components that are deployed on a cluster. 

## HDInsight Resource provider

Resources in Azure are made available by a Resource provider. HDInsight Resource provider is responsible for creating, managing, and deleting clusters.

## HDInsight images

HDInsight uses images to put together open-source software (OSS) components that can be deployed on a cluster. These images contain the base Ubuntu operating system and core components such as Spark, Hadoop, Kafka, HBase or Hive.

## Versioning in HDInsight

Microsoft periodically upgrades the images and the HDInsight Resource provider to include new improvements and features.

New HDInsight version may be created when one or more of the following are true:

- Major changes or updates to HDInsight Resource provider functionality
- Major releases of OSS components
- Major changes to Ubuntu operating system

A new image version is created when one or more of the following are true:

- Major or minor releases and updates of OSS components
- Patches or fixes for a component in the image

## Next steps

- [Apache components and versions in HDInsight](./hdinsight-component-versioning.md)
