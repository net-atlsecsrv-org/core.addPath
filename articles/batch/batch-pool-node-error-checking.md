---
title: Check for pool and node errors - Azure Batch
description: Errors to check for and how to avoid them when creating pools and nodes
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 07/16/2019
ms.topic: conceptual
---

# Check for pool and node errors

When you're creating and managing Azure Batch pools, some operations happen immediately. However, some operations are asynchronous and run in the background, taking several minutes to complete.

Detecting failures for operations that take place immediately is straightforward because any failures are returned immediately by the API, CLI, or UI.

This article covers the background operations that can occur for pools and pool nodes. It specifies how you can detect and avoid failures.

## Pool errors

### Resize timeout or failure

When creating a new pool or resizing an existing pool, you specify the target number of nodes.  The create or resize operation completes immediately, but the actual allocation of new nodes or the removal of existing nodes might take several minutes.  You specify the resize timeout in the [create](https://docs.microsoft.com/rest/api/batchservice/pool/add) or [resize](https://docs.microsoft.com/rest/api/batchservice/pool/resize) API. If Batch can't obtain the target number of nodes during the resize timeout period the pool goes into a steady state and reports resize errors.

The [ResizeError](https://docs.microsoft.com/rest/api/batchservice/pool/get#resizeerror) property for the most recent evaluation lists the errors that occurred.

Common causes for resize errors include:

- Resize timeout is too short
  - Under most circumstances, the default timeout of 15 minutes is long enough for pool nodes to be allocated or removed.
  - If you're allocating a large number of nodes, we recommend setting the resize timeout to 30 minutes. For example, when you're resizing to more than 1,000 nodes from an Azure Marketplace image, or to more than 300 nodes from a custom VM image.
- Insufficient core quota
  - A Batch account is limited in the number of cores that it can allocate across all pools. Batch stops allocating nodes once that quota has been reached. You [can increase](https://docs.microsoft.com/azure/batch/batch-quota-limit) the core quota so that Batch can allocate more nodes.
- Insufficient subnet IPs when a [pool is in a virtual network](https://docs.microsoft.com/azure/batch/batch-virtual-network)
  - A virtual network subnet must have enough unassigned IP addresses to allocate to every requested pool node. Otherwise, the nodes can't be created.
- Insufficient resources when a [pool is in a virtual network](https://docs.microsoft.com/azure/batch/batch-virtual-network)
  - You might create resources such as load-balancers, public IPs, and network security groups in the same subscription as the Batch account. Check that the subscription quotas are sufficient for these resources.
- Large pools with custom VM images
  - Large pools that use custom VM images can take longer to allocate and resize timeouts can occur.  See [Use a custom image to create a pool of virtual machines](https://docs.microsoft.com/azure/batch/batch-custom-images) for recommendations on limits and configuration.

### Automatic scaling failures

You can also set Azure Batch to automatically scale the number of nodes in a pool. You define the parameters for the [automatic scaling formula for a pool](https://docs.microsoft.com/azure/batch/batch-automatic-scaling). The Batch service uses the formula to periodically evaluate the number of nodes in the pool and set a new target number. The following types of issues can occur:

- The automatic scaling evaluation fails.
- The resulting resize operation fails and times out.
- A problem with the automatic scaling formula leads to incorrect node target values. The resize either works or times out.

You can get information about the last automatic scaling evaluation by using the [autoScaleRun](https://docs.microsoft.com/rest/api/batchservice/pool/get#autoscalerun) property. This property reports the evaluation time, the values and result, and any performance errors.

The [pool resize complete event](https://docs.microsoft.com/azure/batch/batch-pool-resize-complete-event) captures information about all evaluations.

### Delete

When you delete a pool that contains nodes, first Batch deletes the nodes. Then it deletes the pool object itself. It can take a few minutes for the pool nodes to be deleted.

Batch sets the [pool state](https://docs.microsoft.com/rest/api/batchservice/pool/get#poolstate) to **deleting** during the deletion process. The calling application can detect if the pool deletion is taking too long by using the **state** and **stateTransitionTime** properties.

## Pool compute node errors

Even when Batch successfully allocates nodes in a pool, various issues can cause some of the nodes to be unhealthy and unable to run tasks. These nodes still incur charges, so it's important to detect problems to avoid paying for nodes that can't be used.

### Start task failures

You might want to specify an optional [start task](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask) for a pool. As with any task, you can use a command line and resource files to download from storage. The start task is run for each node after it's been started. The **waitForSuccess** property specifies whether Batch waits until the start task completes successfully before it schedules any tasks to a node.

What if you've configured the node to wait for successful start task completion, but the start task fails? In that case, the node will not be usable, but will still incur charges.

You can detect start task failures by using the [result](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskexecutionresult) and  [failureInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskfailureinformation) properties of the top-level [startTaskInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#starttaskinformation) node property.

A failed start task also causes Batch to set the node [state](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate) to **starttaskfailed** if  **waitForSuccess** was set to **true**.

As with any task, there can be many causes for the start task failing.  To troubleshoot, check the stdout, stderr, and any further task-specific log files.

Start tasks must be re-entrant, as it is possible the start task is run multiple times on the same node; the start task is run when a node is reimaged or rebooted. In rare cases, a start task will be run after an event caused a node reboot, where one of the operating system or ephemeral disks was reimaged while the other wasn't. Since Batch start tasks (like all Batch tasks) run from the ephemeral disk, this is not normally a problem, but in some instances where the start task is installing an application to the operating system disk and keeping other data on the ephemeral disk, this can cause problems because things are out of sync. Protect your application accordingly if you are using both disks.

### Application package download failure

You can specify one or more application packages for a pool. Batch downloads the specified package files to each node and uncompresses the files after the node has started, but before tasks are scheduled. It's common to use a start task command line in conjunction with application packages. For example, to copy files to a different location or to run setup.

The node [errors](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) property reports a failure to download and un-compress an application package; the node state is set to **unusable**.

### Container download failure

You can specify one or more container references on a pool. Batch downloads the specified containers to each node. The node [errors](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) property reports a failure to download a container and sets the node state to **unusable**.

### Node in unusable state

Azure Batch might set the [node state](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate) to **unusable** for many reasons. With the node state set to **unusable**, tasks can't be scheduled to the node, but it still incurs charges.

Nodes in an **unusable** state, but without [errors](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) means that Batch is unable to communicate with the VM. In this case, Batch always tries to recover the VM. Batch will not automatically attempt to recover VMs that failed to install application packages or containers even though their state is **unusable**.

If Batch can determine the cause, the node [errors](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) property reports it.

Additional examples of causes for **unusable** nodes include:

- A custom VM image is invalid. For example, an image that's not properly prepared.

- A VM is moved because of an infrastructure failure or a low-level upgrade. Batch recovers the node.

- A VM image has been deployed on hardware that doesn’t support it. For example, trying to run a CentOS HPC image on a [Standard_D1_v2](../virtual-machines/linux/sizes-general.md#dv2-series) VM.

- The VMs are in an [Azure virtual network](batch-virtual-network.md), and traffic has been blocked to key ports.

- The VMs are in a virtual network, but outbound traffic to Azure storage is blocked.

- The VMs are in a virtual network with a customer DNS configuration and the DNS server cannot resolve Azure storage.

### Node agent log files

The Batch agent process that runs on each pool node can provide log files that might be helpful if you need to contact support about a pool node issue. Log files for a node can be uploaded via the Azure portal, Batch Explorer, or an [API](https://docs.microsoft.com/rest/api/batchservice/computenode/uploadbatchservicelogs). It's useful to upload and save the log files. Afterward, you can delete the node or pool to save the cost of the running nodes.

### Node disk full

The temporary drive for a pool node VM is used by Batch for job files, task files, and shared files. 

- Application packages files
- Task resource files
- Application-specific files downloaded to one of the Batch folders
- Stdout and stderr files for each task application execution
- Application-specific output files

Some of these files are only written once when pool nodes are created, such as pool application packages or pool start task resource files. Even if only written once when the node is created, if these files are too large they could fill the temporary drive.

Other files are written out for each task that is run on a node, such as stdout and stderr. If a large number of tasks run on the same node and/or the task files are too large, they could fill the temporary drive.

The size of the temporary drive depends on the VM size. One consideration when picking a VM size is to ensure the temporary drive has enough space.

- In the Azure portal when adding a pool, the full list of VM sizes can be displayed and there is a 'Resource Disk Size' column.
- The articles describing all VM sizes have tables with a 'Temp Storage' column; for example [Compute Optimized VM sizes](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-compute)

For files written out by each task, a retention time can be specified for each task that determines how long the task files are kept before being automatically cleaned up. The retention time can be reduced to lower the storage requirements.

If temporary disk space does fill, then currently the node will stop running tasks. In the future, a [node error](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) will be reported.


## Next steps

Check that you've set your application to implement comprehensive error checking, especially for asynchronous operations. It can be critical to promptly detect and diagnose issues.
