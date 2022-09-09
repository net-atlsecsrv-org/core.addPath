---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
---
Next, if any servers on the cluster are running Windows Server 2008 R2 or Windows Server 2012, you must verify that the hotfix [KB2854082](https://support.microsoft.com/kb/2854082) is installed on each of the on-premises servers or Azure VMs that are part of the cluster. Any server or VM that is in the cluster, but not in the availability group, should also have this hotfix installed.

In the remote desktop session for each of the cluster nodes, download [KB2854082](https://support.microsoft.com/kb/2854082) to a local directory. Then, install the hotfix on each cluster node sequentially. If the cluster service is currently running on the cluster node, the server is restarted at the end of the hotfix installation.

> [!WARNING]
> Stopping the cluster service or restarting the server affects the quorum health of your cluster and the availability group, and it might cause your cluster to go offline. To maintain the high availability of your cluster during installation, make sure that:
> 
> * The cluster is in optimal quorum health. 
> * Before you install the hotfix on any node, all cluster nodes are online.
> * Before you install the hotfix on any other node in the cluster, allow the hotfix installation to run to completion on one node, including fully restarting the server.
> 
> 

