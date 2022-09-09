---
title: Monitor, diagnose, and troubleshoot Azure Storage | Microsoft Docs
description: Use features like storage analytics, client-side logging, and other third-party tools to identify, diagnose, and troubleshoot Azure Storage-related issues.
author: normesta
ms.service: storage
ms.topic: troubleshooting
ms.date: 10/08/2020
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: "monitoring, devx-track-csharp"
---
# Monitor, diagnose, and troubleshoot Microsoft Azure Storage
[!INCLUDE [storage-selector-portal-monitoring-diagnosing-troubleshooting](../../../includes/storage-selector-portal-monitoring-diagnosing-troubleshooting.md)]

## Overview
Diagnosing and troubleshooting issues in a distributed application hosted in a cloud environment can be more complex than in traditional environments. Applications can be deployed in a PaaS or IaaS infrastructure, on premises, on a mobile device, or in some combination of these environments. Typically, your application's network traffic may traverse public and private networks and your application may use multiple storage technologies such as Microsoft Azure Storage Tables, Blobs, Queues, or Files in addition to other data stores such as relational and document databases.

To manage such applications successfully you should monitor them proactively and understand how to diagnose and troubleshoot all aspects of them and their dependent technologies. As a user of Azure Storage services, you should continuously monitor the Storage services your application uses for any unexpected changes in behavior (such as slower than usual response times), and use logging to collect more detailed data and to analyze a problem in depth. The diagnostics information you obtain from both monitoring and logging will help you to determine the root cause of the issue your application encountered. Then you can troubleshoot the issue and determine the appropriate steps you can take to remediate it. Azure Storage is a core Azure service, and forms an important part of the majority of solutions that customers deploy to the Azure infrastructure. Azure Storage includes capabilities to simplify monitoring, diagnosing, and troubleshooting storage issues in your cloud-based applications.

* [Introduction]
  * [How this guide is organized]
* [Monitoring your storage service]
  * [Monitoring service health]
  * [Monitoring capacity]
  * [Monitoring availability]
  * [Monitoring performance]
* [Diagnosing storage issues]
  * [Service health issues]
  * [Performance issues]
  * [Diagnosing errors]
  * [Storage emulator issues]
  * [Storage logging tools]
  * [Using network logging tools]
* [End-to-end tracing]
  * [Correlating log data]
  * [Client request ID]
  * [Server request ID]
  * [Timestamps]
* [Troubleshooting guidance]
  * [Metrics show high AverageE2ELatency and low AverageServerLatency]
  * [Metrics show low AverageE2ELatency and low AverageServerLatency but the client is experiencing high latency]
  * [Metrics show high AverageServerLatency]
  * [You are experiencing unexpected delays in message delivery on a queue]
  * [Metrics show an increase in PercentThrottlingError]
  * [Metrics show an increase in PercentTimeoutError]
  * [Metrics show an increase in PercentNetworkError]
  * [The client is receiving HTTP 403 (Forbidden) messages]
  * [The client is receiving HTTP 404 (Not found) messages]
  * [The client is receiving HTTP 409 (Conflict) messages]
  * [Metrics show low PercentSuccess or analytics log entries have operations with transaction status of ClientOtherErrors]
  * [Capacity metrics show an unexpected increase in storage capacity usage]
  * [Your issue arises from using the storage emulator for development or test]
  * [You are encountering problems installing the Azure SDK for .NET]
  * [You have a different issue with a storage service]
  * [Troubleshooting VHDs on Windows virtual machines](../../virtual-machines/windows/troubleshoot-vhds.md)   
  * [Troubleshooting VHDs on Linux virtual machines](../../virtual-machines/linux/troubleshoot-vhds.md)
  * [Troubleshooting Azure Files issues with Windows](../files/storage-troubleshoot-windows-file-connection-problems.md)   
  * [Troubleshooting Azure Files issues with Linux](../files/storage-troubleshoot-linux-file-connection-problems.md)
* [Appendices]
  * [Appendix 1: Using Fiddler to capture HTTP and HTTPS traffic]
  * [Appendix 2: Using Wireshark to capture network traffic]
  * [Appendix 4: Using Excel to view metrics and log data]
  * [Appendix 5: Monitoring with Application Insights for Azure DevOps]

## <a name="introduction"></a>Introduction
This guide shows you how to use features such as Azure Storage Analytics, client-side logging in the Azure Storage Client Library, and other third-party tools to identify, diagnose, and troubleshoot Azure Storage related issues.

![Diagram that shows the flow of information between client applications and Azure storage services.][1]

This guide is intended to be read primarily by developers of online services that use Azure Storage Services and IT Pros responsible for managing such online services. The goals of this guide are:

* To help you maintain the health and performance of your Azure Storage accounts.
* To provide you with the necessary processes and tools to help you decide whether an issue or problem in an application relates to Azure Storage.
* To provide you with actionable guidance for resolving problems related to Azure Storage.

### <a name="how-this-guide-is-organized"></a>How this guide is organized
The section "[Monitoring your storage service]" describes how to monitor the health and performance of your Azure Storage services using Azure Storage Analytics Metrics (Storage Metrics).

The section "[Diagnosing storage issues]" describes how to diagnose issues using Azure Storage Analytics Logging (Storage Logging). It also describes how to enable client-side logging using the facilities in one of the client libraries such as the Storage Client Library for .NET or the Azure SDK for Java.

The section "[End-to-end tracing]" describes how you can correlate the information contained in various log files and metrics data.

The section "[Troubleshooting guidance]" provides troubleshooting guidance for some of the common storage-related issues you might encounter.

The "[Appendices]" include information about using other tools such as Wireshark and Netmon for analyzing network packet data, and Fiddler for analyzing HTTP/HTTPS messages.

## <a name="monitoring-your-storage-service"></a>Monitoring your storage service
If you are familiar with Windows performance monitoring, you can think of Storage Metrics as being an Azure Storage equivalent of Windows Performance Monitor counters. In Storage Metrics, you will find a comprehensive set of metrics (counters in Windows Performance Monitor terminology) such as service availability, total number of requests to service, or percentage of successful requests to service. For a full list of the available metrics, see [Storage Analytics Metrics Table Schema](https://msdn.microsoft.com/library/azure/hh343264.aspx). You can specify whether you want the storage service to collect and aggregate metrics every hour or every minute. For more information about how to enable metrics and monitor your storage accounts, see [Enabling storage metrics and viewing metrics data](https://go.microsoft.com/fwlink/?LinkId=510865).

You can choose which hourly metrics you want to display in the [Azure portal](https://portal.azure.com) and configure rules that notify administrators by email whenever an hourly metric exceeds a particular threshold. For more information, see [Receive Alert Notifications](/azure/monitoring-and-diagnostics/monitoring-overview-alerts).

We recommend you review [Azure Monitor for Storage](../../azure-monitor/insights/storage-insights-overview.md) (preview). It is a feature of Azure Monitor that offers comprehensive monitoring of your Azure Storage accounts by delivering a unified view of your Azure Storage services performance, capacity, and availability. It does not require you to enable or configure anything, and you can immediately view these metrics from the pre-defined interactive charts and other visualizations included.

The storage service collects metrics using a best effort, but may not record every storage operation.

In the Azure portal, you can view metrics such as availability, total requests, and average latency numbers for a storage account. A notification rule has also been set up to alert an administrator if availability drops below a certain level. From viewing this data, one possible area for investigation is the table service success percentage being below 100% (for more information, see the section "[Metrics show low PercentSuccess or analytics log entries have operations with transaction status of ClientOtherErrors]").

You should continuously monitor your Azure applications to ensure they are healthy and performing as expected by:

* Establishing some baseline metrics for application that will enable you to compare current data and identify any significant changes in the behavior of Azure storage and your application. The values of your baseline metrics will, in many cases, be application specific and you should establish them when you are performance testing your application.
* Recording minute metrics and using them to monitor actively for unexpected errors and anomalies such as spikes in error counts or request rates.
* Recording hourly metrics and using them to monitor average values such as average error counts and request rates.
* Investigating potential issues using diagnostics tools as discussed later in the section "[Diagnosing storage issues]."

The charts in the following image illustrate how the averaging that occurs for hourly metrics can hide spikes in activity. The hourly metrics appear to show a steady rate of requests, while the minute metrics reveal the fluctuations that are really taking place.

![Charts that show how the averaging that occurs for hourly metrics can hide spikes in activity.][3]

The remainder of this section describes what metrics you should monitor and why.

### <a name="monitoring-service-health"></a>Monitoring service health
You can use the [Azure portal](https://portal.azure.com) to view the health of the Storage service (and other Azure services) in all the Azure regions around the world. Monitoring enables you to see immediately if an issue outside of your control is affecting the Storage service in the region you use for your application.

The [Azure portal](https://portal.azure.com) can also provide notifications of incidents that affect the various Azure services.
Note: This information was previously available, along with historical data, on the [Azure Service Dashboard](https://status.azure.com).

While the [Azure portal](https://portal.azure.com) collects health information from inside the Azure datacenters (inside-out monitoring), you could also consider adopting an outside-in approach to generate synthetic transactions that periodically access your Azure-hosted web application from multiple locations. The services offered by [Dynatrace](https://www.dynatrace.com/en/synthetic-monitoring) and Application Insights for Azure DevOps are examples of this approach. For more information about Application Insights for Azure DevOps, see the appendix "[Appendix 5: Monitoring with Application Insights for Azure DevOps](#appendix-5)."

### <a name="monitoring-capacity"></a>Monitoring capacity
Storage Metrics only stores capacity metrics for the blob service because blobs typically account for the largest proportion of stored data (at the time of writing, it is not possible to use Storage Metrics to monitor the capacity of your tables and queues). You can find this data in the **$MetricsCapacityBlob** table if you have enabled monitoring for the Blob service. Storage Metrics records this data once per day, and you can use the value of the **RowKey** to determine whether the row contains an entity that relates to user data (value **data**) or analytics data (value **analytics**). Each stored entity contains information about the amount of storage used (**Capacity** measured in bytes) and the current number of containers (**ContainerCount**) and blobs (**ObjectCount**) in use in the storage account. For more information about the capacity metrics stored in the **$MetricsCapacityBlob** table, see [Storage Analytics Metrics Table Schema](https://msdn.microsoft.com/library/azure/hh343264.aspx).

> [!NOTE]
> You should monitor these values for an early warning that you are approaching the capacity limits of your storage account. In the Azure portal, you can add alert rules to notify you if aggregate storage use exceeds or falls below thresholds that you specify.
>
>

For help estimating the size of various storage objects such as blobs, see the blog post [Understanding Azure Storage Billing – Bandwidth, Transactions, and Capacity](/archive/blogs/patrick_butler_monterde/azure-storage-understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity).

### <a name="monitoring-availability"></a>Monitoring availability
You should monitor the availability of the storage services in your storage account by monitoring the value in the **Availability** column in the hourly or minute metrics tables — **$MetricsHourPrimaryTransactionsBlob**, **$MetricsHourPrimaryTransactionsTable**, **$MetricsHourPrimaryTransactionsQueue**, **$MetricsMinutePrimaryTransactionsBlob**, **$MetricsMinutePrimaryTransactionsTable**, **$MetricsMinutePrimaryTransactionsQueue**, **$MetricsCapacityBlob**. The **Availability** column contains a percentage value that indicates the availability of the service or the API operation represented by the row (the **RowKey** shows if the row contains metrics for the service as a whole or for a specific API operation).

Any value less than 100% indicates that some storage requests are failing. You can see why they are failing by examining the other columns in the metrics data that show the numbers of requests with different error types such as **ServerTimeoutError**. You should expect to see **Availability** fall temporarily below 100% for reasons such as transient server timeouts while the service moves partitions to better load-balance request; the retry logic in your client application should handle such intermittent conditions. The article [Storage Analytics Logged Operations and Status Messages](https://msdn.microsoft.com/library/azure/hh343260.aspx) lists the transaction types that Storage Metrics includes in its **Availability** calculation.

In the [Azure portal](https://portal.azure.com), you can add alert rules to notify you if **Availability** for a service falls below a threshold that you specify.

The "[Troubleshooting guidance]" section of this guide describes some common storage service issues related to availability.

### <a name="monitoring-performance"></a>Monitoring performance
To monitor the performance of the storage services, you can use the following metrics from the hourly and minute metrics tables.

* The values in the **AverageE2ELatency** and **AverageServerLatency** columns show the average time the storage service or API operation type is taking to process requests. **AverageE2ELatency** is a measure of end-to-end latency that includes the time taken to read the request and send the response in addition to the time taken to process the request (therefore includes network latency once the request reaches the storage service); **AverageServerLatency** is a measure of just the processing time and therefore excludes any network latency related to communicating with the client. See the section "[Metrics show high AverageE2ELatency and low AverageServerLatency]" later in this guide for a discussion of why there might be a significant difference between these two values.
* The values in the **TotalIngress** and **TotalEgress** columns show the total amount of data, in bytes, coming in to and going out of your storage service or through a specific API operation type.
* The values in the **TotalRequests** column show the total number of requests that the storage service of API operation is receiving. **TotalRequests** is the total number of requests that the storage service receives.

Typically, you will monitor for unexpected changes in any of these values as an indicator that you have an issue that requires investigation.

In the [Azure portal](https://portal.azure.com), you can add alert rules to notify you if any of the performance metrics for this service fall below or exceed a threshold that you specify.

The "[Troubleshooting guidance]" section of this guide describes some common storage service issues related to performance.

## <a name="diagnosing-storage-issues"></a>Diagnosing storage issues
There are a number of ways that you might become aware of a problem or issue in your application, including:

* A major failure that causes the application to crash or to stop working.
* Significant changes from baseline values in the metrics you are monitoring as described in the previous section "[Monitoring your storage service]."
* Reports from users of your application that some particular operation didn't complete as expected or that some feature is not working.
* Errors generated within your application that appear in log files or through some other notification method.

Typically, issues related to Azure storage services fall into one of four broad categories:

* Your application has a performance issue, either reported by your users, or revealed by changes in the performance metrics.
* There is a problem with the Azure Storage infrastructure in one or more regions.
* Your application is encountering an error, either reported by your users, or revealed by an increase in one of the error count metrics you monitor.
* During development and test, you may be using the local storage emulator; you may encounter some issues that relate specifically to usage of the storage emulator.

The following sections outline the steps you should follow to diagnose and troubleshoot issues in each of these four categories. The section "[Troubleshooting guidance]" later in this guide provides more detail for some common issues you may encounter.

### <a name="service-health-issues"></a>Service health issues
Service health issues are typically outside of your control. The [Azure portal](https://portal.azure.com) provides information about any ongoing issues with Azure services including storage services. If you opted for Read-Access Geo-Redundant Storage when you created your storage account, then if your data becomes unavailable in the primary location, your application can switch temporarily to the read-only copy in the secondary location. To read from the secondary, your application must be able to switch between using the primary and secondary storage locations, and be able to work in a reduced functionality mode with read-only data. The Azure Storage Client libraries allow you to define a retry policy that can read from secondary storage in case a read from primary storage fails. Your application also needs to be aware that the data in the secondary location is eventually consistent. For more information, see the blog post [Azure Storage Redundancy Options and Read Access Geo Redundant Storage](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/).

### <a name="performance-issues"></a>Performance issues
The performance of an application can be subjective, especially from a user perspective. Therefore, it is important to have baseline metrics available to help you identify where there might be a performance issue. Many factors might affect the performance of an Azure storage service from the client application perspective. These factors might operate in the storage service, in the client, or in the network infrastructure; therefore it is important to have a strategy for identifying the origin of the performance issue.

After you have identified the likely location of the cause of the performance issue from the metrics, you can then use the log files to find detailed information to diagnose and troubleshoot the problem further.

The section "[Troubleshooting guidance]" later in this guide provides more information about some common performance-related issues you may encounter.

### <a name="diagnosing-errors"></a>Diagnosing errors
Users of your application may notify you of errors reported by the client application. Storage Metrics also records counts of different error types from your storage services such as **NetworkError**, **ClientTimeoutError**, or **AuthorizationError**. While Storage Metrics only records counts of different error types, you can obtain more detail about individual requests by examining server-side, client-side, and network logs. Typically, the HTTP status code returned by the storage service will give an indication of why the request failed.

> [!NOTE]
> Remember that you should expect to see some intermittent errors: for example, errors due to transient network conditions, or application errors.
>
>

The following resources are useful for understanding storage-related status and error codes:

* [Common REST API Error Codes](https://msdn.microsoft.com/library/azure/dd179357.aspx)
* [Blob Service Error Codes](https://msdn.microsoft.com/library/azure/dd179439.aspx)
* [Queue Service Error Codes](https://msdn.microsoft.com/library/azure/dd179446.aspx)
* [Table Service Error Codes](https://msdn.microsoft.com/library/azure/dd179438.aspx)
* [File Service Error Codes](https://msdn.microsoft.com/library/azure/dn690119.aspx)

### <a name="storage-emulator-issues"></a>Storage emulator issues
The Azure SDK includes a storage emulator you can run on a development workstation. This emulator simulates most of the behavior of the Azure storage services and is useful during development and test, enabling you to run applications that use Azure storage services without the need for an Azure subscription and an Azure storage account.

The "[Troubleshooting guidance]" section of this guide describes some common issues encountered using the storage emulator.

### <a name="storage-logging-tools"></a>Storage logging tools
Storage Logging provides server-side logging of storage requests in your Azure storage account. For more information about how to enable server-side logging and access the log data, see [Enabling Storage Logging and Accessing Log Data](https://go.microsoft.com/fwlink/?LinkId=510867).

The Storage Client Library for .NET enables you to collect client-side log data that relates to storage operations performed by your application. For more information, see [Client-side Logging with the .NET Storage Client Library](https://go.microsoft.com/fwlink/?LinkId=510868).

> [!NOTE]
> In some circumstances (such as SAS authorization failures), a user may report an error for which you can find no request data in the server-side Storage logs. You can use the logging capabilities of the Storage Client Library to investigate if the cause of the issue is on the client or use network monitoring tools to investigate the network.
>
>

### <a name="using-network-logging-tools"></a>Using network logging tools
You can capture the traffic between the client and server to provide detailed information about the data the client and server are exchanging and the underlying network conditions. Useful network logging tools include:

* [Fiddler](https://www.telerik.com/fiddler) is a free web debugging proxy that enables you to examine the headers and payload data of HTTP and HTTPS request and response messages. For more information, see [Appendix 1: Using Fiddler to capture HTTP and HTTPS traffic](#appendix-1).
* [Microsoft Network Monitor (Netmon)](https://download.cnet.com/s/network-monitor/) and [Wireshark](https://www.wireshark.org/) are free network protocol analyzers that enable you to view detailed packet information for a wide range of network protocols. For more information about Wireshark, see "[Appendix 2: Using Wireshark to capture network traffic](#appendix-2)".
* If you want to perform a basic connectivity test to check that your client machine can connect to the Azure storage service over the network, you cannot do this using the standard **ping** tool on the client. However, you can use the [**tcping** tool](https://www.elifulkerson.com/projects/tcping.php) to check connectivity.

In many cases, the log data from Storage Logging and the Storage Client Library will be sufficient to diagnose an issue, but in some scenarios, you may need the more detailed information that these network logging tools can provide. For example, using Fiddler to view HTTP and HTTPS messages enables you to view header and payload data sent to and from the storage services, which would enable you to examine how a client application retries storage operations. Protocol analyzers such as Wireshark operate at the packet level enabling you to view TCP data, which would enable you to troubleshoot lost packets and connectivity issues. 

## <a name="end-to-end-tracing"></a>End-to-end tracing
End-to-end tracing using a variety of log files is a useful technique for investigating potential issues. You can use the date/time information from your metrics data as an indication of where to start looking in the log files for the detailed information that will help you troubleshoot the issue.

### <a name="correlating-log-data"></a>Correlating log data
When viewing logs from client applications, network traces, and server-side storage logging it is critical to be able to correlate requests across the different log files. The log files include a number of different fields that are useful as correlation identifiers. The client request ID is the most useful field to use to correlate entries in the different logs. However sometimes, it can be useful to use either the server request ID or timestamps. The following sections provide more details about these options.

### <a name="client-request-id"></a>Client request ID
The Storage Client Library automatically generates a unique client request ID for every request.

* In the client-side log that the Storage Client Library creates, the client request ID appears in the **Client Request ID** field of every log entry relating to the request.
* In a network trace such as one captured by Fiddler, the client request ID is visible in request messages as the **x-ms-client-request-id** HTTP header value.
* In the server-side Storage Logging log, the client request ID appears in the Client request ID column.

> [!NOTE]
> It is possible for multiple requests to share the same client request ID because the client can assign this value (although the Storage Client Library assigns a
> new value automatically). When the client retries, all attempts share the same client request ID. In the case of a batch sent from the client, the batch has a single client request ID.
>
>

### <a name="server-request-id"></a>Server request ID
The storage service automatically generates server request IDs.

* In the server-side Storage Logging log, the server request ID appears the **Request ID header** column.
* In a network trace such as one captured by Fiddler, the server request ID appears in response messages as the **x-ms-request-id** HTTP header value.
* In the client-side log that the Storage Client Library creates, the server request ID appears in the **Operation Text** column for the log entry showing details of the server response.

> [!NOTE]
> The storage service always assigns a unique server request ID to every request it receives, so every retry attempt from the client and every operation included in a batch has a unique server request ID.
>
>

# [.NET v12](#tab/dotnet)

The code sample below demonstrates how to use a custom client request ID. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Monitoring.cs" id="Snippet_UseCustomRequestID":::

# [.NET v11](#tab/dotnet11)

If the Storage Client Library throws a **StorageException** in the client, the **RequestInformation** property contains a **RequestResult** object that includes a **ServiceRequestID** property. You can also access a **RequestResult** object from an **OperationContext** instance.

The code sample below demonstrates how to set a custom **ClientRequestId** value by attaching an **OperationContext** object the request to the storage service. It also shows how to retrieve the **ServerRequestId** value from the response message.

```csharp
//Parse the connection string for the storage account.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Create an Operation Context that includes custom ClientRequestId string based on constants defined within the application along with a Guid.
OperationContext oc = new OperationContext();
oc.ClientRequestID = String.Format("{0} {1} {2} {3}", HOSTNAME, APPNAME, USERID, Guid.NewGuid().ToString());

try
{
    CloudBlobContainer container = blobClient.GetContainerReference("democontainer");
    ICloudBlob blob = container.GetBlobReferenceFromServer("testImage.jpg", null, null, oc);  
    var downloadToPath = string.Format("./{0}", blob.Name);
    using (var fs = File.OpenWrite(downloadToPath))
    {
        blob.DownloadToStream(fs, null, null, oc);
        Console.WriteLine("\t Blob downloaded to file: {0}", downloadToPath);
    }
}
catch (StorageException storageException)
{
    Console.WriteLine("Storage exception {0} occurred", storageException.Message);
    // Multiple results may exist due to client side retry logic - each retried operation will have a unique ServiceRequestId
    foreach (var result in oc.RequestResults)
    {
            Console.WriteLine("HttpStatus: {0}, ServiceRequestId {1}", result.HttpStatusCode, result.ServiceRequestID);
    }
}
```

---

### <a name="timestamps"></a>Timestamps
You can also use timestamps to locate related log entries, but be careful of any clock skew between the client and server that may exist. Search plus or minus 15 minutes for matching server-side entries based on the timestamp on the client. Remember that the blob metadata for the blobs containing metrics indicates the time range for the metrics stored in the blob. This time range is useful if you have many metrics blobs for the same minute or hour.

## <a name="troubleshooting-guidance"></a>Troubleshooting guidance
This section will help you with the diagnosis and troubleshooting of some of the common issues your application may encounter when using the Azure storage services. Use the list below to locate the information relevant to your specific issue.

**Troubleshooting Decision Tree**

---
Does your issue relate to the performance of one of the storage services?

* [Metrics show high AverageE2ELatency and low AverageServerLatency]
* [Metrics show low AverageE2ELatency and low AverageServerLatency but the client is experiencing high latency]
* [Metrics show high AverageServerLatency]
* [You are experiencing unexpected delays in message delivery on a queue]

---
Does your issue relate to the availability of one of the storage services?

* [Metrics show an increase in PercentThrottlingError]
* [Metrics show an increase in PercentTimeoutError]
* [Metrics show an increase in PercentNetworkError]

---
 Is your client application receiving an HTTP 4XX (such as 404) response from a storage service?

* [The client is receiving HTTP 403 (Forbidden) messages]
* [The client is receiving HTTP 404 (Not found) messages]
* [The client is receiving HTTP 409 (Conflict) messages]

---
[Metrics show low PercentSuccess or analytics log entries have operations with transaction status of ClientOtherErrors]

---
[Capacity metrics show an unexpected increase in storage capacity usage]

---
[You are experiencing unexpected reboots of Virtual Machines that have a large number of attached VHDs]

---
[Your issue arises from using the storage emulator for development or test]

---
[You are encountering problems installing the Azure SDK for .NET]

---
[You have a different issue with a storage service]

---
### <a name="metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency"></a>Metrics show high AverageE2ELatency and low AverageServerLatency
The illustration below from the [Azure portal](https://portal.azure.com) monitoring tool shows an example where the **AverageE2ELatency** is significantly higher than the **AverageServerLatency**.

![Illustration from the Azure portal that shows an example where the AverageE2ELatency is significantly higher than the AverageServerLatency.][4]

The storage service only calculates the metric **AverageE2ELatency** for successful requests and, unlike **AverageServerLatency**, includes the time the client takes to send the data and receive acknowledgment from the storage service. Therefore, a difference between **AverageE2ELatency** and **AverageServerLatency** could be either due to the client application being slow to respond, or due to conditions on the network.

> [!NOTE]
> You can also view **E2ELatency** and **ServerLatency** for individual storage operations in the Storage Logging log data.
>
>

#### Investigating client performance issues
Possible reasons for the client responding slowly include having a limited number of available connections or threads, or being low on resources such as CPU, memory or network bandwidth. You may be able to resolve the issue by modifying the client code to be more efficient (for example by using asynchronous calls to the storage service), or by using a larger Virtual Machine (with more cores and more memory).

For the table and queue services, the Nagle algorithm can also cause high **AverageE2ELatency** as compared to **AverageServerLatency**: for more information, see the post [Nagle's Algorithm is Not Friendly towards Small Requests](/archive/blogs/windowsazurestorage/nagles-algorithm-is-not-friendly-towards-small-requests). You can disable the Nagle algorithm in code by using the **ServicePointManager** class in the **System.Net** namespace. You should do this before you make any calls to the table or queue services in your application since this does not affect connections that are already open. The following example comes from the **Application_Start** method in a worker role.

# [.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Monitoring.cs" id="Snippet_DisableNagle":::

# [.NET v11](#tab/dotnet11)

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);
ServicePoint queueServicePoint = ServicePointManager.FindServicePoint(storageAccount.QueueEndpoint);
queueServicePoint.UseNagleAlgorithm = false;
```

---

You should check the client-side logs to see how many requests your client application is submitting, and check for general .NET related performance bottlenecks in your client such as CPU, .NET garbage collection, network utilization, or memory. As a starting point for troubleshooting .NET client applications, see [Debugging, Tracing, and Profiling](https://msdn.microsoft.com/library/7fe0dd2y).

#### Investigating network latency issues
Typically, high end-to-end latency caused by the network is due to transient conditions. You can investigate both transient and persistent network issues such as dropped packets by using tools such as Wireshark.

For more information about using Wireshark to troubleshoot network issues, see "[Appendix 2: Using Wireshark to capture network traffic]."

### <a name="metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency"></a>Metrics show low AverageE2ELatency and low AverageServerLatency but the client is experiencing high latency
In this scenario, the most likely cause is a delay in the storage requests reaching the storage service. You should investigate why requests from the client are not making it through to the blob service.

One possible reason for the client delaying sending requests is that there are a limited number of available connections or threads.

Also check whether the client is performing multiple retries, and investigate the reason if it is. To determine whether the client is performing multiple retries, you can:

* Examine the Storage Analytics logs. If multiple retries are happening, you will see multiple operations with the same client request ID but with different server request IDs.
* Examine the client logs. Verbose logging will indicate that a retry has occurred.
* Debug your code, and check the properties of the **OperationContext** object associated with the request. If the operation has retried, the **RequestResults** property will include multiple unique server request IDs. You can also check the start and end times for each request. For more information, see the code sample in the section [Server request ID].

If there are no issues in the client, you should investigate potential network issues such as packet loss. You can use tools such as Wireshark to investigate network issues.

For more information about using Wireshark to troubleshoot network issues, see "[Appendix 2: Using Wireshark to capture network traffic]."

### <a name="metrics-show-high-AverageServerLatency"></a>Metrics show high AverageServerLatency
In the case of high **AverageServerLatency** for blob download requests, you should use the Storage Logging logs to see if there are repeated requests for the same blob (or set of blobs). For blob upload requests, you should investigate what block size the client is using (for example, blocks less than 64 K in size can result in overheads unless the reads are also in less than 64 K chunks), and if multiple clients are uploading blocks to the same blob in parallel. You should also check the per-minute metrics for spikes in the number of requests that result in exceeding the per second scalability targets: also see "[Metrics show an increase in PercentTimeoutError]."

If you are seeing high **AverageServerLatency** for blob download requests when there are repeated requests the same blob or set of blobs, then you should consider caching these blobs using Azure Cache or the Azure Content Delivery Network (CDN). For upload requests, you can improve the throughput by using a larger block size. For queries to tables, it is also possible to implement client-side caching on clients that perform the same query operations and where the data doesn't change frequently.

High **AverageServerLatency** values can also be a symptom of poorly designed tables or queries that result in scan operations or that follow the append/prepend anti-pattern. For more information, see "[Metrics show an increase in PercentThrottlingError]".

> [!NOTE]
> You can find a comprehensive checklist performance checklist here: [Microsoft Azure Storage Performance and Scalability Checklist](storage-performance-checklist.md).
>
>

### <a name="you-are-experiencing-unexpected-delays-in-message-delivery"></a>You are experiencing unexpected delays in message delivery on a queue
If you are experiencing a delay between the time an application adds a message to a queue and the time it becomes available to read from the queue, then you should take the following steps to diagnose the issue:

* Verify the application is successfully adding the messages to the queue. Check that the application is not retrying the **AddMessage** method several times before succeeding. The Storage Client Library logs will show any repeated retries of storage operations.
* Verify there is no clock skew between the worker role that adds the message to the queue and the worker role that reads the message from the queue that makes it appear as if there is a delay in processing.
* Check if the worker role that reads the messages from the queue is failing. If a queue client calls the **GetMessage** method but fails to respond with an acknowledgment, the message will remain invisible on the queue until the **invisibilityTimeout** period expires. At this point, the message becomes available for processing again.
* Check if the queue length is growing over time. This can occur if you do not have sufficient workers available to process all of the messages that other workers are placing on the queue. Also check the metrics to see if delete requests are failing and the dequeue count on messages, which might indicate repeated failed attempts to delete the message.
* Examine the Storage Logging logs for any queue operations that have higher than expected **E2ELatency** and **ServerLatency** values over a longer period of time than usual.

### <a name="metrics-show-an-increase-in-PercentThrottlingError"></a>Metrics show an increase in PercentThrottlingError
Throttling errors occur when you exceed the scalability targets of a storage service. The storage service throttles to ensure that no single client or tenant can use the service at the expense of others. For more information, see [Scalability and performance targets for standard storage accounts](scalability-targets-standard-account.md) for details on scalability targets for storage accounts and performance targets for partitions within storage accounts.

If the **PercentThrottlingError** metric show an increase in the percentage of requests that are failing with a throttling error, you need to investigate one of two scenarios:

* [Transient increase in PercentThrottlingError]
* [Permanent increase in PercentThrottlingError error]

An increase in **PercentThrottlingError** often occurs at the same time as an increase in the number of storage requests, or when you are initially load testing your application. This may also manifest itself in the client as "503 Server Busy" or "500 Operation Timeout" HTTP status messages from storage operations.

#### <a name="transient-increase-in-PercentThrottlingError"></a>Transient increase in PercentThrottlingError
If you are seeing spikes in the value of **PercentThrottlingError** that coincide with periods of high activity for the application, you implement an exponential (not linear) back-off strategy for retries in your client. Back-off retries reduce the immediate load on the partition and help your application to smooth out spikes in traffic. For more information about how to implement retry policies using the Storage Client Library, see the [Microsoft.Azure.Storage.RetryPolicies namespace](/dotnet/api/microsoft.azure.storage.retrypolicies).

> [!NOTE]
> You may also see spikes in the value of **PercentThrottlingError** that do not coincide with periods of high activity for the application: the most likely cause here is the storage service moving partitions to improve load balancing.
>
>

#### <a name="permanent-increase-in-PercentThrottlingError"></a>Permanent increase in PercentThrottlingError error
If you are seeing a consistently high value for **PercentThrottlingError** following a permanent increase in your transaction volumes, or when you are performing your initial load tests on your application, then you need to evaluate how your application is using storage partitions and whether it is approaching the scalability targets for a storage account. For example, if you are seeing throttling errors on a queue (which counts as a single partition), then you should consider using additional queues to spread the transactions across multiple partitions. If you are seeing throttling errors on a table, you need to consider using a different partitioning scheme to spread your transactions across multiple partitions by using a wider range of partition key values. One common cause of this issue is the prepend/append anti-pattern where you select the date as the partition key and then all data on a particular day is written to one partition: under load, this can result in a write bottleneck. Either consider a different partitioning design or evaluate whether using blob storage might be a better solution. Also check whether throttling is occurring as a result of spikes in your traffic and investigate ways of smoothing your pattern of requests.

If you distribute your transactions across multiple partitions, you must still be aware of the scalability limits set for the storage account. For example, if you used ten queues each processing the maximum of 2,000 1KB messages per second, you will be at the overall limit of 20,000 messages per second for the storage account. If you need to process more than 20,000 entities per second, you should consider using multiple storage accounts. You should also bear in mind that the size of your requests and entities has an impact on when the storage service throttles your clients: if you have larger requests and entities, you may be throttled sooner.

Inefficient query design can also cause you to hit the scalability limits for table partitions. For example, a query with a filter that only selects one percent of the entities in a partition but that scans all the entities in a partition will need to access each entity. Every entity read will count towards the total number of transactions in that partition; therefore, you can easily reach the scalability targets.

> [!NOTE]
> Your performance testing should reveal any inefficient query designs in your application.
>
>

### <a name="metrics-show-an-increase-in-PercentTimeoutError"></a>Metrics show an increase in PercentTimeoutError
Your metrics show an increase in **PercentTimeoutError** for one of your storage services. At the same time, the client receives a high volume of "500 Operation Timeout" HTTP status messages from storage operations.

> [!NOTE]
> You may see timeout errors temporarily as the storage service load balances requests by moving a partition to a new server.
>
>

The **PercentTimeoutError** metric is an aggregation of the following metrics: **ClientTimeoutError**, **AnonymousClientTimeoutError**, **SASClientTimeoutError**, **ServerTimeoutError**, **AnonymousServerTimeoutError**, and **SASServerTimeoutError**.

The server timeouts are caused by an error on the server. The client timeouts happen because an operation on the server has exceeded the timeout specified by the client; for example, a client using the Storage Client Library can set a timeout for an operation by using the **ServerTimeout** property of the **QueueRequestOptions** class.

Server timeouts indicate a problem with the storage service that requires further investigation. You can use metrics to see if you are hitting the scalability limits for the service and to identify any spikes in traffic that might be causing this problem. If the problem is intermittent, it may be due to load-balancing activity in the service. If the problem is persistent and is not caused by your application hitting the scalability limits of the service, you should raise a support issue. For client timeouts, you must decide if the timeout is set to an appropriate value in the client and either change the timeout value set in the client or investigate how you can improve the performance of the operations in the storage service, for example by optimizing your table queries or reducing the size of your messages.

### <a name="metrics-show-an-increase-in-PercentNetworkError"></a>Metrics show an increase in PercentNetworkError
Your metrics show an increase in **PercentNetworkError** for one of your storage services. The **PercentNetworkError** metric is an aggregation of the following metrics: **NetworkError**, **AnonymousNetworkError**, and **SASNetworkError**. These occur when the storage service detects a network error when the client makes a storage request.

The most common cause of this error is a client disconnecting before a timeout expires in the storage service. Investigate the code in your client to understand why and when the client disconnects from the storage service. You can also use Wireshark, or Tcping to investigate network connectivity issues from the client. These tools are described in the [Appendices].

### <a name="the-client-is-receiving-403-messages"></a>The client is receiving HTTP 403 (Forbidden) messages
If your client application is throwing HTTP 403 (Forbidden) errors, a likely cause is that the client is using an expired Shared Access Signature (SAS) when it sends a storage request (although other possible causes include clock skew, invalid keys, and empty headers). If an expired SAS key is the cause, you will not see any entries in the server-side Storage Logging log data. The following table shows a sample from the client-side log generated by the Storage Client Library that illustrates this issue occurring:

| Source | Verbosity | Verbosity | Client request ID | Operation text |
| --- | --- | --- | --- | --- |
| Microsoft.Azure.Storage |Information |3 |85d077ab-… |Starting operation with location Primary per location mode PrimaryOnly. |
| Microsoft.Azure.Storage |Information |3 |85d077ab -… |Starting synchronous request to <https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/Synchronous_and_Asynchronous_Requests#Synchronous_request> |
| Microsoft.Azure.Storage |Information |3 |85d077ab -… |Waiting for response. |
| Microsoft.Azure.Storage |Warning |2 |85d077ab -… |Exception thrown while waiting for response: The remote server returned an error: (403) Forbidden. |
| Microsoft.Azure.Storage |Information |3 |85d077ab -… |Response received. Status code = 403, Request ID = 9d67c64a-64ed-4b0d-9515-3b14bbcdc63d, Content-MD5 = , ETag = . |
| Microsoft.Azure.Storage |Warning |2 |85d077ab -… |Exception thrown during the operation: The remote server returned an error: (403) Forbidden.. |
| Microsoft.Azure.Storage |Information |3 |85d077ab -… |Checking if the operation should be retried. Retry count = 0, HTTP status code = 403, Exception = The remote server returned an error: (403) Forbidden.. |
| Microsoft.Azure.Storage |Information |3 |85d077ab -… |The next location has been set to Primary, based on the location mode. |
| Microsoft.Azure.Storage |Error |1 |85d077ab -… |Retry policy did not allow for a retry. Failing with The remote server returned an error: (403) Forbidden. |

In this scenario, you should investigate why the SAS token is expiring before the client sends the token to the server:

* Typically, you should not set a start time when you create a SAS for a client to use immediately. If there are small clock differences between the host generating the SAS using the current time and the storage service, then it is possible for the storage service to receive a SAS that is not yet valid.
* Do not set a very short expiry time on a SAS. Again, small clock differences between the host generating the SAS and the storage service can lead to a SAS apparently expiring earlier than anticipated.
* Does the version parameter in the SAS key (for example **sv=2015-04-05**) match the version of the Storage Client Library you are using? We recommend that you always use the latest version of the [Storage Client Library](https://www.nuget.org/packages/WindowsAzure.Storage/).
* If you regenerate your storage access keys, any existing SAS tokens may be invalidated. This issue may arise if you generate SAS tokens with a long expiry time for client applications to cache.

If you are using the Storage Client Library to generate SAS tokens, then it is easy to build a valid token. However, if you are using the Storage REST API and constructing the SAS tokens by hand, see [Delegating Access with a Shared Access Signature](https://msdn.microsoft.com/library/azure/ee395415.aspx).

### <a name="the-client-is-receiving-404-messages"></a>The client is receiving HTTP 404 (Not found) messages
If the client application receives an HTTP 404 (Not found) message from the server, this implies that the object the client was attempting to use (such as an entity, table, blob, container, or queue) does not exist in the storage service. There are a number of possible reasons for this, such as:

* [The client or another process previously deleted the object]
* [A Shared Access Signature (SAS) authorization issue]
* [Client-side JavaScript code does not have permission to access the object]
* [Network failure]

#### <a name="client-previously-deleted-the-object"></a>The client or another process previously deleted the object
In scenarios where the client is attempting to read, update, or delete data in a storage service it is usually easy to identify in the server-side logs a previous operation that deleted the object in question from the storage service. Often, the log data shows that another user or process deleted the object. In the server-side Storage Logging log, the operation-type and requested-object-key columns show when a client deleted an object.

In the scenario where a client is attempting to insert an object, it may not be immediately obvious why this results in an HTTP 404 (Not found) response given that the client is creating a new object. However, if the client is creating a blob it must be able to find the blob container, if the client is creating a message it must be able to find a queue, and if the client is adding a row it must be able to find the table.

You can use the client-side log from the Storage Client Library to gain a more detailed understanding of when the client sends specific requests to the storage service.

The following client-side log generated by the Storage Client library illustrates the problem when the client cannot find the container for the blob it is creating. This log includes details of the following storage operations:

| Request ID | Operation |
| --- | --- |
| 07b26a5d-... |**DeleteIfExists** method to delete the blob container. Note that this operation includes a **HEAD** request to check for the existence of the container. |
| e2d06d78… |**CreateIfNotExists** method to create the blob container. Note that this operation includes a **HEAD** request that checks for the existence of the container. The **HEAD** returns a 404 message but continues. |
| de8b1c3c-... |**UploadFromStream** method to create the blob. The **PUT** request fails with a 404 message |

Log entries:

| Request ID | Operation Text |
| --- | --- |
| 07b26a5d-... |Starting synchronous request to `https://domemaildist.blob.core.windows.net/azuremmblobcontainer`. |
| 07b26a5d-... |StringToSign = HEAD............x-ms-client-request-id:07b26a5d-....x-ms-date:Tue, 03 Jun 2014 10:33:11 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| 07b26a5d-... |Waiting for response. |
| 07b26a5d-... |Response received. Status code = 200, Request ID = eeead849-...Content-MD5 = , ETag =    &quot;0x8D14D2DC63D059B&quot;. |
| 07b26a5d-... |Response headers were processed successfully, proceeding with the rest of the operation. |
| 07b26a5d-... |Downloading response body. |
| 07b26a5d-... |Operation completed successfully. |
| 07b26a5d-... |Starting synchronous request to `https://domemaildist.blob.core.windows.net/azuremmblobcontainer`. |
| 07b26a5d-... |StringToSign = DELETE............x-ms-client-request-id:07b26a5d-....x-ms-date:Tue, 03 Jun 2014 10:33:12    GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| 07b26a5d-... |Waiting for response. |
| 07b26a5d-... |Response received. Status code = 202, Request ID = 6ab2a4cf-..., Content-MD5 = , ETag = . |
| 07b26a5d-... |Response headers were processed successfully, proceeding with the rest of the operation. |
| 07b26a5d-... |Downloading response body. |
| 07b26a5d-... |Operation completed successfully. |
| e2d06d78-... |Starting asynchronous request to `https://domemaildist.blob.core.windows.net/azuremmblobcontainer`.</td> |
| e2d06d78-... |StringToSign = HEAD............x-ms-client-request-id:e2d06d78-....x-ms-date:Tue, 03 Jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| e2d06d78-... |Waiting for response. |
| de8b1c3c-... |Starting synchronous request to `https://domemaildist.blob.core.windows.net/azuremmblobcontainer/blobCreated.txt`. |
| de8b1c3c-... |StringToSign = PUT...64.qCmF+TQLPhq/YYK50mP9ZQ==........x-ms-blob-type:BlockBlob.x-ms-client-request-id:de8b1c3c-....x-ms-date:Tue, 03 Jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer/blobCreated.txt. |
| de8b1c3c-... |Preparing to write request data. |
| e2d06d78-... |Exception thrown while waiting for response: The remote server returned an error: (404) Not Found.. |
| e2d06d78-... |Response received. Status code = 404, Request ID = 353ae3bc-..., Content-MD5 = , ETag = . |
| e2d06d78-... |Response headers were processed successfully, proceeding with the rest of the operation. |
| e2d06d78-... |Downloading response body. |
| e2d06d78-... |Operation completed successfully. |
| e2d06d78-... |Starting asynchronous request to `https://domemaildist.blob.core.windows.net/azuremmblobcontainer`. |
| e2d06d78-... |StringToSign = PUT...0.........x-ms-client-request-id:e2d06d78-....x-ms-date:Tue, 03 Jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| e2d06d78-... |Waiting for response. |
| de8b1c3c-... |Writing request data. |
| de8b1c3c-... |Waiting for response. |
| e2d06d78-... |Exception thrown while waiting for response: The remote server returned an error: (409) Conflict.. |
| e2d06d78-... |Response received. Status code = 409, Request ID = c27da20e-..., Content-MD5 = , ETag = . |
| e2d06d78-... |Downloading error response body. |
| de8b1c3c-... |Exception thrown while waiting for response: The remote server returned an error: (404) Not Found.. |
| de8b1c3c-... |Response received. Status code = 404, Request ID = 0eaeab3e-..., Content-MD5 = , ETag = . |
| de8b1c3c-... |Exception thrown during the operation: The remote server returned an error: (404) Not Found.. |
| de8b1c3c-... |Retry policy did not allow for a retry. Failing with The remote server returned an error: (404) Not Found.. |
| e2d06d78-... |Retry policy did not allow for a retry. Failing with The remote server returned an error: (409) Conflict.. |

In this example, the log shows that the client is interleaving requests from the **CreateIfNotExists** method (request ID e2d06d78…) with the requests from the **UploadFromStream** method (de8b1c3c-...). This interleaving happens because the client application is invoking these methods asynchronously. Modify the asynchronous code in the client to ensure that it creates the container before attempting to upload any data to a blob in that container. Ideally, you should create all your containers in advance.

#### <a name="SAS-authorization-issue"></a>A Shared Access Signature (SAS) authorization issue
If the client application attempts to use a SAS key that does not include the necessary permissions for the operation, the storage service returns an HTTP 404 (Not found) message to the client. At the same time, you will also see a non-zero value for **SASAuthorizationError** in the metrics.

The following table shows a sample server-side log message from the Storage Logging log file:

| Name | Value |
| --- | --- |
| Request start time | 2014-05-30T06:17:48.4473697Z |
| Operation type     | GetBlobProperties            |
| Request status     | SASAuthorizationError        |
| HTTP status code   | 404                            |
| Authentication type| Sas                          |
| Service type       | Blob                         |
| Request URL         | `https://domemaildist.blob.core.windows.net/azureimblobcontainer/blobCreatedViaSAS.txt` |
| &nbsp;                 |   ?sv=2014-02-14&sr=c&si=mypolicy&sig=XXXXX&;api-version=2014-02-14 |
| Request ID header  | a1f348d5-8032-4912-93ef-b393e5252a3b |
| Client request ID  | 2d064953-8436-4ee0-aa0c-65cb874f7929 |


Investigate why your client application is attempting to perform an operation for which it has not been granted permissions.

#### <a name="JavaScript-code-does-not-have-permission"></a>Client-side JavaScript code does not have permission to access the object
If you are using a JavaScript client and the storage service is returning HTTP 404 messages, you check for the following JavaScript errors in the browser:

```
SEC7120: Origin http://localhost:56309 not found in Access-Control-Allow-Origin header.
SCRIPT7002: XMLHttpRequest: Network Error 0x80070005, Access is denied.
```

> [!NOTE]
> You can use the F12 Developer Tools in Internet Explorer to trace the messages exchanged between the browser and the storage service when you are troubleshooting client-side JavaScript issues.
>
>

These errors occur because the web browser implements the [same origin policy](https://www.w3.org/Security/wiki/Same_Origin_Policy) security restriction that prevents a web page from calling an API in a different domain from the domain the page comes from.

To work around the JavaScript issue, you can configure Cross Origin Resource Sharing (CORS) for the storage service the client is accessing. For more information, see [Cross-Origin Resource Sharing (CORS) Support for Azure Storage Services](https://msdn.microsoft.com/library/azure/dn535601.aspx).

The following code sample shows how to configure your blob service to allow JavaScript running in the Contoso domain to access a blob in your blob storage service:

# [.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Monitoring.cs" id="Snippet_ConfigureCORS":::

# [.NET v11](#tab/dotnet11)

```csharp
CloudBlobClient client = new CloudBlobClient(blobEndpoint, new StorageCredentials(accountName, accountKey));
// Set the service properties.
ServiceProperties sp = client.GetServiceProperties();
sp.DefaultServiceVersion = "2013-08-15";
CorsRule cr = new CorsRule();
cr.AllowedHeaders.Add("*");
cr.AllowedMethods = CorsHttpMethods.Get | CorsHttpMethods.Put;
cr.AllowedOrigins.Add("http://www.contoso.com");
cr.ExposedHeaders.Add("x-ms-*");
cr.MaxAgeInSeconds = 5;
sp.Cors.CorsRules.Clear();
sp.Cors.CorsRules.Add(cr);
client.SetServiceProperties(sp);
```

---

#### <a name="network-failure"></a>Network Failure
In some circumstances, lost network packets can lead to the storage service returning HTTP 404 messages to the client. For example, when your client application is deleting an entity from the table service you see the client throw a storage exception reporting an "HTTP 404 (Not Found)" status message from the table service. When you investigate the table in the table storage service, you see that the service did delete the entity as requested.

The exception details in the client include the request ID (7e84f12d…) assigned by the table service for the request: you can use this information to locate the request details in the server-side storage logs by searching in the **request-id-header** column in the log file. You could also use the metrics to identify when failures such as this occur and then search the log files based on the time the metrics recorded this error. This log entry shows that the delete failed with an "HTTP (404) Client Other Error" status message. The same log entry also includes the request ID generated by the client in the **client-request-id** column (813ea74f…).

The server-side log also includes another entry with the same **client-request-id** value (813ea74f…) for a successful delete operation for the same entity, and from the same client. This successful delete operation took place very shortly before the failed delete request.

The most likely cause of this scenario is that the client sent a delete request for the entity to the table service, which succeeded, but did not receive an acknowledgment from the server (perhaps due to a temporary network issue). The client then automatically retried the operation (using the same **client-request-id**), and this retry failed because the entity had already been deleted.

If this problem occurs frequently, you should investigate why the client is failing to receive acknowledgments from the table service. If the problem is intermittent, you should trap the "HTTP (404) Not Found" error and log it in the client, but allow the client to continue.

### <a name="the-client-is-receiving-409-messages"></a>The client is receiving HTTP 409 (Conflict) messages
The following table shows an extract from the server-side log for two client operations: **DeleteIfExists** followed immediately by **CreateIfNotExists** using the same blob container name. Each client operation results in two requests sent to the server, first a **GetContainerProperties** request to check if the container exists, followed by the **DeleteContainer** or **CreateContainer** request.

| Timestamp | Operation | Result | Container name | Client request ID |
| --- | --- | --- | --- | --- |
| 05:10:13.7167225 |GetContainerProperties |200 |mmcont |c9f52c89-… |
| 05:10:13.8167325 |DeleteContainer |202 |mmcont |c9f52c89-… |
| 05:10:13.8987407 |GetContainerProperties |404 |mmcont |bc881924-… |
| 05:10:14.2147723 |CreateContainer |409 |mmcont |bc881924-… |

The code in the client application deletes and then immediately recreates a blob container using the same name: the **CreateIfNotExists** method (Client request ID bc881924-…) eventually fails with the HTTP 409 (Conflict) error. When a client deletes blob containers, tables, or queues there is a brief period before the name becomes available again.

The client application should use unique container names whenever it creates new containers if the delete/recreate pattern is common.

### <a name="metrics-show-low-percent-success"></a>Metrics show low PercentSuccess or analytics log entries have operations with transaction status of ClientOtherErrors
The **PercentSuccess** metric captures the percent of operations that were successful based on their HTTP Status Code. Operations with status codes of 2XX count as successful, whereas operations with status codes in 3XX, 4XX and 5XX ranges are counted as unsuccessful and lower the **PercentSuccess** metric value. In the server-side storage log files, these operations are recorded with a transaction status of **ClientOtherErrors**.

It is important to note that these operations have completed successfully and therefore do not affect other metrics such as availability. Some examples of operations that execute successfully but that can result in unsuccessful HTTP status codes include:

* **ResourceNotFound** (Not Found 404), for example from a GET request to a blob that does not exist.
* **ResourceAlreadyExists** (Conflict 409), for example from a **CreateIfNotExist** operation where the resource already exists.
* **ConditionNotMet** (Not Modified 304), for example from a conditional operation such as when a client sends an **ETag** value and an HTTP **If-None-Match** header to request an image only if it has been updated since the last operation.

You can find a list of common REST API error codes that the storage services return on the page [Common REST API Error Codes](https://msdn.microsoft.com/library/azure/dd179357.aspx).

### <a name="capacity-metrics-show-an-unexpected-increase"></a>Capacity metrics show an unexpected increase in storage capacity usage
If you see sudden, unexpected changes in capacity usage in your storage account, you can investigate the reasons by first looking at your availability metrics; for example, an increase in the number of failed delete requests might lead to an increase in the amount of blob storage you are using as application-specific cleanup operations you might have expected to be freeing up space may not be working as expected (for example, because the SAS tokens used for freeing up space have expired).

### <a name="your-issue-arises-from-using-the-storage-emulator"></a>Your issue arises from using the storage emulator for development or test
You typically use the storage emulator during development and test to avoid the requirement for an Azure storage account. The common issues that can occur when you are using the storage emulator are:

* [Feature "X" is not working in the storage emulator]
* [Error "The value for one of the HTTP headers is not in the correct format" when using the storage emulator]
* [Running the storage emulator requires administrative privileges]

#### <a name="feature-X-is-not-working"></a>Feature "X" is not working in the storage emulator
The storage emulator does not support all of the features of the Azure storage services such as the file service. For more information, see [Use the Azure Storage Emulator for Development and Testing](storage-use-emulator.md).

For those features that the storage emulator does not support, use the Azure storage service in the cloud.

#### <a name="error-HTTP-header-not-correct-format"></a>Error "The value for one of the HTTP headers is not in the correct format" when using the storage emulator
You are testing your application that uses the Storage Client Library against the local storage emulator and method calls such as **CreateIfNotExists** fail with the error message "The value for one of the HTTP headers is not in the correct format." This indicates that the version of the storage emulator you are using does not support the version of the storage client library you are using. The Storage Client Library adds the header **x-ms-version** to all the requests it makes. If the storage emulator does not recognize the value in the **x-ms-version** header, it rejects the request.

You can use the Storage Library Client logs to see the value of the **x-ms-version header** it is sending. You can also see the value of the **x-ms-version header** if you use Fiddler to trace the requests from your client application.

This scenario typically occurs if you install and use the latest version of the Storage Client Library without updating the storage emulator. You should either install the latest version of the storage emulator, or use cloud storage instead of the emulator for development and test.

#### <a name="storage-emulator-requires-administrative-privileges"></a>Running the storage emulator requires administrative privileges
You are prompted for administrator credentials when you run the storage emulator. This only occurs when you are initializing the storage emulator for the first time. After you have initialized the storage emulator, you do not need administrative privileges to run it again.

For more information, see [Use the Azure Storage Emulator for Development and Testing](storage-use-emulator.md). You can also initialize the storage emulator in Visual Studio, which will also require administrative privileges.

### <a name="you-are-encountering-problems-installing-the-Windows-Azure-SDK"></a>You are encountering problems installing the Azure SDK for .NET
When you try to install the SDK, it fails trying to install the storage emulator on your local machine. The installation log contains one of the following messages:

* CAQuietExec:  Error: Unable to access SQL instance
* CAQuietExec:  Error: Unable to create database

The cause is an issue with existing LocalDB installation. By default, the storage emulator uses LocalDB to persist data when it simulates the Azure storage services. You can reset your LocalDB instance by running the following commands in a command-prompt window before trying to install the SDK.

```
sqllocaldb stop v11.0
sqllocaldb delete v11.0
delete %USERPROFILE%\WAStorageEmulatorDb3*.*
sqllocaldb create v11.0
```

The **delete** command removes any old database files from previous installations of the storage emulator.

### <a name="you-have-a-different-issue-with-a-storage-service"></a>You have a different issue with a storage service
If the previous troubleshooting sections do not include the issue you are having with a storage service, you should adopt the following approach to diagnosing and troubleshooting your issue.

* Check your metrics to see if there is any change from your expected base-line behavior. From the metrics, you may be able to determine whether the issue is transient or permanent, and which storage operations the issue is affecting.
* You can use the metrics information to help you search your server-side log data for more detailed information about any errors that are occurring. This information may help you troubleshoot and resolve the issue.
* If the information in the server-side logs is not sufficient to troubleshoot the issue successfully, you can use the Storage Client Library client-side logs to investigate the behavior of your client application, and tools such as Fiddler, Wireshark to investigate your network.

For more information about using Fiddler, see "[Appendix 1: Using Fiddler to capture HTTP and HTTPS traffic]."

For more information about using Wireshark, see "[Appendix 2: Using Wireshark to capture network traffic]."

## <a name="appendices"></a>Appendices
The appendices describe several tools that you may find useful when you are diagnosing and troubleshooting issues with Azure Storage (and other services). These tools are not part of Azure Storage and some are third-party products. As such, the tools discussed in these appendices are not covered by any support agreement you may have with Microsoft Azure or Azure Storage, and therefore as part of your evaluation process you should examine the licensing and support options available from the providers of these tools.

### <a name="appendix-1"></a>Appendix 1: Using Fiddler to capture HTTP and HTTPS traffic
[Fiddler](https://www.telerik.com/fiddler) is a useful tool for analyzing the HTTP and HTTPS traffic between your client application and the Azure storage service you are using.

> [!NOTE]
> Fiddler can decode HTTPS traffic; you should read the Fiddler documentation carefully to understand how it does this, and to understand the security implications.
>
>

This appendix provides a brief walkthrough of how to configure Fiddler to capture traffic between the local machine where you have installed Fiddler and the Azure storage services.

After you have launched Fiddler, it will begin capturing HTTP and HTTPS traffic on your local machine. The following are some useful commands for controlling Fiddler:

* Stop and start capturing traffic. On the main menu, go to **File** and then click **Capture Traffic** to toggle capturing on and off.
* Save captured traffic data. On the main menu, go to **File**, click **Save**, and then click **All Sessions**: this enables you to save the traffic in a Session Archive file. You can reload a Session Archive later for analysis, or send it if requested to Microsoft support.

To limit the amount of traffic that Fiddler captures, you can use filters that you configure in the **Filters** tab. The following screenshot shows a filter that captures only traffic sent to the **contosoemaildist.table.core.windows.net** storage endpoint:

![Screenshot that shows a filter that captures only traffic sent to the contosoemaildist.table.core.windows.net storage endpoint.][5]

### <a name="appendix-2"></a>Appendix 2: Using Wireshark to capture network traffic
[Wireshark](https://www.wireshark.org/) is a network protocol analyzer that enables you to view detailed packet information for a wide range of network protocols.

The following procedure shows you how to capture detailed packet information for traffic from the local machine where you installed Wireshark to the table service in your Azure storage account.

1. Launch Wireshark on your local machine.
2. In the **Start** section, select the local network interface or interfaces that are connected to the internet.
3. Click **Capture Options**.
4. Add a filter to the **Capture Filter** textbox. For example, **host contosoemaildist.table.core.windows.net** will configure Wireshark to capture only packets sent to or from the table service endpoint in the **contosoemaildist** storage account. Check out the [complete list of Capture Filters](https://wiki.wireshark.org/CaptureFilters).

   ![Screenshot that shows how to add a filter to the Capture Filter textbox.][6]
5. Click **Start**. Wireshark will now capture all the packets send to or from the table service endpoint as you use your client application on your local machine.
6. When you have finished, on the main menu click **Capture** and then **Stop**.
7. To save the captured data in a Wireshark Capture File, on the main menu click **File** and then **Save**.

WireShark will highlight any errors that exist in the **packetlist** window. You can also use the **Expert Info** window (click **Analyze**, then **Expert Info**) to view a summary of errors and warnings.

![Screenshot that shows the Expert Info window where you can view a summary of errors and warnings.][7]

You can also choose to view the TCP data as the application layer sees it by right-clicking on the TCP data and selecting **Follow TCP Stream**. This is useful if you captured your dump without a capture filter. For more information, see [Following TCP Streams](https://www.wireshark.org/docs/wsug_html_chunked/ChAdvFollowTCPSection.html).

![Screenshot that shows how to view the TCP data as the application layer sees it.][8]

> [!NOTE]
> For more information about using Wireshark, see the [Wireshark Users Guide](https://www.wireshark.org/docs/wsug_html_chunked).
>
>

### <a name="appendix-4"></a>Appendix 4: Using Excel to view metrics and log data
Many tools enable you to download the Storage Metrics data from Azure table storage in a delimited format that makes it easy to load the data into Excel for viewing and analysis. Storage Logging data from Azure blob storage is already in a delimited format that you can load into Excel. However, you will need to add appropriate column headings based in the information at [Storage Analytics Log Format](https://msdn.microsoft.com/library/azure/hh343259.aspx) and [Storage Analytics Metrics Table Schema](https://msdn.microsoft.com/library/azure/hh343264.aspx).

To import your Storage Logging data into Excel after you download it from blob storage:

* On the **Data** menu, click **From Text**.
* Browse to the log file you want to view and click **Import**.
* On step 1 of the **Text Import Wizard**, select **Delimited**.

On step 1 of the **Text Import Wizard**, select **Semicolon** as the only delimiter and choose double-quote as the **Text qualifier**. Then click **Finish** and choose where to place the data in your workbook.

### <a name="appendix-5"></a>Appendix 5: Monitoring with Application Insights for Azure DevOps
You can also use the Application Insights feature for Azure DevOps as part of your performance and availability monitoring. This tool can:

* Make sure your web service is available and responsive. Whether your app is a web site or a device app that uses a web service, it can test your URL every few minutes from locations around the world, and let you know if there's a problem.
* Quickly diagnose any performance issues or exceptions in your web service. Find out if CPU or other resources are being stretched, get stack traces from exceptions, and easily search through log traces. If the app's performance drops below acceptable limits, Microsoft can send you an email. You can monitor both .NET and Java web services.

You can find more information at [What is Application Insights](../../azure-monitor/app/app-insights-overview.md).

## Next steps

For more information about analytics in Azure Storage, see these resources:

* [Monitor a storage account in the Azure portal](storage-monitor-storage-account.md)
* [Storage analytics](storage-analytics.md)
* [Storage analytics metrics](storage-analytics-metrics.md)
* [Storage analytics metrics table schema](/rest/api/storageservices/storage-analytics-metrics-table-schema)
* [Storage analytics logs](storage-analytics-logging.md)
* [Storage analytics log format](/rest/api/storageservices/storage-analytics-log-format)

<!--Anchors-->
[Introduction]: #introduction
[How this guide is organized]: #how-this-guide-is-organized

[Monitoring your storage service]: #monitoring-your-storage-service
[Monitoring service health]: #monitoring-service-health
[Monitoring capacity]: #monitoring-capacity
[Monitoring availability]: #monitoring-availability
[Monitoring performance]: #monitoring-performance

[Diagnosing storage issues]: #diagnosing-storage-issues
[Service health issues]: #service-health-issues
[Performance issues]: #performance-issues
[Diagnosing errors]: #diagnosing-errors
[Storage emulator issues]: #storage-emulator-issues
[Storage logging tools]: #storage-logging-tools
[Using network logging tools]: #using-network-logging-tools

[End-to-end tracing]: #end-to-end-tracing
[Correlating log data]: #correlating-log-data
[Client request ID]: #client-request-id
[Server request ID]: #server-request-id
[Timestamps]: #timestamps

[Troubleshooting guidance]: #troubleshooting-guidance
[Metrics show high AverageE2ELatency and low AverageServerLatency]: #metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency
[Metrics show low AverageE2ELatency and low AverageServerLatency but the client is experiencing high latency]: #metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency
[Metrics show high AverageServerLatency]: #metrics-show-high-AverageServerLatency
[You are experiencing unexpected delays in message delivery on a queue]: #you-are-experiencing-unexpected-delays-in-message-delivery

[Metrics show an increase in PercentThrottlingError]: #metrics-show-an-increase-in-PercentThrottlingError
[Transient increase in PercentThrottlingError]: #transient-increase-in-PercentThrottlingError
[Permanent increase in PercentThrottlingError error]: #permanent-increase-in-PercentThrottlingError
[Metrics show an increase in PercentTimeoutError]: #metrics-show-an-increase-in-PercentTimeoutError
[Metrics show an increase in PercentNetworkError]: #metrics-show-an-increase-in-PercentNetworkError

[The client is receiving HTTP 403 (Forbidden) messages]: #the-client-is-receiving-403-messages
[The client is receiving HTTP 404 (Not found) messages]: #the-client-is-receiving-404-messages
[The client or another process previously deleted the object]: #client-previously-deleted-the-object
[A Shared Access Signature (SAS) authorization issue]: #SAS-authorization-issue
[Client-side JavaScript code does not have permission to access the object]: #JavaScript-code-does-not-have-permission
[Network failure]: #network-failure
[The client is receiving HTTP 409 (Conflict) messages]: #the-client-is-receiving-409-messages

[Metrics show low PercentSuccess or analytics log entries have operations with transaction status of ClientOtherErrors]: #metrics-show-low-percent-success
[Capacity metrics show an unexpected increase in storage capacity usage]: #capacity-metrics-show-an-unexpected-increase
[Your issue arises from using the storage emulator for development or test]: #your-issue-arises-from-using-the-storage-emulator
[Feature "X" is not working in the storage emulator]: #feature-X-is-not-working
[Error "The value for one of the HTTP headers is not in the correct format" when using the storage emulator]: #error-HTTP-header-not-correct-format
[Running the storage emulator requires administrative privileges]: #storage-emulator-requires-administrative-privileges
[You are encountering problems installing the Azure SDK for .NET]: #you-are-encountering-problems-installing-the-Windows-Azure-SDK
[You have a different issue with a storage service]: #you-have-a-different-issue-with-a-storage-service

[Appendices]: #appendices
[Appendix 1: Using Fiddler to capture HTTP and HTTPS traffic]: #appendix-1
[Appendix 2: Using Wireshark to capture network traffic]: #appendix-2
[Appendix 4: Using Excel to view metrics and log data]: #appendix-4
[Appendix 5: Monitoring with Application Insights for Azure DevOps]: #appendix-5

<!--Image references-->
[1]: ./media/storage-monitoring-diagnosing-troubleshooting/overview.png
[3]: ./media/storage-monitoring-diagnosing-troubleshooting/hour-minute-metrics.png
[4]: ./media/storage-monitoring-diagnosing-troubleshooting/high-e2e-latency.png
[5]: ./media/storage-monitoring-diagnosing-troubleshooting/fiddler-screenshot.png
[6]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-1.png
[7]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-2.png
[8]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-3.png
[9]: ./media/storage-monitoring-diagnosing-troubleshooting/mma-screenshot-1.png
[10]: ./media/storage-monitoring-diagnosing-troubleshooting/mma-screenshot-2.png
