---
title: Send or receive events from Azure Event Hubs using Java (latest)
description: This article provides a walkthrough of creating a Java application that sends/receives events to/from Azure Event Hubs using the latest azure-messaging-eventhubs package. 
services: event-hubs
author: spelluru

ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 02/11/2020
ms.author: spelluru

---

# Use Java to send events to or receive events from Azure Event Hubs (azure-messaging-eventhubs)
This quickstart shows how to send events to and receive events from an event hub using the **azure-messaging-eventhubs** Java package.

> [!IMPORTANT]
> This quickstart uses the new **azure-messaging-eventhubs** package. For a quickstart that uses the old **azure-eventhubs** and **azure-eventhubs-eph** packages, see [Send and receive events using azure-eventhubs and azure-eventhubs-eph](event-hubs-java-get-started-send.md). 


## Prerequisites
If you are new to Azure Event Hubs, see [Event Hubs overview](event-hubs-about.md) before you do this quickstart. 

To complete this quickstart, you need the following prerequisites:

- **Microsoft Azure subscription**. To use Azure services, including Azure Event Hubs, you need a subscription.  If you don't have an existing Azure account, you can sign up for a [free trial](https://azure.microsoft.com/free/) or use your MSDN subscriber benefits when you [create an account](https://azure.microsoft.com).
- A Java development environment. This quickstart uses [Eclipse](https://www.eclipse.org/). Java Development Kit (JDK) with version 8 or above is required. 
- **Create an Event Hubs namespace and an event hub**. The first step is to use the [Azure portal](https://portal.azure.com) to create a namespace of type Event Hubs, and obtain the management credentials your application needs to communicate with the event hub. To create a namespace and an event hub, follow the procedure in [this article](event-hubs-create.md). Then, get the **connection string for the Event Hubs namespace** by following instructions from the article: [Get connection string](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). You use the connection string later in this quickstart.

## Send events 
This section shows you how to create a Java application to send events an event hub. 

### Add reference to Azure Event Hubs library

The Java client library for Event Hubs is available for use in Maven projects from the [Maven Central Repository](https://search.maven.org/search?q=a:azure-messaging-eventhubs). You can reference this library using the following dependency declaration inside your Maven project file:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-messaging-eventhubs</artifactId>
    <version>5.0.1</version>
</dependency>
```

### Write code to send messages to the event hub

For the following sample, first create a new Maven project for a console/shell application in your favorite Java development environment. Add a class named `SimpleSend`, and add the following code to the class:

```java
import com.azure.messaging.eventhubs.*;
import static java.nio.charset.StandardCharsets.UTF_8;

public class Sender {
       public static void main(String[] args) {
    }
}
```

### Connection string and event hub
This code uses the connection string to the Event Hubs namespace and the name of the event hub to build an Event Hubs client. 

```java
String connectionString = "<CONNECTION STRING to EVENT HUBS NAMESPACE>";
String eventHubName = "<EVENT HUB NAME>";
```

### Create an Event Hubs Producer client 
This code creates a producer client object that's used to produce/send events to the event hub. 

```java
EventHubProducerClient producer = new EventHubClientBuilder()
    .connectionString(connectionString, eventHubName)
    .buildProducerClient();
```

### Prepare a batch of events
This code prepares a batch of events. 

```java
EventDataBatch batch = producer.createBatch();
batch.tryAdd(new EventData("First event"));
batch.tryAdd(new EventData("Second event"));
batch.tryAdd(new EventData("Third event"));
batch.tryAdd(new EventData("Fourth event"));
batch.tryAdd(new EventData("Fifth event"));
```

### Send the batch of events to the event hub
This code sends the batch of events you prepared in the previous step to the event hub. The following code blocks on the send operation. 

```java
producer.send(batch);
```

### Close and cleanup
This code closes the producer. 

```java
producer.close();
```
### Complete code to send events
Here is the complete code to send events to the event hub. 

```java
import com.azure.messaging.eventhubs.*;

public class Sender {
    public static void main(String[] args) {
        final String connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";
        final String eventHubName = "EVENT HUB NAME";

        // create a producer using the namespace connection string and event hub name
        EventHubProducerClient producer = new EventHubClientBuilder()
            .connectionString(connectionString, eventHubName)
            .buildProducerClient();

        // prepare a batch of events to send to the event hub    
        EventDataBatch batch = producer.createBatch();
        batch.tryAdd(new EventData("First event"));
        batch.tryAdd(new EventData("Second event"));
        batch.tryAdd(new EventData("Third event"));
        batch.tryAdd(new EventData("Fourth event"));
        batch.tryAdd(new EventData("Fifth event"));

        // send the batch of events to the event hub
        producer.send(batch);

        // close the producer
        producer.close();
    }
}
```

Build the program, and ensure that there are no errors. You will run this program after you run the receiver program. 

## Receive events
The code in this tutorial is based on the [EventProcessorClient sample on GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs/EventProcessorClientSample.java), which you can examine to see the full working application.

### Create a Java project

The Java client library for Event Hubs is available for use in Maven projects from the [Maven Central Repository](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22), and can be referenced using the following dependency declaration inside your Maven project file: 

```xml
<dependencies>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-eventhubs</artifactId>
        <version>5.0.1</version>
    </dependency>
</dependencies>
```

1. Use the following code to create a new class called `Receiver`. Replace the placeholders with the values used when you created the event hub and storage account:
   
   ```java
     import com.azure.messaging.eventhubs.*;
     import com.azure.messaging.eventhubs.models.ErrorContext;
     import com.azure.messaging.eventhubs.models.EventContext;
     import java.util.concurrent.TimeUnit;
     import java.util.function.Consumer;
    
     public class Receiver {
    
         final static String connectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
         final static String eventHubName = "<EVENT HUB NAME>";
         
         public static void main(String[] args) throws Exception {
    
             // function to process events
             Consumer<EventContext> processEvent = eventContext  -> {
                 System.out.print("Received event: ");
                 // print the body of the event
                 System.out.println(eventContext.getEventData().getBodyAsString());
                 eventContext.updateCheckpoint();
             };
    
             // function to process errors
             Consumer<ErrorContext> processError = errorContext -> {
                 // print the error message
                 System.out.println(errorContext.getThrowable().getMessage());
             };
    
            
             EventProcessorClient eventProcessorClient = new EventProcessorClientBuilder()
                     .connectionString(connectionString, eventHubName)
                     .processEvent(processEvent)
                     .processError(processError)
                     .consumerGroup(EventHubClientBuilder.DEFAULT_CONSUMER_GROUP_NAME)
                     .checkpointStore(new InMemoryCheckpointStore())
                     .buildEventProcessorClient();
    
             System.out.println("Starting event processor");
             eventProcessorClient.start();
    
             System.out.println("Press enter to stop.");
             System.in.read();
    
             System.out.println("Stopping event processor");
             eventProcessorClient.stop();
             System.out.println("Event processor stopped.");
    
             System.out.println("Exiting process");
         }
     }
    ```
    
2. Download the **InMemoryCheckpointStore.java** file from [GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs/InMemoryCheckpointStore.java), and add it to your project. 
3. Build the program, and ensure that there are no errors. 

## Run the applications
1. Run the **receiver** application first.
1. Then, run the **sender** application. 
1. In the **receiver** application window, confirm that you see the events that were published by the sender application.
1. Press **ENTER** in the receiver application window to stop the application. 

## Next steps
Check out [Java SDK samples on GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)

