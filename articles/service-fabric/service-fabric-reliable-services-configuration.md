---
title: Configure Azure Service Fabric Reliable Services 
description: Learn about configuring stateful Reliable Services in an Azure Service Fabric application globally and for a single service.
author: sumukhs

ms.topic: conceptual
ms.date: 10/02/2017
ms.author: sumukhs
---
# Configure stateful reliable services
There are two sets of configuration settings for reliable services. One set is global for all reliable services in the cluster while the other set is specific to a particular reliable service.

## Global Configuration
The global reliable service configuration is specified in the cluster manifest for the cluster under the KtlLogger section. It allows configuration of the shared log location and size plus the global memory limits used by the logger. The cluster manifest is a single XML file that holds settings and configurations that apply to all nodes and services in the cluster. The file is typically called ClusterManifest.xml. You can see the cluster manifest for your cluster using the Get-ServiceFabricClusterManifest powershell command.

### Configuration names
| Name | Unit | Default value | Remarks |
| --- | --- | --- | --- |
| WriteBufferMemoryPoolMinimumInKB |Kilobytes |8388608 |Minimum number of KB to allocate in kernel mode for the logger write buffer memory pool. This memory pool is used for caching state information before writing to disk. |
| WriteBufferMemoryPoolMaximumInKB |Kilobytes |No Limit |Maximum size to which the logger write buffer memory pool can grow. |
| SharedLogId |GUID |"" |Specifies a unique GUID to use for identifying the default shared log file used by all reliable services on all nodes in the cluster that do not specify the SharedLogId in their service specific configuration. If SharedLogId is specified, then SharedLogPath must also be specified. |
| SharedLogPath |Fully qualified path name |"" |Specifies the fully qualified path where the shared log file used by all reliable services on all nodes in the cluster that do not specify the SharedLogPath in their service specific configuration. However, if SharedLogPath is specified, then SharedLogId must also be specified. |
| SharedLogSizeInMB |Megabytes |8192 |Specifies the number of MB of disk space to statically allocate for the shared log. The value must be 2048 or larger. |

In Azure ARM or on-premises JSON template, the example below shows how to change the shared transaction log that gets created to back any reliable collections for stateful services.

```json
"fabricSettings": [{
    "name": "KtlLogger",
    "parameters": [{
        "name": "SharedLogSizeInMB",
        "value": "4096"
    }]
}]
```

### Sample local developer cluster manifest section
If you want to change this on your local development environment, you need to edit the local clustermanifest.xml file.

```xml
   <Section Name="KtlLogger">
     <Parameter Name="SharedLogSizeInMB" Value="4096"/>
     <Parameter Name="WriteBufferMemoryPoolMinimumInKB" Value="8192" />
     <Parameter Name="WriteBufferMemoryPoolMaximumInKB" Value="8192" />
     <Parameter Name="SharedLogId" Value="{7668BB54-FE9C-48ed-81AC-FF89E60ED2EF}"/>
     <Parameter Name="SharedLogPath" Value="f:\SharedLog.Log"/>
   </Section>
```

### Remarks
The logger has a global pool of memory allocated from non paged kernel memory that is available to all reliable services on a node for caching state data before being written to the dedicated log associated with the reliable service replica. The pool size is controlled by the WriteBufferMemoryPoolMinimumInKB and WriteBufferMemoryPoolMaximumInKB settings. WriteBufferMemoryPoolMinimumInKB specifies both the initial size of this memory pool and the lowest size to which the memory pool may shrink. WriteBufferMemoryPoolMaximumInKB is the highest size to which the memory pool may grow. Each reliable service replica that is opened may increase the size of the memory pool by a system determined amount up to WriteBufferMemoryPoolMaximumInKB. If there is more demand for memory from the memory pool than is available, requests for memory will be delayed until memory is available. Therefore if the write buffer memory pool is too small for a particular configuration then performance may suffer.

The SharedLogId and SharedLogPath settings are always used together to define the GUID and location for the default shared log for all nodes in the cluster. The default shared log is used for all reliable services that do not specify the settings in the settings.xml for the specific service. For best performance, shared log files should be placed on disks that are used solely for the shared log file to reduce contention.

SharedLogSizeInMB specifies the amount of disk space to preallocate for the default shared log on all nodes.  SharedLogId and SharedLogPath do not need to be specified in order for SharedLogSizeInMB to be specified.

## Service Specific Configuration
You can modify stateful Reliable Services' default configurations by using the configuration package (Config) or the service implementation (code).

* **Config** - Configuration via the config package is accomplished by changing the Settings.xml file that is generated in the Microsoft Visual Studio package root under the Config folder for each service in the application.
* **Code**   - Configuration via code is accomplished by creating a ReliableStateManager using a  ReliableStateManagerConfiguration object with the appropriate options set.

By default, the Azure Service Fabric runtime looks for predefined section names in the Settings.xml file and consumes the configuration values while creating the underlying runtime components.

> [!NOTE]
> Do **not** delete the section names of the following configurations in the Settings.xml file that is generated in the Visual Studio solution unless you plan to configure your service via code.
> Renaming the config package or section names will require a code change when configuring the ReliableStateManager.
> 
> 

### Replicator security configuration
Replicator security configurations are used to secure the communication channel that is used during replication. This means that services will not be able to see each other's replication traffic, ensuring that the data that is made highly available is also secure. By default, an empty security configuration section prevents replication security.

> [!IMPORTANT]
> On Linux nodes, certificates must be PEM-formatted. To learn more about locating and configuring certificates for Linux, see [Configure certificates on Linux](./service-fabric-configure-certificates-linux.md). 
> 
> 

### Default section name
ReplicatorSecurityConfig

> [!NOTE]
> To change this section name, override the replicatorSecuritySectionName parameter to the ReliableStateManagerConfiguration constructor when creating the ReliableStateManager for this service.
> 
> 

### Replicator configuration
Replicator configurations configure the replicator that is responsible for making the stateful Reliable Service's state highly reliable by replicating and persisting the state locally.
The default configuration is generated by the Visual Studio template and should suffice. This section talks about additional configurations that are available to tune the replicator.

### Default section name
ReplicatorConfig

> [!NOTE]
> To change this section name, override the replicatorSettingsSectionName parameter to the ReliableStateManagerConfiguration constructor when creating the ReliableStateManager for this service.
> 
> 

### Configuration names
| Name | Unit | Default value | Remarks |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |Seconds |0.015 |Time period for which the replicator at the secondary waits after receiving an operation before sending back an acknowledgement to the primary. Any other acknowledgements to be sent for operations processed within this interval are sent as one response. |
| ReplicatorEndpoint |N/A |No default--required parameter |IP address and port that the primary/secondary replicator will use to communicate with other replicators in the replica set. This should reference a TCP resource endpoint in the service manifest. Refer to [Service manifest resources](service-fabric-service-manifest-resources.md) to read more about defining endpoint resources in a service manifest. |
| MaxPrimaryReplicationQueueSize |Number of operations |8192 |Maximum number of operations in the primary queue. An operation is freed up after the primary replicator receives an acknowledgement from all the secondary replicators. This value must be greater than 64 and a power of 2. |
| MaxSecondaryReplicationQueueSize |Number of operations |16384 |Maximum number of operations in the secondary queue. An operation is freed up after making its state highly available through persistence. This value must be greater than 64 and a power of 2. |
| CheckpointThresholdInMB |MB |50 |Amount of log file space after which the state is checkpointed. |
| MaxRecordSizeInKB |KB |1024 |Largest record size that the replicator may write in the log. This value must be a multiple of 4 and greater than 16. |
| MinLogSizeInMB |MB |0 (system determined) |Minimum size of the transactional log. The log will not be allowed to truncate to a size below this setting. 0 indicates that the replicator will determine the minimum log size. Increasing this value increases the possibility of doing partial copies and incremental backups since chances of relevant log records being truncated is lowered. |
| TruncationThresholdFactor |Factor |2 |Determines at what size of the log, truncation will be triggered. Truncation threshold is determined by MinLogSizeInMB multiplied by TruncationThresholdFactor. TruncationThresholdFactor must be greater than 1. MinLogSizeInMB * TruncationThresholdFactor must be less than MaxStreamSizeInMB. |
| ThrottlingThresholdFactor |Factor |4 |Determines at what size of the log, the replica will start being throttled. Throttling threshold (in MB) is determined by Max((MinLogSizeInMB * ThrottlingThresholdFactor),(CheckpointThresholdInMB * ThrottlingThresholdFactor)). Throttling threshold (in MB) must be greater than truncation threshold (in MB). Truncation threshold (in MB) must be less than MaxStreamSizeInMB. |
| MaxAccumulatedBackupLogSizeInMB |MB |800 |Max accumulated size (in MB) of backup logs in a given backup log chain. An incremental backup requests will fail if the incremental backup would generate a backup log that would cause the accumulated backup logs since the relevant full backup to be larger than this size. In such cases, user is required to take a full backup. |
| SharedLogId |GUID |"" |Specifies a unique GUID to use for identifying the shared log file used with this replica. Typically, services should not use this setting. However, if SharedLogId is specified, then SharedLogPath must also be specified. |
| SharedLogPath |Fully qualified path name |"" |Specifies the fully qualified path where the shared log file for this replica will be created. Typically, services should not use this setting. However, if SharedLogPath is specified, then SharedLogId must also be specified. |
| SlowApiMonitoringDuration |Seconds |300 |Sets the monitoring interval for managed API calls. Example: user provided backup callback function. After the interval has passed, a warning health report will be sent to the Health Manager. |
| LogTruncationIntervalSeconds |Seconds |0 |Configurable interval at which log truncation will be initiated on each replica. It is used to ensure log is also truncated based on time instead of just log size. This setting also forces purge of deleted entries in reliable dictionary. Hence it can be used to ensure deleted items are purged in a timely manner. |
| EnableStableReads |Boolean |False |Enabling stable reads restricts secondary replicas to returning values which have been quorum-acked. |

### Sample configuration via code
```csharp
class Program
{
    /// <summary>
    /// This is the entry point of the service host process.
    /// </summary>
    static void Main()
    {
        ServiceRuntime.RegisterServiceAsync("HelloWorldStatefulType",
            context => new HelloWorldStateful(context, 
                new ReliableStateManager(context, 
        new ReliableStateManagerConfiguration(
                        new ReliableStateManagerReplicatorSettings()
            {
                RetryInterval = TimeSpan.FromSeconds(3)
                        }
            )))).GetAwaiter().GetResult();
    }
}    
```
```csharp
class MyStatefulService : StatefulService
{
    public MyStatefulService(StatefulServiceContext context, IReliableStateManagerReplica stateManager)
        : base(context, stateManager)
    { }
    ...
}
```


### Sample configuration file
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="ReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="ReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
      <Parameter Name="CheckpointThresholdInMB" Value="512" />
   </Section>
   <Section Name="ReplicatorSecurityConfig">
      <Parameter Name="CredentialType" Value="X509" />
      <Parameter Name="FindType" Value="FindByThumbprint" />
      <Parameter Name="FindValue" Value="9d c9 06 b1 69 dc 4f af fd 16 97 ac 78 1e 80 67 90 74 9d 2f" />
      <Parameter Name="StoreLocation" Value="LocalMachine" />
      <Parameter Name="StoreName" Value="My" />
      <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
      <Parameter Name="AllowedCommonNames" Value="My-Test-SAN1-Alice,My-Test-SAN1-Bob" />
   </Section>
</Settings>
```


### Remarks
BatchAcknowledgementInterval controls replication latency. A value of '0' results in the lowest possible latency, at the cost of throughput (as more acknowledgement messages must be sent and processed, each containing fewer acknowledgements).
The larger the value for BatchAcknowledgementInterval, the higher the overall replication throughput, at the cost of higher operation latency. This directly translates to the latency of transaction commits.

The value for CheckpointThresholdInMB controls the amount of disk space that the replicator can use to store state information in the replica's dedicated log file. Increasing this to a higher value than the default could result in faster reconfiguration times when a new replica is added to the set. This is due to the partial state transfer that takes place due to the availability of more history of operations in the log. This can potentially increase the recovery time of a replica after a crash.

The MaxRecordSizeInKB setting defines the maximum size of a record that can be written by the replicator into the log file. In most cases, the default 1024-KB record size is optimal. However, if the service is causing larger data items to be part of the state information, then this value might need to be increased. There is little benefit in making MaxRecordSizeInKB smaller than 1024, as smaller records use only the space needed for the smaller record. We expect that this value would need to be changed in only rare cases.

The SharedLogId and SharedLogPath settings are always used together to make a service use a separate shared log from the default shared log for the node. For best efficiency, as many services as
possible should specify the same shared log. Shared log files should be placed on disks that are used solely for the shared log file to reduce head movement contention. We expect that this value would need to be changed in only rare cases.

## Next steps
* [Debug your Service Fabric application in Visual Studio](service-fabric-debugging-your-application.md)
* [Developer reference for Reliable Services](/previous-versions/azure/dn706529(v=azure.100))
