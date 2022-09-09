---
title: Chat concepts in Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Learn about Communication Services Chat concepts.
author: mikben
manager: jken
services: azure-communication-services

ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
---
# Chat concepts

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Azure Communication Services Chat client libraries can be used to add real-time text chat to your applications. This page summarizes key Chat concepts and capabilities.

See the [Communication Services Chat client library Overview](./sdk-features.md) to learn more about specific client library languages and capabilities.

## Chat overview 

Chat conversations happen within chat threads. A chat thread can contain many messages and many users. Every message belongs to a single thread, and a user can be a part of one or many threads. 

Each user in the chat thread is called a member. You can have up to 250 members in a chat thread. Only thread members can send and receive messages or add/remove members in a chat thread. The maximum message size allowed is approximately 28KB. You can retrieve all messages in a chat thread using the `List/Get Messages` operation. Communication Services stores chat history until you execute a delete operation on the chat thread or message, or until no members are remaining in the chat thread at which point it is orphaned and processed for deletion.   

For chat threads with more than 20 members, read receipts and typing indicator features are disabled. 

## Chat architecture

There are two core parts to chat architecture: 1) Trusted Service and 2) Client Application.

:::image type="content" source="../../media/chat-architecture.png" alt-text="Diagram showing Communication Services' chat architecture.":::

 - **Trusted service:** To properly manage a chat session, you need a service that helps you connect to Communication Services using your resource connection string. This service is responsible for creating chat threads, managing thread memberships, and providing access tokens to users. More information about access tokens can be found in our [access tokens](../../quickstarts/access-tokens.md) quickstart.

 - **Client app:**  The client application connects to your trusted service and receives the access tokens that are used to connect directly to Communication Services. After this connection is made, your client app can send and receive messages.
    
## Message types

Communication Services Chat shares user-generated messages as well as system-generated messages called **Thread activities**. Thread activities are generated when a chat thread is updated. When you call `List Messages` or `Get Messages` on a chat thread, the result will contain the user-generated text messages as well as the system messages in chronological order. This helps you identify when a member was added or removed or when the chat thread topic was updated. Supported message types are:  

 - `Text`: Actual message composed and sent by user as part of chat conversation. 
 - `ThreadActivity/AddMember`: System message that indicates one or more members have been added to the chat thread. For example:

```xml

<addmember>
    <eventtime>1598478187549</eventtime>
    <initiator>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_0e59221d-0c1d-46ae-9544-c963ce56c10b</initiator>
    <detailedinitiatorinfo>
        <friendlyName>User 1</friendlyName>
    </detailedinitiatorinfo>
    <rosterVersion>1598478184564</rosterVersion>
    <target>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_0e59221d-0c1d-46ae-9544-c963ce56c10b</target>
    <detailedtargetinfo>
        <id>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_0e59221d-0c1d-46ae-9544-c963ce56c10b</id>
        <friendlyName>User 1</friendlyName>
    </detailedtargetinfo>
    <target>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_8540c0de-899f-5cce-acb5-3ec493af3800</target>
    <detailedtargetinfo>
        <id>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_8540c0de-899f-5cce-acb5-3ec493af3800</id>
        <friendlyName>User 2</friendlyName>
    </detailedtargetinfo>
</addmember>

```  

- `ThreadActivity/DeleteMember`: System message that indicates a member has been removed from the chat thread. For example:

```xml

<deletemember>
    <eventtime>1598478187642</eventtime>
    <initiator>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_0e59221d-0c1d-46ae-9544-c963ce56c10b</initiator>
    <detailedinitiatorinfo>
        <friendlyName>User 1</friendlyName>
    </detailedinitiatorinfo>
    <rosterVersion>1598478184564</rosterVersion>
    <target>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_8540c0de-899f-5cce-acb5-3ec493af3800</target>
    <detailedtargetinfo>
        <id>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_8540c0de-899f-5cce-acb5-3ec493af3800</id>
        <friendlyName>User 2</friendlyName>
    </detailedtargetinfo>
</deletemember>

```

- `ThreadActivity/TopicUpdate`: System message that indicates the topic has been updated. For example:

```xml

<topicupdate>
    <eventtime>1598477591811</eventtime>
    <initiator>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_0e59221d-0c1d-46ae-9544-c963ce56c10b</initiator>
    <value>New topic</value>
</topicupdate>

```

## Real-time signaling 

The Chat JavaScript client library includes real-time signaling. This allows clients to listen for real-time updates and incoming messages to a chat thread without having to poll the APIs. Available events include:

 - `ChatMessageReceived` - when a new message is sent to a chat thread that the user is member of. This event is not sent for auto generated system messages which we discussed in the previous topic.  
 - `ChatMessageEdited` - when a message is edited in a chat thread that the user is member of. 
 - `ChatMessageDeleted` - when a message is deleted in a chat thread that the user is member of. 
 - `TypingIndicatorReceived` - when another member is typing a message in a chat thread that the user is member of. 
 - `ReadReceiptReceived` - when another member has read the message that user sent in a chat thread. 

## Chat events 

Real-time signaling allows your users to chat in real-time. Your services can use Azure Event Grid to subscribe to chat-related events. For more details, see [Event Handling conceptual](../event-handling.md).

## Using Cognitive Services with Chat client library to enable intelligent features

You can use [Azure Cognitive APIs](https://docs.microsoft.com/azure/cognitive-services/) with the Chat client library to add intelligent features to your applications. For example, you can:

- Enable users to chat with each other in different languages. 
- Help a support agent prioritize tickets by detecting a negative sentiment of an incoming issue from a customer.
- Analyze the incoming messages for key detection and entity recognition, and prompt relevant info to the user in your app based on the message content.

One way to achieve this is by having your trusted service act as a member of a chat thread. Let's say you want to enable language translation. This service will be responsible for listening to the messages being exchanged by other members [1], calling cognitive APIs to translate the content to desired language[2,3] and sending the translated result as a message in the chat thread[4]. 

This way, the message history will contain both original and translated messages. In the client application, you can add logic to show the original or translated message. See [this quickstart](https://docs.microsoft.com/azure/cognitive-services/translator/quickstart-translator) to understand how to use Cognitive APIs to translate text to different languages. 

:::image type="content" source="../media/chat/cognitive-services.png" alt-text="Diagram showing Cognitive Services interacting with Communication Services.":::

## Next steps

> [!div class="nextstepaction"]
> [Get started with chat](../../quickstarts/chat/get-started.md)

The following documents may be interesting to you:

- Familiarize yourself with the [Chat client library](sdk-features.md)
