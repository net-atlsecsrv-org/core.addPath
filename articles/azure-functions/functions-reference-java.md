---
title: Java developer reference for Azure Functions 
description: Understand how to develop functions with Java.
ms.topic: conceptual
ms.date: 09/14/2018
ms.custom: devx-track-java
---

# Azure Functions Java developer guide

This guide contains detailed information to help you succeed developing Azure Functions using Java.

As a Java developer, if you're new to Azure Functions, please consider first reading one of the following articles:

| Getting started | Concepts| 
| -- | -- |  
| <ul><li>[Java function using Visual Studio Code](./functions-create-first-function-vs-code.md?pivots=programming-language-java)</li><li>[Java/Maven function with terminal/command prompt](./functions-create-first-azure-function-azure-cli.md?pivots=programming-language-java)</li><li>[Java function using Gradle](functions-create-first-java-gradle.md)</li><li>[Java function using Eclipse](functions-create-maven-eclipse.md)</li><li>[Java function using IntelliJ IDEA](functions-create-maven-intellij.md)</li></ul> | <ul><li>[Developer guide](functions-reference.md)</li><li>[Hosting options](functions-scale.md)</li><li>[Performance&nbsp; considerations](functions-best-practices.md)</li></ul> |

## Java function basics

A Java function is a `public` method, decorated with the annotation `@FunctionName`. This method defines the entry for a Java function, and must be unique in a particular package. The package can have multiple classes with multiple public methods annotated with `@FunctionName`. A single package is deployed to a function app in Azure. When running in Azure, the function app provides the deployment, execution, and management context for your individual Java functions.

## Programming model 

The concepts of [triggers and bindings](functions-triggers-bindings.md) are fundamental to Azure Functions. Triggers start the execution of your code. Bindings give you a way to pass data to and return data from a function, without having to write custom data access code.

## Create Java functions

To make it easier to create Java functions, there are Maven-based tooling and archetypes that use predefined Java templates to help you create projects with a specific function trigger.    

### Maven-based tooling

The following developer environments have Azure Functions tooling that lets you create Java function projects: 

+ [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions)
+ [Eclipse](functions-create-maven-eclipse.md)
+ [IntelliJ](functions-create-maven-intellij.md)

The article links above show you how to create your first functions using your IDE of choice. 

### Project Scaffolding

If you prefer command line development from the Terminal, the simplest way to scaffold Java-based function projects is to use `Apache Maven` archetypes. The Java Maven archetype for Azure Functions is published under the following _groupId_:_artifactId_: [com.microsoft.azure:azure-functions-archetype](https://search.maven.org/artifact/com.microsoft.azure/azure-functions-archetype/). 

The following command generates a new Java function project using this archetype:

```
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype 
```

To get started using this archetype, see the [Java quickstart](./functions-create-first-azure-function-azure-cli.md?pivots=programming-language-java). 

## Folder structure

Here is the folder structure of an Azure Functions Java project:

```
FunctionsProject
 | - src
 | | - main
 | | | - java
 | | | | - FunctionApp
 | | | | | - MyFirstFunction.java
 | | | | | - MySecondFunction.java
 | - target
 | | - azure-functions
 | | | - FunctionApp
 | | | | - FunctionApp.jar
 | | | | - host.json
 | | | | - MyFirstFunction
 | | | | | - function.json
 | | | | - MySecondFunction
 | | | | | - function.json
 | | | | - bin
 | | | | - lib
 | - pom.xml
```

You can use a shared [host.json](functions-host-json.md) file to configure the function app. Each function has its own code file (.java) and binding configuration file (function.json).

You can put more than one function in a project. Avoid putting your functions into separate jars. The `FunctionApp` in the target directory is what gets deployed to your function app in Azure.

## Triggers and annotations

 Functions are invoked by a trigger, such as an HTTP request, a timer, or an update to data. Your function needs to process that trigger, and any other inputs, to produce one or more outputs.

Use the Java annotations included in the [com.microsoft.azure.functions.annotation.*](/java/api/com.microsoft.azure.functions.annotation) package to bind input and outputs to your methods. For more information, see the [Java reference docs](/java/api/com.microsoft.azure.functions.annotation).

> [!IMPORTANT] 
> You must configure an Azure Storage account in your [local.settings.json](./functions-run-local.md#local-settings-file) to run Azure Blob storage, Azure Queue storage, or Azure Table storage triggers locally.

Example:

```java
public class Function {
    public String echo(@HttpTrigger(name = "req", 
      methods = {HttpMethod.POST},  authLevel = AuthorizationLevel.ANONYMOUS) 
        String req, ExecutionContext context) {
        return String.format(req);
    }
}
```

Here is the generated corresponding `function.json` by the [azure-functions-maven-plugin](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-maven-plugin):

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.Function.echo",
  "bindings": [
    {
      "type": "httpTrigger",
      "name": "req",
      "direction": "in",
      "authLevel": "anonymous",
      "methods": [ "post" ]
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ]
}

```

## Java versions

_Support for Java 11 is currently in preview_

The version of Java used when creating the function app on which functions runs in Azure is specified in the pom.xml file. The Maven archetype currently generates a pom.xml for Java 8, which you can change before publishing. The Java version in pom.xml should match the version on which you have locally developed and tested your app. 

### Supported versions

The following table shows current supported Java versions for each major version of the Functions runtime, by operating system:

| Functions version | Java versions (Windows) | Java versions (Linux) |
| ----- | ----- | --- |
| 3.x | 11 (preview)<br/>8<sup>\*</sup> | 11 (preview)<br/>8 |
| 2.x | 8 | n/a |

<sup>\*</sup> This is the current default of the pom.xml generated by the Maven archetype.

### Specify the deployment version

Currently, the Maven archetype generates a pom.xml that targets Java 8. The following elements in pom.xml need to be updated to create a function app that runs Java 11.

| Element |  Java 8 value | Java 11 value | Description |
| ---- | ---- | ---- | --- |
| **`Java.version`** | 1.8 | 11 | Version of Java used by the maven-compiler-plugin. |
| **`JavaVersion`** | 8 | 11 | Java version hosted by the function app in Azure. |

The following examples show the settings for Java 8 in the relevant sections of the pom.xml file:

#### `Java.version`
:::code language="xml" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/pom.xml" range="12-19" highlight="14":::

#### `JavaVersion`
:::code language="xml" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/pom.xml" range="77-85" highlight="80":::

> [!IMPORTANT]
> You must have the JAVA_HOME environment variable set correctly to the JDK directory that is used during code compiling using Maven. Make sure that the version of the JDK is at least as high as the `Java.version` setting. 

### Specify the deployment OS

Maven also lets you specify the operating system on which your function app runs in Azure. Use the `os` element to choose the operating system. 

| Element |  Windows | Linux | Docker |
| ---- | ---- | ---- | --- |
| **`os`** | windows | linux | docker |

The following example shows the operating system setting in the `runtime` section of the pom.xml file:

:::code language="xml" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/pom.xml" range="77-85" highlight="79":::
 
## JDK runtime availability and support 

For local development of Java function apps, download and use the appropriate [Azul Zulu Enterprise for Azure](https://assets.azul.com/files/Zulu-for-Azure-FAQ.pdf) Java JDKs from [Azul Systems](https://www.azul.com/downloads/azure-only/zulu/). Azure Functions uses an Azul Java JDK runtime when you deploy your function app to the cloud.

[Azure support](https://azure.microsoft.com/support/) for issues with the JDKs and function apps is available with a [qualified support plan](https://azure.microsoft.com/support/plans/).

## Customize JVM

Functions lets you customize the Java virtual machine (JVM) used to run your Java functions. The [following JVM options](https://github.com/Azure/azure-functions-java-worker/blob/master/worker.config.json#L7) are used by default:

* `-XX:+TieredCompilation`
* `-XX:TieredStopAtLevel=1`
* `-noverify` 
* `-Djava.net.preferIPv4Stack=true`
* `-jar`

You can provide additional arguments in an app setting named `JAVA_OPTS`. You can add app settings to your function app deployed to Azure in the Azure portal or the Azure CLI.

> [!IMPORTANT]  
> In the Consumption plan, you must also add the WEBSITE_USE_PLACEHOLDER setting with a value of 0 for the customization to work. This setting does increase the cold start times for Java functions.

### Azure portal

In the [Azure portal](https://portal.azure.com), use the [Application Settings tab](functions-how-to-use-azure-function-app-settings.md#settings) to add the `JAVA_OPTS` setting.

### Azure CLI

You can use the [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings) command to set `JAVA_OPTS`, as in the following example:

#### [Consumption plan](#tab/consumption)
```azurecli-interactive
az functionapp config appsettings set \
--settings "JAVA_OPTS=-Djava.awt.headless=true" \
"WEBSITE_USE_PLACEHOLDER=0" \
--name <APP_NAME> --resource-group <RESOURCE_GROUP>
```
#### [Dedicated plan / Premium plan](#tab/dedicated+premium)
```azurecli-interactive
az functionapp config appsettings set \
--settings "JAVA_OPTS=-Djava.awt.headless=true" \
--name <APP_NAME> --resource-group <RESOURCE_GROUP>
```
---

This example enables headless mode. Replace `<APP_NAME>` with the name of your function app, and `<RESOURCE_GROUP>` with the resource group. 

## Third-party libraries 

Azure Functions supports the use of third-party libraries. By default, all dependencies specified in your project `pom.xml` file are automatically bundled during the [`mvn package`](https://github.com/Microsoft/azure-maven-plugins/blob/master/azure-functions-maven-plugin/README.md#azure-functionspackage) goal. For libraries not specified as dependencies in the `pom.xml` file, place them in a `lib` directory in the function's root directory. Dependencies placed in the `lib` directory are added to the system class loader at runtime.

The `com.microsoft.azure.functions:azure-functions-java-library` dependency is provided on the classpath by default, and doesn't need to be included in the `lib` directory. Also, [azure-functions-java-worker](https://github.com/Azure/azure-functions-java-worker) adds dependencies listed [here](https://github.com/Azure/azure-functions-java-worker/wiki/Azure-Java-Functions-Worker-Dependencies) to the classpath.

## Data type support

You can use Plain old Java objects (POJOs), types defined in `azure-functions-java-library`, or primitive data types such as String and Integer to bind to input or output bindings.

### POJOs

For converting input data to POJO, [azure-functions-java-worker](https://github.com/Azure/azure-functions-java-worker) uses the [gson](https://github.com/google/gson) library. POJO types used as inputs to functions should be `public`.

### Binary data

Bind binary inputs or outputs to `byte[]`, by setting the `dataType` field in your function.json to `binary`:

```java
   @FunctionName("BlobTrigger")
    @StorageAccount("AzureWebJobsStorage")
     public void blobTrigger(
        @BlobTrigger(name = "content", path = "myblob/{fileName}", dataType = "binary") byte[] content,
        @BindingName("fileName") String fileName,
        final ExecutionContext context
    ) {
        context.getLogger().info("Java Blob trigger function processed a blob.\n Name: " + fileName + "\n Size: " + content.length + " Bytes");
    }
```

If you expect null values, use `Optional<T>`.

## Bindings

Input and output bindings provide a declarative way to connect to data from within your code. A function can have multiple input and output bindings.

### Input binding example

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class Function {
    @FunctionName("echo")
    public static String echo(
        @HttpTrigger(name = "req", methods = { HttpMethod.PUT }, authLevel = AuthorizationLevel.ANONYMOUS, route = "items/{id}") String inputReq,
        @TableInput(name = "item", tableName = "items", partitionKey = "Example", rowKey = "{id}", connection = "AzureWebJobsStorage") TestInputData inputData
        @TableOutput(name = "myOutputTable", tableName = "Person", connection = "AzureWebJobsStorage") OutputBinding<Person> testOutputData,
    ) {
        testOutputData.setValue(new Person(httpbody + "Partition", httpbody + "Row", httpbody + "Name"));
        return "Hello, " + inputReq + " and " + inputData.getKey() + ".";
    }

    public static class TestInputData {
        public String getKey() { return this.RowKey; }
        private String RowKey;
    }
    public static class Person {
        public String PartitionKey;
        public String RowKey;
        public String Name;

        public Person(String p, String r, String n) {
            this.PartitionKey = p;
            this.RowKey = r;
            this.Name = n;
        }
    }
}
```

You invoke this function with an HTTP request. 
- HTTP request payload is passed as a `String` for the argument `inputReq`.
- One entry is retrieved from Table storage, and is passed as `TestInputData` to the argument `inputData`.

To receive a batch of inputs, you can bind to `String[]`, `POJO[]`, `List<String>`, or `List<POJO>`.

```java
@FunctionName("ProcessIotMessages")
    public void processIotMessages(
        @EventHubTrigger(name = "message", eventHubName = "%AzureWebJobsEventHubPath%", connection = "AzureWebJobsEventHubSender", cardinality = Cardinality.MANY) List<TestEventData> messages,
        final ExecutionContext context)
    {
        context.getLogger().info("Java Event Hub trigger received messages. Batch size: " + messages.size());
    }
    
    public class TestEventData {
    public String id;
}

```

This function gets triggered whenever there is new data in the configured event hub. Because the `cardinality` is set to `MANY`, the function receives a batch of messages from the event hub. `EventData` from event hub gets converted to `TestEventData` for the function execution.

### Output binding example

You can bind an output binding to the return value by using `$return`. 

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class Function {
    @FunctionName("copy")
    @StorageAccount("AzureWebJobsStorage")
    @BlobOutput(name = "$return", path = "samples-output-java/{name}")
    public static String copy(@BlobTrigger(name = "blob", path = "samples-input-java/{name}") String content) {
        return content;
    }
}
```

If there are multiple output bindings, use the return value for only one of them.

To send multiple output values, use `OutputBinding<T>` defined in the `azure-functions-java-library` package. 

```java
@FunctionName("QueueOutputPOJOList")
    public HttpResponseMessage QueueOutputPOJOList(@HttpTrigger(name = "req", methods = { HttpMethod.GET,
            HttpMethod.POST }, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            @QueueOutput(name = "itemsOut", queueName = "test-output-java-pojo", connection = "AzureWebJobsStorage") OutputBinding<List<TestData>> itemsOut, 
            final ExecutionContext context) {
        context.getLogger().info("Java HTTP trigger processed a request.");
       
        String query = request.getQueryParameters().get("queueMessageId");
        String queueMessageId = request.getBody().orElse(query);
        itemsOut.setValue(new ArrayList<TestData>());
        if (queueMessageId != null) {
            TestData testData1 = new TestData();
            testData1.id = "msg1"+queueMessageId;
            TestData testData2 = new TestData();
            testData2.id = "msg2"+queueMessageId;

            itemsOut.getValue().add(testData1);
            itemsOut.getValue().add(testData2);

            return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + queueMessageId).build();
        } else {
            return request.createResponseBuilder(HttpStatus.INTERNAL_SERVER_ERROR)
                    .body("Did not find expected items in CosmosDB input list").build();
        }
    }

     public static class TestData {
        public String id;
    }
```

You invoke this function on an HttpRequest. It writes multiple values to Queue storage.

## HttpRequestMessage and HttpResponseMessage

 These are defined in `azure-functions-java-library`. They are helper types to work with HttpTrigger functions.

| Specialized type      |       Target        | Typical usage                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    HTTP Trigger     | Gets method, headers, or queries |
| `HttpResponseMessage` | HTTP Output Binding | Returns status other than 200   |

## Metadata

Few triggers send [trigger metadata](./functions-triggers-bindings.md) along with input data. You can use annotation `@BindingName` to bind to trigger metadata.


```Java
package com.example;

import java.util.Optional;
import com.microsoft.azure.functions.annotation.*;


public class Function {
    @FunctionName("metadata")
    public static String metadata(
        @HttpTrigger(name = "req", methods = { HttpMethod.GET, HttpMethod.POST }, authLevel = AuthorizationLevel.ANONYMOUS) Optional<String> body,
        @BindingName("name") String queryValue
    ) {
        return body.orElse(queryValue);
    }
}
```
In the preceding example, the `queryValue` is bound to the query string parameter `name` in the HTTP request URL, `http://{example.host}/api/metadata?name=test`. Here's another example, showing how to bind to `Id` from queue trigger metadata.

```java
 @FunctionName("QueueTriggerMetadata")
    public void QueueTriggerMetadata(
        @QueueTrigger(name = "message", queueName = "test-input-java-metadata", connection = "AzureWebJobsStorage") String message,@BindingName("Id") String metadataId,
        @QueueOutput(name = "output", queueName = "test-output-java-metadata", connection = "AzureWebJobsStorage") OutputBinding<TestData> output,
        final ExecutionContext context
    ) {
        context.getLogger().info("Java Queue trigger function processed a message: " + message + " with metadaId:" + metadataId );
        TestData testData = new TestData();
        testData.id = metadataId;
        output.setValue(testData);
    }
```

> [!NOTE]
> The name provided in the annotation needs to match the metadata property.

## Execution context

`ExecutionContext`, defined in the `azure-functions-java-library`, contains helper methods to communicate with the functions runtime. For more information, see the [ExecutionContext reference article](/java/api/com.microsoft.azure.functions.executioncontext).

### Logger

Use `getLogger`, defined in `ExecutionContext`, to write logs from function code.

Example:

```java

import com.microsoft.azure.functions.*;
import com.microsoft.azure.functions.annotation.*;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        if (req.isEmpty()) {
            context.getLogger().warning("Empty request body received by function " + context.getFunctionName() + " with invocation " + context.getInvocationId());
        }
        return String.format(req);
    }
}
```

## View logs and trace

You can use the Azure CLI to stream Java stdout and stderr logging, as well as other application logging. 

Here's how to configure your function app to write application logging by using the Azure CLI:

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

To stream logging output for your function app by using the Azure CLI, open a new command prompt, Bash, or Terminal session, and enter the following command:

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```
The [az webapp log tail](/cli/azure/webapp/log) command has options to filter output by using the `--provider` option. 

To download the log files as a single ZIP file by using the Azure CLI, open a new command prompt, Bash, or Terminal session, and enter the following command:

```azurecli-interactive
az webapp log download --resource-group resourcegroupname --name functionappname
```

You must have enabled file system logging in the Azure portal or the Azure CLI before running this command.

## Environment variables

In Functions, [app settings](functions-app-settings.md), such as service connection strings, are exposed as environment variables during execution. You can access these settings by using, `System.getenv("AzureWebJobsStorage")`.

The following example gets the [application setting](functions-how-to-use-azure-function-app-settings.md#settings), with the key named `myAppSetting`:

```java

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        context.getLogger().info("My app setting value: "+ System.getenv("myAppSetting"));
        return String.format(req);
    }
}

```

> [!NOTE]
> The value of AppSetting FUNCTIONS_EXTENSION_VERSION should be ~2 or ~3 for an optimized cold start experience.

## Next steps

For more information about Azure Functions Java development, see the following resources:

* [Best practices for Azure Functions](functions-best-practices.md)
* [Azure Functions developer reference](functions-reference.md)
* [Azure Functions triggers and bindings](functions-triggers-bindings.md)
* Local development and debug with [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md), and [Eclipse](functions-create-maven-eclipse.md)
* [Remote Debug Java functions using Visual Studio Code](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [Maven plugin for Azure Functions](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) 
* Streamline function creation through the `azure-functions:add` goal, and prepare a staging directory for [ZIP file deployment](deployment-zip-push.md).