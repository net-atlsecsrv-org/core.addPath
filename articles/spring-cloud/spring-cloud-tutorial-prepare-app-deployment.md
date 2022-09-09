---
title: How to prepare an application for deployment in Azure Spring Cloud
description: Learn how to prepare an application for deployment to Azure Spring Cloud.
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
---

# Prepare an application for deployment in Azure Spring Cloud

::: zone pivot="programming-language-csharp"
Azure Spring Cloud provides robust services to host, monitor, scale, and update a Steeltoe app. This article shows how to prepare an existing Steeltoe application for deployment to Azure Spring Cloud. 

This article explains the dependencies, configuration, and code that are required to run a .NET Core Steeltoe app in Azure Spring Cloud. For information about how to deploy an application to Azure Spring Cloud, see [Deploy your first Azure Spring Cloud application](spring-cloud-quickstart.md).

>[!Note]
> Steeltoe support for Azure Spring Cloud is currently offered as a public preview. Public preview offerings allow customers to experiment with new features prior to their official release.  Public preview features and services are not meant for production use.  For more information about support during previews, see the [FAQ](https://azure.microsoft.com/support/faq/) or file a [Support request](../azure-portal/supportability/how-to-create-azure-support-request.md).

##  Supported versions

Azure Spring Cloud supports:

* .NET Core 3.1
* Steeltoe 2.4

## Dependencies

Install the [Microsoft.Azure.SpringCloud.Client](https://www.nuget.org/packages/Microsoft.Azure.SpringCloud.Client/) package.

## Update Program.cs

In the `Program.Main` method, call the `UseAzureSpringCloudService` method:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        })
        .UseAzureSpringCloudService();
```

## Enable Eureka Server service discovery

In the configuration source that will be used when the app runs in Azure Spring Cloud, set `spring.application.name` to the same name as the Azure Spring Cloud app to which the project will be deployed.

For example, if you deploy a .NET project named `EurekaDataProvider` to an Azure Spring Cloud app named `planet-weather-provider` the *appSettings.json* file should include the following JSON:

```json
"spring": {
  "application": {
    "name": "planet-weather-provider"
  }
}
```

## Use service discovery

To call a service by using the Eureka Server service discovery, make HTTP requests to `http://<app_name>` where `app_name` is the value of `spring.application.name` of the target app. For example, the following code calls the `planet-weather-provider` service:

```csharp
using (var client = new HttpClient(discoveryHandler, false))
{
    var responses = await Task.WhenAll(
        client.GetAsync("http://planet-weather-provider/weatherforecast/mercury"),
        client.GetAsync("http://planet-weather-provider/weatherforecast/saturn"));
    var weathers = await Task.WhenAll(from res in responses select res.Content.ReadAsStringAsync());
    return new[]
    {
        new KeyValuePair<string, string>("Mercury", weathers[0]),
        new KeyValuePair<string, string>("Saturn", weathers[1]),
    };
}
```
::: zone-end

::: zone pivot="programming-language-java"
This topic shows how to prepare an existing Java Spring application for deployment to Azure Spring Cloud. If configured properly, Azure Spring Cloud provides robust services to monitor, scale, and update your Java Spring Cloud application.

Before running this example, you can try the [basic quickstart](spring-cloud-quickstart.md).

Other examples explain how to deploy an application to Azure Spring Cloud when the POM file is configured. 
* [Launch your first App](spring-cloud-quickstart.md)
* [Build and run Microservices](spring-cloud-quickstart-sample-app-introduction.md)

This article explains the required dependencies and how to add them to the POM file.

## Java Runtime version

Only Spring/Java applications can run in Azure Spring Cloud.

Azure Spring Cloud supports both Java 8 and Java 11. The hosting environment contains the latest version of Azul Zulu OpenJDK for Azure. For more information about Azul Zulu OpenJDK for Azure, see [Install the JDK](/azure/developer/java/fundamentals/java-jdk-install).

## Spring Boot and Spring Cloud versions

To prepare an existing Spring Boot application for deployment to Azure Spring Cloud include the Spring Boot and Spring Cloud dependencies in the application POM file as shown in the following sections.

Azure Spring Cloud supports only Spring Boot apps either Spring Boot version 2.1 or version 2.2. The following table lists the supported Spring Boot and Spring Cloud combinations:

Spring Boot version | Spring Cloud version
---|---
2.1 | Greenwich.RELEASE
2.2 | Hoxton.SR8
2.3 | Hoxton.SR8

### Dependencies for Spring Boot version 2.1

For Spring Boot version 2.1 add the following dependencies to the application POM file.

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.12.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Greenwich.RELEASE</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

### Dependencies for Spring Boot version 2.2

For Spring Boot version 2.2 add the following dependencies to the application POM file.

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.4.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Hoxton.SR8</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```
### Dependencies for Spring Boot version 2.3

For Spring Boot version 2.3 add the following dependencies to the application POM file.

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.3.0.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Hoxton.SR8</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```
## Azure Spring Cloud client dependency

Azure Spring Cloud hosts and manages Spring Cloud components. The components include Spring Cloud Service Registry and Spring Cloud Config Server. It is recommended to use Spring Boot 2.2 or 2.3. For Spring Boot 2.1, you will need to include the Azure Spring Cloud client library in your dependencies to allow communication with your Azure Spring Cloud service instance.

The following table lists the correct Azure Spring Cloud versions for your app that uses Spring Boot and Spring Cloud.

Spring Boot version | Spring Cloud version | Azure Spring Cloud client starter version
---|---|---
2.1.x | Greenwich.RELEASE | 2.1.2
2.2.x | Hoxton.SR8 | Not needed
2.3.x | Hoxton.SR8 | Not needed

Include the following dependenciy in your pom.xml file if you are using Spring Boot 2.1.

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.2</version>
</dependency>
```
> [!WARNING]
> Don't specify `server.port` in your configuration. Azure Spring Cloud will overide this setting to a fixed port number. Please also respect this setting and not specify server port in your code.

## Other recommended dependencies to enable Azure Spring Cloud features

To enable the built-in features of Azure Spring Cloud from service registry to distributed tracing, you need to also include the following dependencies in your application. You can drop some of these dependencies if you don't need corresponding features for the specific apps.

### Service Registry

To use the managed Azure Service Registry service, include the `spring-cloud-starter-netflix-eureka-client` dependency in the pom.xml file as shown here:

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

The endpoint of the Service Registry server is automatically injected as environment variables with your app. Applications can register themselves with the Service Registry server and discover other dependent microservices.


#### EnableDiscoveryClient annotation

Add the following annotation to the application source code.
```java
@EnableDiscoveryClient
```
For example, see the piggymetrics application from earlier examples:
```java
package com.piggymetrics.gateway;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.netflix.zuul.EnableZuulProxy;

@SpringBootApplication
@EnableDiscoveryClient
@EnableZuulProxy

public class GatewayApplication {
    public static void main(String[] args) {
        SpringApplication.run(GatewayApplication.class, args);
    }
}
```

### Distributed Configuration

To enable Distributed Configuration, include the following `spring-cloud-config-client` dependency in the dependencies section of your pom.xml file:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> Don't specify `spring.cloud.config.enabled=false` in your bootstrap configuration. Otherwise, your application stops working with Config Server.

### Metrics

Include the `spring-boot-starter-actuator` dependency in the dependencies section of your pom.xml file as shown here:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

 Metrics are periodically pulled from the JMX endpoints. You can visualize the metrics by using the Azure portal.

 > [!WARNING]
 > Please specify `spring.jmx.enabled=true` in your configuration property. Otherwise, metrics can't be visualized in Azure portal.

### Distributed Tracing

Include the following `spring-cloud-starter-sleuth` and `spring-cloud-starter-zipkin` dependencies in the dependencies section of your pom.xml file:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-sleuth</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-zipkin</artifactId>
</dependency>
```

 You also need to enable an Azure Application Insights instance to work with your Azure Spring Cloud service instance. For information about how to use Application Insights with Azure Spring Cloud, see the [documentation on distributed tracing](spring-cloud-tutorial-distributed-tracing.md).

## See also
* [Analyze application logs and metrics](./diagnostic-services.md)
* [Set up your Config Server](./spring-cloud-tutorial-config-server.md)
* [Use distributed tracing with Azure Spring Cloud](./spring-cloud-tutorial-distributed-tracing.md)
* [Spring Quickstart Guide](https://spring.io/quickstart)
* [Spring Boot documentation](https://spring.io/projects/spring-boot)

## Next steps

In this topic, you learned how to configure your Java Spring application for deployment to Azure Spring Cloud. To learn how to set up a Config Server instance, see [Set up a Config Server instance](spring-cloud-tutorial-config-server.md).

More samples are available on GitHub: [Azure Spring Cloud Samples](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples).
::: zone-end