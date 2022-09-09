---
title: Quickstart to learn how to use Azure App Configuration
description: A quickstart for using Azure App Configuration with Java Spring apps.
author: yidon
ms.author: yidon
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 12/17/2019

#Customer intent: As a Java Spring developer, I want to manage all my app settings in one place.
---
# Quickstart: Create a Java Spring app with Azure App Configuration

In this quickstart, you incorporate Azure App Configuration into a Java Spring app to centralize storage and management of application settings separate from your code.

## Prerequisites

- Azure subscription - [create one for free](https://azure.microsoft.com/free/)
- A supported [Java Development Kit (JDK)](https://docs.microsoft.com/java/azure/jdk) with version 8.
- [Apache Maven](https://maven.apache.org/download.cgi) version 3.0 or above.

## Create an App Configuration store

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Select **Configuration Explorer** > **+ Create** to add the following key-value pairs:

    | Key | Value |
    |---|---|
    | /application/config.message | Hello |

    Leave **Label** and **Content Type** empty for now.

## Create a Spring Boot app

Use the [Spring Initializr](https://start.spring.io/) to create a new Spring Boot project.

1. Browse to <https://start.spring.io/>.

2. Specify the following options:

   * Generate a **Maven** project with **Java**.
   * Specify a **Spring Boot** version that's equal to or greater than 2.0.
   * Specify the **Group** and **Artifact** names for your application.
   * Add the **Web** dependency.

3. After you specify the previous options, select **Generate Project**. When prompted, download the project to a path on your local computer.

## Connect to an App Configuration store

1. After you extract the files on your local system, your simple Spring Boot application is ready for editing. Locate the *pom.xml* file in the root directory of your app.

2. Open the *pom.xml* file in a text editor, and add the Spring Cloud Azure Config starter to the list of `<dependencies>`:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-appconfiguration-config</artifactId>
        <version>1.1.0.M5</version>
    </dependency>
    ```

3. Create a new Java file named *MessageProperties.java* in the package directory of your app. Add the following lines:

    ```java
    @ConfigurationProperties(prefix = "config")
    public class MessageProperties {
        private String message;

        public String getMessage() {
            return message;
        }

        public void setMessage(String message) {
            this.message = message;
        }
    }
    ```

4. Create a new Java file named *HelloController.java* in the package directory of your app. Add the following lines:

    ```java
    @RestController
    public class HelloController {
        private final MessageProperties properties;

        public HelloController(MessageProperties properties) {
            this.properties = properties;
        }

        @GetMapping
        public String getMessage() {
            return "Message: " + properties.getMessage();
        }
    }
    ```

5. Open the main application Java file, and add `@EnableConfigurationProperties` to enable this feature.

    ```java
    @SpringBootApplication
    @EnableConfigurationProperties(MessageProperties.class)
    public class AzureConfigApplication {
        public static void main(String[] args) {
            SpringApplication.run(AzureConfigApplication.class, args);
        }
    }
    ```

6. Create a new file named `bootstrap.properties` under the resources directory of your app, and add the following lines to the file. Replace the sample values with the appropriate properties for your App Configuration store.

    ```CLI
    spring.cloud.azure.appconfiguration.stores[0].connection-string=[your-connection-string]
    ```

## Build and run the app locally

1. Build your Spring Boot application with Maven and run it, for example:

    ```CLI
    mvn clean package
    mvn spring-boot:run
    ```
2. After your application is running, use *curl* to test your application, for example:

      ```CLI
      curl -X GET http://localhost:8080/
      ```
    You see the message that you entered in the App Configuration store.

## Clean up resources

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## Next steps

In this quickstart, you created a new App Configuration store and used it with a Java Spring app. For more information, see [Spring on Azure](https://docs.microsoft.com/java/azure/spring-framework/). To learn how to use an Azure managed identity to streamline access to App Configuration, continue to the next tutorial.

> [!div class="nextstepaction"]
> [Managed identity integration](./howto-integrate-azure-managed-service-identity.md)
