---
title: BindException - Address already in use in Azure HDInsight
description: BindException - Address already in use in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/08/2019
---

# Scenario: BindException - Address already in use in Azure HDInsight

This article describes troubleshooting steps and possible resolutions for issues when interacting with Azure HDInsight clusters.

## Issue

The restart operation on an Apache HBase Region Server fails to complete. From the `region-server.log` in `/var/log/hbase` directory on the worker nodes where region server start fails, you may see an error message similar as follows:

```
Caused by: java.net.BindException: Problem binding to /10.2.0.4:16020 : Address already in use
...

Caused by: java.net.BindException: Address already in use
...
```

## Cause

Restarting HBase Region Servers during heavy workload activity. Below is what happens behind the scenes when a user initiates the restart operation on HBase region server's from Ambari UI:

1. Ambari agent will send a stop request to region server.

1. The Ambari agent then waits for 30 seconds for region server to shut down gracefully.

1. If the application continues to connect with region server, it will not shut down immediately and hence 30-seconds timeout will expire sooner.

1. After expiration of 30 seconds, Ambari agent will send a force kill (kill -9) to region server.

1. Due to this abrupt shutdown, although the region server process gets killed, the port associated with the process may not be released, which eventually leads to `AddressBindException`.

## Resolution

Reduce the load on the HBase region servers before initiating a restart.

Alternatively, try and manually restart region servers on the worker nodes using following commands:

```bash
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"
```

## Next steps

If you didn't see your problem or are unable to solve your issue, visit one of the following channels for more support:

* Get answers from Azure experts through [Azure Community Support](https://azure.microsoft.com/support/community/).

* Connect with [@AzureSupport](https://twitter.com/azuresupport) - the official Microsoft Azure account for improving customer experience. Connecting the Azure community to the right resources: answers, support, and experts.

* If you need more help, you can submit a support request from the [Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Select **Support** from the menu bar or open the **Help + support** hub. For more detailed information, review [How to create an Azure support request](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Access to Subscription Management and billing support is included with your Microsoft Azure subscription, and Technical Support is provided through one of the [Azure Support Plans](https://azure.microsoft.com/support/plans/).
