---
title: include file
description: include file
services: azure-communication-services
author: pvicencio
manager: ankita

ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/12/2021
ms.topic: include
ms.custom: include file
ms.author: pvicencio
---

Get started with Azure Communication Services by using the Communication Services Java SMS SDK to send SMS messages.

Completing this quickstart incurs a small cost of a few USD cents or less in your Azure account.

## Prerequisites

- An Azure account with an active subscription. [Create an account for free](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Java Development Kit (JDK)](/java/azure/jdk/) version 8 or above.
- [Apache Maven](https://maven.apache.org/download.cgi).
- An active Communication Services resource and connection string. [Create a Communication Services resource](../../create-communication-resource.md).
- An SMS enabled telephone number. [Get a phone number](../get-phone-number.md).

### Prerequisite check

- In a terminal or command window, run `mvn -v` to check that maven is installed.
- To view the phone numbers associated with your Communication Services resource, sign in to the [Azure portal](https://portal.azure.com/), locate your Communication Services resource and open the **phone numbers** tab from the left navigation pane.

## Setting up

### Create a new Java application

Open your terminal or command window and navigate to the directory where you would like to create your Java application. Run the command below to generate the Java project from the maven-archetype-quickstart template.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

The 'generate' goal will create a directory with the same name as the artifactId. Under this directory, the **src/main/java** directory contains the project source code, the **src/test/java directory** contains the test source, and the **pom.xml** file is the project's Project Object Model, or POM.

### Install the package

Open the **pom.xml** file in your text editor. Add the following dependency element to the group of dependencies.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-sms</artifactId>
    <version>1.0.0</version>
</dependency>
```

### Set up the app framework

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-core</artifactId>
    <version>1.13.0</version> <!-- {x-version-update;com.azure:azure-core;dependency} -->
</dependency>
```

Open **/src/main/java/com/communication/quickstart/App.java** in a text editor, add import directives and remove the `System.out.println("Hello world!");` statement:

```java
package com.communication.quickstart;

import com.azure.communication.sms.models.*;
import com.azure.core.credential.AzureKeyCredential;
import com.azure.communication.sms.*;
import com.azure.core.util.Context;
import java.util.Arrays;

public class App
{
    public static void main( String[] args )
    {
        // Quickstart code goes here
    }
}

```

## Object model

The following classes and interfaces handle some of the major features of the Azure Communication Services SMS SDK for Java.

| Name                                                             | Description                                                                                     |
| ---------------------------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| SmsClientBuilder              | This class creates the SmsClient. You provide it with endpoint, credential, and an http client. |
| SmsClient                    | This class is needed for all SMS functionality. You use it to send SMS messages.                |
| SmsSendOptions               | This class provides options to add custom tags and configure delivery reporting. If deliveryReportEnabled is set to true, then an event will be emitted when delivery was successful |        
| SmsSendResult                | This class contains the result from the SMS service.                                          |

## Authenticate the client

Instantiate an `SmsClient` with your connection string. (Credential is the `Key` from the Azure portal. Learn how to [manage your resource's connection string](../../create-communication-resource.md#store-your-connection-string). In addition, you can initialize the client with any custom HTTP client the implements the `com.azure.core.http.HttpClient` interface.

Add the following code to the `main` method:

```java
// You can find your endpoint and access key from your resource in the Azure portal
String endpoint = "https://<resource-name>.communication.azure.com/";
AzureKeyCredential azureKeyCredential = new AzureKeyCredential("<access-key-credential>");

SmsClient smsClient = new SmsClientBuilder()
                .endpoint(endpoint)
                .credential(azureKeyCredential)
                .buildClient();
```

You can also provide the entire connection string using the connectionString() function instead of providing the endpoint and access key.
```java
// You can find your connection string from your resource in the Azure portal
String connectionString = "https://<resource-name>.communication.azure.com/;<access-key>";

SmsClient smsClient = new SmsClientBuilder()
            .connectionString(connectionString)
            .buildClient();
```

## Send a 1:1 SMS message

To send an SMS message to a single recipient, call the `send` method from the SmsClient with a single recipient phone number. You may also pass in optional parameters to specify whether the delivery report should be enabled and to set custom tags.

```java
SmsSendResult sendResult = smsClient.send(
                "<from-phone-number>",
                "<to-phone-number>",
                "Weekly Promotion");

System.out.println("Message Id: " + sendResult.getMessageId());
System.out.println("Recipient Number: " + sendResult.getTo());
System.out.println("Send Result Successful:" + sendResult.isSuccessful());
```

You should replace `<from-phone-number>` with an SMS enabled phone number associated with your Communication Services resource and `<to-phone-number>` with a phone number you wish to send a message to.

> [!WARNING]
> Note that phone numbers should be provided in E.164 international standard format. (e.g.: +14255550123).

## Send a 1:N SMS message with options
To send an SMS message to a list of recipients, call the `send` method with a list of recipient phone numbers. You may also pass in optional parameters to specify whether the delivery report should be enabled and to set custom tags.
```java
SmsSendOptions options = new SmsSendOptions();
options.setDeliveryReportEnabled(true);
options.setTag("Marketing");

Iterable<SmsSendResult> sendResults = smsClient.sendWithResponse(
    "<from-phone-number>",
    Arrays.asList("<to-phone-number1>", "<to-phone-number2>"),
    "Weekly Promotion",
    options /* Optional */,
    Context.NONE).getValue();

for (SmsSendResult result : sendResults) {
    System.out.println("Message Id: " + result.getMessageId());
    System.out.println("Recipient Number: " + result.getTo());
    System.out.println("Send Result Successful:" + result.isSuccessful());
}
```

You should replace `<from-phone-number>` with an SMS enabled phone number associated with your Communication Services resource and `<to-phone-number-1>` and `<to-phone-number-2>` with phone number(s) you wish to send a message to.

> [!WARNING]
> Note that phone numbers should be provided in E.164 international standard format. (e.g.: +14255550123).

The `setDeliveryReportEnabled` method is used to configure Delivery Reporting. This is useful for scenarios where you want to emit events when SMS messages are delivered. See the [Handle SMS Events](../handle-sms-events.md) quickstart to configure Delivery Reporting for your SMS messages.

The `setTag` method is used to apply a tag to the Delivery Report.

## Run the code

Navigate to the directory containing the **pom.xml** file and compile the project using the `mvn` command.

```console

mvn compile

```

Then, build the package.

```console

mvn package

```

Run the following `mvn` command to execute the app.

```console

mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false

```
