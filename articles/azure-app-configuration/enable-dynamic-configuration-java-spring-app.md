---
title: Use dynamic configuration in a Spring Boot app
titleSuffix: Azure App Configuration
description: Learn how to dynamically update configuration data for Spring Boot apps
services: azure-app-configuration
author: mrm9084
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 12/09/2020
ms.custom: devx-track-java
ms.author: mametcal

#Customer intent: As a Java Spring developer, I want to dynamically update my app to use the latest configuration data in App Configuration.
---
# Tutorial: Use dynamic configuration in a Java Spring app

App Configuration has two libraries for Spring. `spring-cloud-azure-appconfiguration-config` requires Spring Boot and takes a dependency on `spring-cloud-context`. `spring-cloud-azure-appconfiguration-config-web` requires Spring Web along with Spring Boot. Both libraries support manual triggering to check for refreshed configuration values. `spring-cloud-azure-appconfiguration-config-web` also adds support for automatic checking of configuration refresh.

Refresh allows you to refresh your configuration values without having to restart your application, though it will cause all beans in the `@RefreshScope` to be recreated. The client library caches a hash id of the currently loaded configurations to avoid too many calls to the configuration store. The refresh operation doesn't update the value until the cached value has expired, even when the value has changed in the configuration store. The default expiration time for each request is 30 seconds. It can be overridden if necessary.

`spring-cloud-azure-appconfiguration-config-web`'s automated refresh is triggered based off activity, specifically Spring Web's `ServletRequestHandledEvent`. If a `ServletRequestHandledEvent` is not triggered, `spring-cloud-azure-appconfiguration-config-web`'s automated refresh will not trigger a refresh even if the cache expiration time has expired.

## Use manual refresh

App Configuration exposes `AppConfigurationRefresh` which can be used to check if the cache is expired and if it is expired trigger a refresh.

```java
import com.microsoft.azure.spring.cloud.config.AppConfigurationRefresh;

...

@Autowired
private AppConfigurationRefresh appConfigurationRefresh;

...

public void myConfigurationRefreshCheck() {
    Future<Boolean> triggeredRefresh = appConfigurationRefresh.refreshConfigurations();
}
```

`AppConfigurationRefresh`'s `refreshConfigurations()` returns a `Future` that is true if a refresh has been triggered, and false if not. False means either the cache expiration time hasn't expired, there was no change, or another thread is currently checking for a refresh.

## Use automated refresh

To use automated refresh, start with a Spring Boot app that uses App Configuration, such as the app you create by following the [Spring Boot quickstart for App Configuration](quickstart-java-spring-app.md).

Then, open the *pom.xml* file in a text editor, and add a `<dependency>` for `spring-cloud-azure-appconfiguration-config-web`.

**Spring Cloud 1.1.x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.1.5</version>
</dependency>
```

**Spring Cloud 1.2.x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.2.7</version>
</dependency>
```

## Run and test the app locally

1. Build your Spring Boot application with Maven and run it.

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

1. Open a browser window, and go to the URL: `http://localhost:8080`.  You will see the message associated with your key.

    You can also use *curl* to test your application, for example: 
    
    ```cmd
    curl -X GET http://localhost:8080/
    ```

1. To test dynamic configuration, open the Azure App Configuration portal associated with your application. Select **Configuration Explorer**, and update the value of your displayed key, for example:

    | Key | Value |
    |---|---|
    | application/config.message | Hello - Updated |

1. Refresh the browser page to see the new message displayed.

## Next steps

In this tutorial, you enabled your Spring Boot app to dynamically refresh configuration settings from App Configuration. To learn how to use an Azure managed identity to streamline the access to App Configuration, continue to the next tutorial.

> [!div class="nextstepaction"]
> [Managed identity integration](./howto-integrate-azure-managed-service-identity.md)
