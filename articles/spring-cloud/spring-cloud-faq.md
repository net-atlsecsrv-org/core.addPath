---
title: Frequently asked questions about Azure Spring Cloud | Microsoft Docs
description: This article answers frequently asked questions about Azure Spring Cloud.
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: brendm
ms.custom: devx-track-java
---

# Azure Spring Cloud FAQ

This article answers frequently asked questions about Azure Spring Cloud.

## General

### Why Azure Spring Cloud?

Azure Spring Cloud provides a platform as a service (PaaS) for Spring Cloud developers. Azure Spring Cloud manages your application infrastructure so that you can focus on application code and business logic. Core features built into Azure Spring Cloud include Eureka, Config Server, Service Registry Server, Pivotal Build Service, Blue-green deployment, and more. This service also enables developers to bind their applications with other Azure services, such as Azure Cosmos DB, Azure Database for MySQL, and Azure Cache for Redis.

Azure Spring Cloud enhances the application diagnostics experience for developers and operators by integrating Azure Monitor, Application Insights, and Log Analytics.

### How secure is Azure Spring Cloud?

Security and privacy are among the top priorities for Azure and Azure Spring Cloud customers. Azure helps ensure that only customers have access to application data, logs, or configurations by securely encrypting all of this data. 

* The service instances in Azure Spring Cloud are isolated from each other.
* Azure Spring Cloud provides complete TLS/SSL and certificate management.
* Critical security patches for OpenJDK and Spring Cloud runtimes are applied to Azure Spring Cloud as soon as possible.

### In which regions is Azure Spring Cloud available?

East US, West US 2, West Europe, and Southeast Asia.

### What are the known limitations of Azure Spring Cloud?

During preview release, Azure Spring Cloud has the following known limitations:

* `spring.application.name` will be overridden by the application name that's used to create each application.
* `server.port` defaults to ports 80/443. If any other value is applied, it will be overridden to 80/443.
* The Azure portal and Azure Resource Manager templates do not support uploading application packages. You can upload application packages only by deploying the application via the Azure CLI.

### What pricing tiers are available? 
Which one should I use and what are the limits within each tier?
* Azure Spring Cloud offers two pricing tiers: Basic and Standard. The Basic tier is targeted for Dev/Test and trying out Azure Spring Cloud. The Standard tier is optimized to run general purpose production traffic. See [Azure Spring Cloud pricing details](https://azure.microsoft.com/pricing/details/spring-cloud/) for limits and feature level comparison.

### How can I provide feedback and report issues?

If you encounter any issues with Azure Spring Cloud, create an [Azure Support Request](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). To submit a feature request or provide feedback, go to [Azure Feedback](https://feedback.azure.com/forums/34192--general-feedback).

## Development

### I am a Spring Cloud developer but new to Azure. What is the quickest way for me to learn how to develop an Azure Spring Cloud application?

For the quickest way to get started with Azure Spring Cloud, follow the instructions in [Quickstart: Launch an Azure Spring Cloud application by using the Azure portal](spring-cloud-quickstart-launch-app-portal.md).

### What Java runtime does Azure Spring Cloud support?

Azure Spring Cloud supports Java 8 and 11. See [Java runtime and OS versions](#java-runtime-and-os-versions)

### Where can I view my Spring Cloud application logs and metrics?

Find metrics in the App Overview tab and the [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#interacting-with-azure-monitor-metrics) tab.

Azure Spring Cloud supports exporting Spring Cloud application logs and metrics to Azure Storage, EventHub, and [Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs#log-queries). The table name in Log Analytics is *AppPlatformLogsforSpring*. To learn how to enable it, see [Diagnostic services](diagnostic-services.md).

### Does Azure Spring Cloud support distributed tracing?

Yes. For more information, see [Tutorial: Use Distributed Tracing with Azure Spring Cloud](spring-cloud-tutorial-distributed-tracing.md).

### What resource types does Service Binding support?

Three services are currently supported:
* Azure Cosmos DB
* Azure Database for MySQL
* Azure Cache for Redis.

### Can I view, add, or move persistent volumes from inside my applications?

Yes.

### When I delete/move an Azure Spring Cloud service instance, will its extension resources be deleted/moved as well?

It depends on the logic of resource providers that own the extension resources. The extension resources of a `Microsoft.AppPlatform` instance do not belong to the same namespace, so the behavior varies by resource provider. For example, the delete/move operation will not cascade to the **diagnostics settings** resources. If a new Azure Spring Cloud instance is provisioned with the same resource ID as the deleted one, or if the previous Azure Spring Cloud instance is moved back, the previous **diagnostics settings** resources continue extending it.

## Java runtime and OS versions

### Which versions of Java runtime are supported in Azure Spring Cloud?

Azure Spring Cloud supports Java LTS versions with the most recent builds, currently June 2020, Java 8 build 252 and Java 11 build 7 are supported. See [Install the JDK for Azure and Azure Stack](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-install)

### Who built these Java runtimes?

Azul Systems. The Azul Zulu for Azure - Enterprise Edition JDK builds are a no-cost, multi-platform, production-ready distribution of the OpenJDK for Azure and Azure Stack backed by Microsoft and Azul Systems. They contain all the components for building and running Java SE applications.

### How often will Java runtimes get updated?

LTS and MTS JDK releases have quarterly security updates, bug fixes, and critical out-of-band updates and patches as needed. This support includes backports to Java 7 and 8 of security updates and bug fixes reported in newer versions of Java, like Java 11.

### How long will Java 8 and Java 11 LTS versions be supported?

See [Java long-term support for Azure and Azure Stack](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-long-term-support).

* Java 8 LTS will be supported until December 2030.
* Java 11 LTS will be supported until September 2027.

### How can I download a supported Java runtime for local development?

See [Install the JDK for Azure and Azure Stack](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-install).

### What is the retire policy for older Java runtimes?

Public notice will be sent out at 12 months before any old runtime version is retired. You will have 12 months to migrate to a later version.

* Subscription admins will get email notification when we will retire a Java version.
* The retire information will be published in the documentation.

### How can I get support for issues at the Java runtime level?

You can open a support ticket with Azure Support.  See [How to create an Azure support request](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).

### What is the operation system to run my apps?

The most recent Ubuntu LTS version is used, currently [Ubuntu 20.04 LTS (Focal Fossa)](https://releases.ubuntu.com/focal/) is the default OS.

### How often will OS security patches be applied?

Security patches applicable to Azure Spring Cloud will be rolled out to production at monthly basis.
Critical security patches (CVE score >= 9) applicable to Azure Spring Cloud will be rolled out as soon as possible.

## Deployment

### Does Azure Spring Cloud support blue-green deployment?
Yes. For more information, see [Set up a staging environment](spring-cloud-howto-staging-environment.md).

### Can I access Kubernetes to manipulate my application containers?

No.  Azure Spring Cloud abstracts the developer from the underlying architecture, allowing you to concentrate on application code and business logic.

### Does Azure Spring Cloud support building containers from source?

Yes. For more information, see [Launch your Spring Cloud application from source code](spring-cloud-launch-from-source.md).

### Does Azure Spring Cloud support autoscaling in app instances?

No.

### What are the best practices for migrating existing Spring Cloud microservices to Azure Spring Cloud?

As you're migrating existing Spring Cloud microservices to Azure Spring Cloud, it's a good idea to observe the following best practices:
* All application dependencies need to be resolved.
* Prepare your configuration entries, environment variables, and JVM parameters so that you can compare them with the deployment in Azure Spring Cloud.
* If you want to use Service Binding, go through your Azure services and ensure that you've set the appropriate access permissions.
* We recommend that you remove or disable any embedded services that might conflict with services that are managed by Azure Spring Cloud, such as our Service Discovery service, Config Server, and so on.
* We recommend that you use official, stable Pivotal Spring libraries. Unofficial, beta, or forked versions of Pivotal Spring libraries have no service-level agreement (SLA) support.

After the migration, monitor your CPU/RAM metrics and network traffic to ensure that the application instances are scaled appropriately.

## Trouble Shooting

### What are the impacts of service registry rarely unavailable?

In some rarely happened scenario, you may see some errors like 
```
RetryableEurekaHttpClient: Request execution failure with status code 401; retrying on another server if available
```
from your logs of applications. This issue introduced by spring framework with very low rate due to network unstable or other network issues. 

There should be no impacts to user experience, eureka client has both heartbeat and retry policy to take care of this. You could consider it as one transient error and skip it safely.

We will enhance this part and avoid this error from users’ applications in short future.


## Next steps

If you have further questions, see the [Azure Spring Cloud troubleshooting guide](spring-cloud-troubleshoot.md).
