---
title: Azure file shares performance troubleshooting guide
description: Troubleshoot known performance issues with Azure file shares. Discover potential causes and associated workarounds when these problems are encountered.
author: gunjanj
ms.service: storage
ms.topic: troubleshooting
ms.date: 11/16/2020
ms.author: gunjanj
ms.subservice: files
#Customer intent: As a < type of user >, I want < what? > so that < why? >.
---
# Troubleshoot Azure file shares performance issues

This article lists some common problems related to Azure file shares. It provides potential causes and workarounds for when you encounter these problems.

## High latency, low throughput, and general performance issues

### Cause 1: Share was throttled

Requests are throttled when the I/O operations per second (IOPS), ingress, or egress limits for a file share are reached. To understand the limits for standard and premium file shares, see [File share and file scale targets](./storage-files-scale-targets.md#file-share-and-file-scale-targets).

To confirm whether your share is being throttled, you can access and use Azure metrics in the portal.

1. In the Azure portal, go to your storage account.

1. On the left pane, under **Monitoring**, select **Metrics**.

1. Select **File** as the metric namespace for your storage account scope.

1. Select **Transactions** as the metric.

1. Add a filter for **Response type**, and then check to see whether any requests have either of the following response codes:
   * **SuccessWithThrottling**: For Server Message Block (SMB)
   * **ClientThrottlingError**: For REST

   ![Screenshot of the metrics options for premium file shares, showing a "Response type" property filter.](media/storage-troubleshooting-premium-fileshares/metrics.png)

   > [!NOTE]
   > To receive an alert, see the ["How to create an alert if a file share is throttled"](#how-to-create-an-alert-if-a-file-share-is-throttled) section later in this article.

### Solution

- If you're using a standard file share, enable [large file shares](./storage-files-how-to-create-large-file-share.md?tabs=azure-portal) on your storage account. Large file shares support up to 10,000 IOPS per share.
- If you're using a premium file share, increase the provisioned file share size to increase the IOPS limit. To learn more, see the "Understanding provisioning for premium file shares" section in the [Azure Files planning guide](./storage-files-planning.md#understanding-provisioning-for-premium-file-shares).

### Cause 2: Metadata or namespace heavy workload

If the majority of your requests are metadata-centric (such as createfile, openfile, closefile, queryinfo, or querydirectory), the latency will be worse than that of read/write operations.

To determine whether most of your requests are metadata-centric, start by following steps 1-4 as previously outlined in Cause 1. For step 5, instead of adding a filter for **Response type**, add a property filter for **API name**.

![Screenshot of the metrics options for premium file shares, showing an "API name" property filter.](media/storage-troubleshooting-premium-fileshares/MetadataMetrics.png)

### Workaround

- Check to see whether the application can be modified to reduce the number of metadata operations.
- Add a virtual hard disk (VHD) on the file share and mount the VHD over SMB from the client to perform file operations against the data. This approach works for single writer and multiple readers scenarios and allows metadata operations to be local. The setup offers performance similar to that of a local directly attached storage.

### Cause 3: Single-threaded application

If the application that you're using is single-threaded, this setup can result in significantly lower IOPS throughput than the maximum possible throughput, depending on your provisioned share size.

### Solution

- Increase application parallelism by increasing the number of threads.
- Switch to applications where parallelism is possible. For example, for copy operations, you could use AzCopy or RoboCopy from Windows clients or the **parallel** command from Linux clients.

## Very high latency for requests

### Cause

The client virtual machine (VM) could be located in a different region than the file share. Other reason for high latency could be due to the latency caused by the client or the network.

### Solution

- Run the application from a VM that's located in the same region as the file share.
- For your storage account, review transaction metrics **SuccessE2ELatency** and  **SuccessServerLatency** via **Azure Monitor** in Azure portal. A high difference between SuccessE2ELatency and SuccessServerLatency metrics values is an indication of latency that is likely caused by the network or the client. See [Transaction metrics](storage-files-monitoring-reference.md#transaction-metrics) in Azure Files Monitoring data reference.

## Client unable to achieve maximum throughput supported by the network

### Cause
One potential cause is a lack of SMB multi-channel support for standard file shares. Currently, Azure Files supports only single channel, so there's only one connection from the client VM to the server. This single connection is pegged to a single core on the client VM, so the maximum throughput achievable from a VM is bound by a single core.

### Workaround

- For premium file shares, [Enable SMB Multichannel on a FileStorage account](storage-files-enable-smb-multichannel.md).
- Obtaining a VM with a bigger core might help improve throughput.
- Running the client application from multiple VMs will increase throughput.
- Use REST APIs where possible.

## Throughput on Linux clients is significantly lower than that of Windows clients

### Cause

This is a known issue with the implementation of the SMB client on Linux.

### Workaround

- Spread the load across multiple VMs.
- On the same VM, use multiple mount points with a **nosharesock** option, and spread the load across these mount points.
- On Linux, try mounting with a **nostrictsync** option to avoid forcing an SMB flush on every **fsync** call. For Azure Files, this option doesn't interfere with data consistency, but it might result in stale file metadata on directory listings (**ls -l** command). Directly querying file metadata by using the **stat** command will return the most up-to-date file metadata.

## High latencies for metadata-heavy workloads involving extensive open/close operations

### Cause

Lack of support for directory leases.

### Workaround

- If possible, avoid using an excessive opening/closing handle on the same directory within a short period of time.
- For Linux VMs, increase the directory entry cache timeout by specifying **actimeo=\<sec>** as a mount option. By default, the timeout is 1 second, so a larger value, such as 3 or 5 seconds, might help.
- For CentOS Linux or Red Hat Enterprise Linux (RHEL) VMs, upgrade the system to CentOS Linux 8.2 or RHEL 8.2. For other Linux VMs, upgrade the kernel to 5.0 or later.

## Low IOPS on CentOS Linux or RHEL

### Cause

An I/O depth of greater than 1 is not supported on CentOS Linux or RHEL.

### Workaround

- Upgrade to CentOS Linux 8 or RHEL 8.
- Change to Ubuntu.

## Slow file copying to and from Azure file shares in Linux

If you're experiencing slow file copying, see the "Slow file copying to and from Azure file shares in Linux" section in the [Linux troubleshooting guide](storage-troubleshoot-linux-file-connection-problems.md#slow-file-copying-to-and-from-azure-files-in-linux).

## Jittery or sawtooth pattern for IOPS

### Cause

The client application consistently exceeds baseline IOPS. Currently, there's no service-side smoothing of the request load. If the client exceeds baseline IOPS, it will get throttled by the service. The throttling can result in the client experiencing a jittery or sawtooth IOPS pattern. In this case, the average IOPS achieved by the client might be lower than the baseline IOPS.

### Workaround
- Reduce the request load from the client application, so that the share doesn't get throttled.
- Increase the quota of the share so that the share doesn't get throttled.

## Excessive DirectoryOpen/DirectoryClose calls

### Cause

If the number of **DirectoryOpen/DirectoryClose** calls is among the top API calls and you don't expect the client to make that many calls, the issue might be caused by the antivirus software that's installed on the Azure client VM.

### Workaround

- A fix for this issue is available in the [April Platform Update for Windows](https://support.microsoft.com/help/4052623/update-for-windows-defender-antimalware-platform).

## File creation is slower than expected

### Cause

Workloads that rely on creating a large number of files won't see a substantial difference in performance between premium file shares and standard file shares.

### Workaround

- None.

## Slow performance from Windows 8.1 or Server 2012 R2

### Cause

Higher than expected latency accessing Azure file shares for I/O-intensive workloads.

### Workaround

- Install the available [hotfix](https://support.microsoft.com/help/3114025/slow-performance-when-you-access-azure-files-storage-from-windows-8-1).

## SMB Multichannel option not visible under File share settings. 

### Cause

Either the subscription is not registered for the feature, or the region and account type is not supported.

### Solution

Ensure that your subscription is registered for SMB Multichannel feature. See [Getting started](storage-files-enable-smb-multichannel.md#getting-started)
Ensure that the account kind is FileStorage (premium file account) in the account overview page. 

## SMB Multichannel is not being triggered.

### Cause

Recent changes to SMB Multichannel config settings without a remount.

### Solution
 
-	After any changes to Windows SMB client or account SMB multichannel configuration settings, you have to unmount the share, wait for 60 secs, and remount the share to trigger the multichannel.
-	For Windows client OS, generate IO load with high queue depth say QD=8, for example copying a file to trigger SMB Multichannel.  For server OS, SMB Multichannel is triggered with QD=1, which means as soon as you start any IO to the share.

## High latency on web sites hosted on file shares 

### Cause  

High number file change notification on file shares can result in significant high latencies. This typically occurs with web sites hosted on file shares with deep nested directory structure. A typical scenario is IIS hosted web application where file change notification is setup for each directory in the default configuration. Each change ([ReadDirectoryChangesW](/windows/win32/api/winbase/nf-winbase-readdirectorychangesw)) on the share that SMB client is registered for  pushes a change notification from the file service to the client, which takes system resources, and issue worsens with the number of changes. This can cause share throttling and thus, result in higher client side latency. 

To confirm, you can use Azure Metrics in the portal - 

1. In the Azure portal, go to your storage account. 
1. In the left menu, under Monitoring, select Metrics. 
1. Select File as the metric namespace for your storage account scope. 
1. Select Transactions as the metric. 
1. Add a filter for ResponseType and check to see if any requests have a response code of SuccessWithThrottling (for SMB) or ClientThrottlingError (for REST).

### Solution 

- If file change notification is not used,  disable file change notification (preferred).
    - [Disable file change notification](https://support.microsoft.com/help/911272/fix-asp-net-2-0-connected-applications-on-a-web-site-may-appear-to-sto) by updating FCNMode. 
    - Update the IIS Worker Process (W3WP) polling interval to 0 by setting `HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\W3SVC\Parameters\ConfigPollMilliSeconds ` in your registry and restart the W3WP process. To learn about this setting, see [Common registry keys that are used by many parts of IIS](/troubleshoot/iis/use-registry-keys#registry-keys-that-apply-to-iis-worker-process-w3wp).
- Increase frequency of the file change notification polling interval to reduce volume.
    - Update the W3WP worker process polling interval to a higher value (e.g. 10mins or 30mins) based on your requirement. Set `HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\W3SVC\Parameters\ConfigPollMilliSeconds ` [in your registry](/troubleshoot/iis/use-registry-keys#registry-keys-that-apply-to-iis-worker-process-w3wp) and restart the W3WP process.
- If your web site's mapped  physical directory has nested directory structure, you can try to limit scope of file change notification to reduce the notification volume. By default, IIS uses configuration from Web.config files in the physical directory to which the virtual directory is mapped, as well as in any child directories in that physical directory. If you do not want to use Web.config files in child directories, specify false for the allowSubDirConfig attribute on the virtual directory. More details can be found [here](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories). 
    - Set IIS  virtual directory "allowSubDirConfig" setting in Web.Config to *false* to exclude mapped physical child directories from the scope.  

## How to create an alert if a file share is throttled

1. In the Azure portal, go to your storage account.
1. In the **Monitoring** section, select **Alerts**, and then select **New alert rule**.
1. Select **Edit resource**, select the **File resource type** for the storage account, and then select **Done**. For example, if the storage account name is *contoso*, select the contoso/file resource.
1. Select **Select Condition** to add a condition.
1. In the list of signals that are supported for the storage account, select the **Transactions** metric.
1. On the **Configure signal logic** pane, in the **Dimension name** drop-down list, select **Response type**.
1. In the **Dimension values** drop-down list, select **SuccessWithThrottling** (for SMB) or **ClientThrottlingError** (for REST).

   > [!NOTE]
   > If neither the **SuccessWithThrottling** nor the **ClientThrottlingError** dimension value is listed, this means that the resource has not been throttled. To add the dimension value, next to the **Dimension values** drop-down list, select **Add custom value**, enter **SuccessWithThrottling** or **ClientThrottlingError**, select **OK**, and then repeat step 7.

1. In the **Dimension name** drop-down list, select **File Share**.
1. In the **Dimension values** drop-down list, select the file share or shares that you want to alert on.

   > [!NOTE]
   > If the file share is a standard file share, select **All current and future values**. The dimension values drop-down list doesn't list the file shares, because per-share metrics aren't available for standard file shares. Throttling alerts for standard file shares is triggered if any file share within the storage account is throttled, and the alert doesn't identify which file share was throttled. Because per-share metrics aren't available for standard file shares, we recommend that you use one file share per storage account.

1. Define the alert parameters by entering the **Threshold value**, **Operator**, **Aggregation granularity**, and **Frequency of evaluation**, and then select **Done**.

    > [!TIP]
    > If you're using a static threshold, the metric chart can help you determine a reasonable threshold value if the file share is currently being throttled. If you're using a dynamic threshold, the metric chart displays the calculated thresholds based on recent data.

1. Select **Select action group**, and then add an action group (for example, email or SMS) to the alert either by selecting an existing action group or by creating a new action group.
1. Enter the alert details, such as **Alert rule name**, **Description**, and **Severity**.
1. Select **Create alert rule** to create the alert.

To learn more about configuring alerts in Azure Monitor, see [Overview of alerts in Microsoft Azure]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview).

## How to create alerts if a premium file share is trending toward being throttled

1. In the Azure portal, go to your storage account.
1. In the **Monitoring** section, select **Alerts**, and then select **New alert rule**.
1. Select **Edit resource**, select the **File resource type** for the storage account, and then select **Done**. For example, if the storage account name is *contoso*, select the contoso/file resource.
1. Select **Select Condition** to add a condition.
1. In the list of signals that are supported for the storage account, select the **Egress** metric.

   > [!NOTE]
   > You have to create three separate alerts to be alerted when the ingress, egress, or transaction values exceed the thresholds you set. This is because an alert is triggered only when all conditions are met. For example, if you put all the conditions in one alert, you would be alerted only if ingress, egress, and transactions exceed their threshold amounts.

1. Scroll down. In the **Dimension name** drop-down list, select **File Share**.
1. In the **Dimension values** drop-down list, select the file share or shares that you want to alert on.
1. Define the alert parameters by selecting values in the **Operator**, **Threshold value**, **Aggregation granularity**, and **Frequency of evaluation** drop-down lists, and then select **Done**.

   Egress, ingress, and transactions metrics are expressed per minute, though you're provisioned egress, ingress, and I/O per second. Therefore, for example, if your provisioned egress is 90&nbsp;mebibytes per second (MiB/s) and you want your threshold to be 80&nbsp;percent of provisioned egress, select the following alert parameters: 
   - For **Threshold value**: *75497472* 
   - For **Operator**: *greater than or equal to*
   - For **Aggregation type**: *average*
   
   Depending on how noisy you want your alert to be, you can also select values for **Aggregation granularity** and **Frequency of evaluation**. For example, if you want your alert to look at the average ingress over the time period of 1 hour, and you want your alert rule to be run every hour, select the following:
   - For **Aggregation granularity**: *1 hour*
   - For **Frequency of evaluation**: *1 hour*

1. Select **Select action group**, and then add an action group (for example, email or SMS) to the alert either by selecting an existing action group or by creating a new one.
1. Enter the alert details, such as **Alert rule name**, **Description**, and **Severity**.
1. Select **Create alert rule** to create the alert.

    > [!NOTE]
    > - To be notified that your premium file share is close to being throttled *because of provisioned ingress*, follow the preceding instructions, but with the following change:
    >    - In step 5, select the **Ingress** metric instead of **Egress**.
    >
    > - To be notified that your premium file share is close to being throttled *because of provisioned IOPS*, follow the preceding instructions, but with the following changes:
    >    - In step 5, select the **Transactions** metric instead of **Egress**.
    >    - In step 10, the only option for **Aggregation type** is *Total*. Therefore, the threshold value depends on your selected aggregation granularity. For example, if you want your threshold to be 80&nbsp;percent of provisioned baseline IOPS and you select *1 hour* for **Aggregation granularity**, your **Threshold value** would be your baseline IOPS (in bytes) &times;&nbsp;0.8 &times;&nbsp;3600. 

To learn more about configuring alerts in Azure Monitor, see [Overview of alerts in Microsoft Azure]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview).

## See also
- [Troubleshoot Azure Files in Windows](storage-troubleshoot-windows-file-connection-problems.md)  
- [Troubleshoot Azure Files in Linux](storage-troubleshoot-linux-file-connection-problems.md)  
- [Azure Files FAQ](storage-files-faq.md)
