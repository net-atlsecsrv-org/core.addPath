---
title: Distributed Tracing in Azure Application Insights | Microsoft Docs
description: Provides information about Microsoft's support for distributed tracing through our partnership in the OpenCensus project
ms.topic: conceptual
ms.custom: devx-track-dotnet
author: nikmd23
ms.author: nimolnar
ms.date: 09/17/2018

ms.reviewer: mbullwin
---

# What is Distributed Tracing?

The advent of modern cloud and [microservices](https://azure.com/microservices) architectures has given rise to simple, independently deployable services that can help reduce costs while increasing availability and throughput. But while these movements have made individual services easier to understand as a whole, they've made overall systems more difficult to reason about and debug.

In monolithic architectures, we've gotten used to debugging with call stacks. Call stacks are brilliant tools for showing the flow of execution (Method A called Method B, which called Method C), along with details and parameters about each of those calls. This is great for monoliths or services running on a single process, but how do we debug when the call is across a process boundary, not simply a reference on the local stack? 

That's where distributed tracing comes in.  

Distributed tracing is the equivalent of call stacks for modern cloud and microservices architectures, with the addition of a simplistic performance profiler thrown in. In Azure Monitor, we provide two experiences for consuming distributed trace data. The first is our [transaction diagnostics](./transaction-diagnostics.md) view, which is like a call stack with a time dimension added in. The transaction diagnostics view provides visibility into one single transaction/request, and is helpful for finding the root cause of reliability issues and performance bottlenecks on a per request basis.

Azure Monitor also offers an [application map](./app-map.md) view which aggregates many transactions to show a topological view of how the systems interact, and what the average performance and error rates are. 

## How to Enable Distributed Tracing

Enabling distributed tracing across the services in an application is as simple as adding the proper agent, SDK, or library to each service, based on the language the service was implemented in.

## Enabling via Application Insights through auto-instrumentation or SDKs

The Application Insights agents and/or SDKs for .NET, .NET Core, Java, Node.js, and JavaScript all support distributed tracing natively. Instructions for installing and configuring each Application Insights SDK are available below:

* [.NET](asp-net.md)
* [.NET Core](asp-net-core.md)
* [Java](./java-in-process-agent.md)
* [Node.js](../learn/nodejs-quick-start.md)
* [JavaScript](./javascript.md)
* [Python](opencensus-python.md)

With the proper Application Insights SDK installed and configured, tracing information is automatically collected for popular frameworks, libraries, and technologies by SDK dependency auto-collectors. The full list of supported technologies is available in [the Dependency auto-collection documentation](./auto-collect-dependencies.md).

 Additionally, any technology can be tracked manually with a call to [TrackDependency](./api-custom-events-metrics.md) on the [TelemetryClient](./api-custom-events-metrics.md).

## Enable via OpenCensus

In addition to the Application Insights SDKs, Application Insights also supports distributed tracing through [OpenCensus](https://opencensus.io/). OpenCensus is an open source, vendor-agnostic, single distribution of libraries to provide metrics collection and distributed tracing for services. It also enables the open source community to enable distributed tracing with popular technologies like Redis, Memcached, or MongoDB. [Microsoft collaborates on OpenCensus with several other monitoring and cloud partners](https://open.microsoft.com/2018/06/13/microsoft-joins-the-opencensus-project/).

[Python](opencensus-python.md) 

The OpenCensus website maintains API reference documentation for [Python](https://opencensus.io/api/python/trace/usage.html) and [Go](https://godoc.org/go.opencensus.io), as well as various different guides for using OpenCensus. 

## Next steps

* [OpenCensus Python usage guide](https://opencensus.io/api/python/trace/usage.html)
* [Application map](./app-map.md)
* [End-to-end performance monitoring](../learn/tutorial-performance.md)

