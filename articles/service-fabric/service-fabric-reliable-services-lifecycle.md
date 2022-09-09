---
title: Overview of the lifecycle of Reliable Services 
description: Learn about the lifecycle events in an Azure Service Fabric Reliable Services application for stateful and stateless services.
author: masnider

ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
---

# Reliable Services lifecycle overview
> [!div class="op_single_selector"]
> * [C# on Windows](service-fabric-reliable-services-lifecycle.md)
> * [Java on Linux](service-fabric-reliable-services-lifecycle-java.md)
>
>

When you're thinking about the lifecycles of Azure Service Fabric Reliable Services, the basics of the lifecycle are the most important. In general, the lifecycle includes the following:

- During startup:
  - Services are constructed.
  - The services have an opportunity to construct and return zero or more listeners.
  - Any returned listeners are opened, allowing communication with the service.
  - The service's **RunAsync** method is called, allowing the service to do long-running tasks or background work.
- During shutdown:
  - The cancellation token passed to **RunAsync** is canceled, and the listeners are closed.
  - After the listeners close, the service object itself is destructed.

There are details around the exact ordering of these events. The order of events can change slightly depending on whether the Reliable Service is stateless or stateful. In addition, for stateful services, we must deal with the Primary swap scenario. During this sequence, the role of Primary is transferred to another replica (or comes back) without the service shutting down. Finally, we must think about error or failure conditions.

## Stateless service startup
The lifecycle of a stateless service is straightforward. Here's the order of events:

1. The service is constructed.
2. Then, in parallel, two things happen:
    - `StatelessService.CreateServiceInstanceListeners()` is invoked and any returned listeners are opened. `ICommunicationListener.OpenAsync()` is called on each listener.
    - The service's `StatelessService.RunAsync()` method is called.
3. If present, the service's `StatelessService.OnOpenAsync()` method is called. This call is an uncommon override, but it is available. Extended service initialization tasks can be started at this time.

Keep in mind that there is no ordering between the calls to create and open the listeners and **RunAsync**. The listeners can open before **RunAsync** is started. Similarly, you can invoke **RunAsync** before the communication listeners are open or even constructed. If any synchronization is required, it is left as an exercise to the implementer. Here are some common solutions:

  - Sometimes listeners can't function until some other information is created or work is done. For stateless services, that work can usually be done in other locations, such as the following: 
    - In the service's constructor.
    - During the `CreateServiceInstanceListeners()` call.
    - As a part of the construction of the listener itself.
  - Sometimes the code in **RunAsync** doesn't start until the listeners are open. In this case, additional coordination is necessary. One common solution is that there is a flag within the listeners that indicates when they have finished. This flag is then checked in **RunAsync** before continuing to actual work.

## Stateless service shutdown
For shutting down a stateless service, the same pattern is followed, just in reverse:

1. In parallel:
    - Any open listeners are closed. `ICommunicationListener.CloseAsync()` is called on each listener.
    - The cancellation token passed to `RunAsync()` is canceled. A check of the cancellation token's `IsCancellationRequested` property returns true, and if called, the token's `ThrowIfCancellationRequested` method throws an `OperationCanceledException`.
2. After `CloseAsync()` finishes on each listener and `RunAsync()` also finishes, the service's `StatelessService.OnCloseAsync()` method is called, if present.  OnCloseAsync is called when the stateless service instance is going to be gracefully shut down. This can occur when the service's code is being upgraded, the service instance is being moved due to load balancing, or a transient fault is detected. It is uncommon to override `StatelessService.OnCloseAsync()`, but it  can be used to safely close resources, stop background processing, finish saving external state, or close down existing connections.
3. After `StatelessService.OnCloseAsync()` finishes, the service object is destructed.

## Stateful service startup
Stateful services have a similar pattern to stateless services, with a few changes. For starting up a stateful service, the order of events is as follows:

1. The service is constructed.
2. `StatefulServiceBase.OnOpenAsync()` is called. This call is not commonly overridden in the service.
3. The following things happen in parallel:
    - `StatefulServiceBase.CreateServiceReplicaListeners()` is invoked. 
      - If the service is a Primary service, all returned listeners are opened. `ICommunicationListener.OpenAsync()` is called on each listener.
      - If the service is a Secondary service, only those listeners marked as `ListenOnSecondary = true` are opened. Having listeners that are open on secondaries is less common.
    - If the service is currently a Primary, the service's `StatefulServiceBase.RunAsync()` method is called.
4. After all the replica listener's `OpenAsync()` calls finish and `RunAsync()` is called, `StatefulServiceBase.OnChangeRoleAsync()` is called. This call is not commonly overridden in the service.

Similar to stateless services, there's no coordination between the order in which the listeners are created and opened and when **RunAsync** is called. If you need coordination, the solutions are much the same. There is one additional case for stateful service. Say that the calls that arrive at the communication listeners require information kept inside some [Reliable Collections](service-fabric-reliable-services-reliable-collections.md).

   > [!NOTE]  
   > Because the communication listeners could open before the reliable collections are readable or writeable, and before **RunAsync** could start, some additional coordination is necessary. The simplest and most common solution is for the communication listeners to return an error code that the client uses to know to retry the request.

## Stateful service shutdown
Like stateless services, the lifecycle events during shutdown are the same as during startup, but reversed. When a stateful service is being shut down, the following events occur:

1. In parallel:
    - Any open listeners are closed. `ICommunicationListener.CloseAsync()` is called on each listener.
    - The cancellation token passed to `RunAsync()` is canceled. A check of the cancellation token's `IsCancellationRequested` property returns true, and if called, the token's `ThrowIfCancellationRequested` method throws an `OperationCanceledException`.
2. After `CloseAsync()` finishes on each listener and `RunAsync()` also finishes, the service's `StatefulServiceBase.OnChangeRoleAsync()` is called. This call is not commonly overridden in the service.

   > [!NOTE]  
   > The need to wait for **RunAsync** to finish is only necessary if this replica is a Primary replica.

3. After the `StatefulServiceBase.OnChangeRoleAsync()` method finishes, the `StatefulServiceBase.OnCloseAsync()` method is called. This call is an uncommon override, but it is available.
3. After `StatefulServiceBase.OnCloseAsync()` finishes, the service object is destructed.

## Stateful service Primary swaps
While a stateful service is running, only the Primary replicas of that stateful services have their communication listeners opened and their **RunAsync** method called. Secondary replicas are constructed, but see no further calls. While a stateful service is running, the replica that's currently the Primary can change as a result of fault or cluster balancing optimization. What does this mean in terms of the lifecycle events that a replica can see? The behavior the stateful replica sees depends on whether it is the replica being demoted or promoted during the swap.

### For the Primary that's demoted
For the Primary replica that's demoted, Service Fabric needs this replica to stop processing messages and quit any background work it is doing. As a result, this step looks like it did when the service is shut down. One difference is that the service isn't destructed or closed because it remains as a Secondary. The following APIs are called:

1. In parallel:
    - Any open listeners are closed. `ICommunicationListener.CloseAsync()` is called on each listener.
    - The cancellation token passed to `RunAsync()` is canceled. A check of the cancellation token's `IsCancellationRequested` property returns true, and if called, the token's `ThrowIfCancellationRequested` method throws an `OperationCanceledException`.
2. After `CloseAsync()` finishes on each listener and `RunAsync()` also finishes, the service's `StatefulServiceBase.OnChangeRoleAsync()` is called. This call is not commonly overridden in the service.

### For the Secondary that's promoted
Similarly, Service Fabric needs the Secondary replica that's promoted to start listening for messages on the wire and start any background tasks it needs to complete. As a result, this process looks like it did when the service is created, except that the replica itself already exists. The following APIs are called:

1. In parallel:
    - `StatefulServiceBase.CreateServiceReplicaListeners()` is invoked and any returned listeners are opened. `ICommunicationListener.OpenAsync()` is called on each listener.
    - The service's `StatefulServiceBase.RunAsync()` method is called.
2. After all the replica listener's `OpenAsync()` calls finish and `RunAsync()` is called, `StatefulServiceBase.OnChangeRoleAsync()` is called. This call is not commonly overridden in the service.

### Common issues during stateful service shutdown and Primary demotion
Service Fabric changes the Primary of a stateful service for a variety of reasons. The most common are [cluster rebalancing](service-fabric-cluster-resource-manager-balancing.md) and [application upgrade](service-fabric-application-upgrade.md). During these operations (as well as during normal service shutdown, like you'd see if the service was deleted), it is important that the service respect the `CancellationToken`. 

Services that do not handle cancellation cleanly can experience several issues. These operations are slow because Service Fabric waits for the services to stop gracefully. This can ultimately lead to failed upgrades that time out and roll back. Failure to honor the cancellation token can also cause imbalanced clusters. Clusters become unbalanced because nodes get hot, but the services can't be rebalanced because it takes too long to move them elsewhere. 

Because the services are stateful, it is also likely that they use the [Reliable Collections](service-fabric-reliable-services-reliable-collections.md). In Service Fabric, when a Primary is demoted, one of the first things that happens is that write access to the underlying state is revoked. This leads to a second set of issues that can affect the service lifecycle. The collections return exceptions based on the timing and whether the replica is being moved or shut down. These exceptions should be handled correctly. Exceptions thrown by Service Fabric fall into permanent [(`FabricException`)](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception?view=azure-dotnet) and transient [(`FabricTransientException`)](https://docs.microsoft.com/dotnet/api/system.fabric.fabrictransientexception?view=azure-dotnet) categories. Permanent exceptions should be logged and thrown while the transient exceptions can be retried based on some retry logic.

Handling the exceptions that come from use of the `ReliableCollections` in conjunction with service lifecycle events is an important part of testing and validating a Reliable Service. We recommend that you always run your service under load while performing upgrades and [chaos testing](service-fabric-controlled-chaos.md) before deploying to production. These basic steps help ensure that your service is correctly implemented and handles lifecycle events correctly.


## Notes on the service lifecycle
  - Both the `RunAsync()` method and the `CreateServiceReplicaListeners/CreateServiceInstanceListeners` calls are optional. A service can have one of them, both, or neither. For example, if the service does all its work in response to user calls, there is no need for it to implement `RunAsync()`. Only the communication listeners and their associated code are necessary. Similarly, creating and returning communication listeners is optional, as the service can have only background work to do, and so only needs to implement `RunAsync()`.
  - It is valid for a service to complete `RunAsync()` successfully and return from it. Completing is not a failure condition. Completing `RunAsync()` indicates that the background work of the service has finished. For stateful reliable services, `RunAsync()` is called again if the replica is demoted from Primary to Secondary and then promoted back to Primary.
  - If a service exits from `RunAsync()` by throwing some unexpected exception, this constitutes a failure. The service object is shut down and a health error is reported.
  - Although there is no time limit on returning from these methods, you immediately lose the ability to write to Reliable Collections, and therefore, cannot complete any real work. We recommended that you return as quickly as possible upon receiving the cancellation request. If your service does not respond to these API calls in a reasonable amount of time, Service Fabric can forcibly terminate your service. Usually this only happens during application upgrades or when a service is being deleted. This timeout is 15 minutes by default.
  - Failures in the `OnCloseAsync()` path result in `OnAbort()` being called, which is a last-chance best-effort opportunity for the service to clean up and release any resources that they have claimed. This is generally called when a permanent fault is detected on the node, or when Service Fabric cannot reliably manage the service instance's lifecycle due to internal failures.
  - `OnChangeRoleAsync()` is called when the stateful service replica is changing role, for example to primary or secondary. Primary replicas are given write status (are allowed to create and write to Reliable Collections). Secondary replicas are given read status (can only read from existing Reliable Collections). Most work in a stateful service is performed at the primary replica. Secondary replicas can perform read-only validation, report generation, data mining, or other read-only jobs.

## Next steps
- [Introduction to Reliable Services](service-fabric-reliable-services-introduction.md)
- [Reliable Services quick start](service-fabric-reliable-services-quick-start.md)
- [Replicas and instances](service-fabric-concepts-replica-lifecycle.md)
