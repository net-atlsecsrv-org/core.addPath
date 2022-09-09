---
title: How to use Queue storage from Python - Azure Storage
description: Learn how to use the Azure Queue service from Python to create and delete queues, and insert, get, and delete messages.
services: storage
author: mhopkins-msft
ms.service: storage

ms.devlang: python
ms.topic: article
ms.date: 12/14/2018
ms.author: mhopkins
ms.reviewer: cbrooks
ms.subservice: queues
---

# How to use Queue storage from Python
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## Overview
This guide shows you how to perform common scenarios using the Azure Queue storage service. The samples are written in Python and use the [Microsoft Azure Storage SDK for Python]. The scenarios covered include **inserting**, **peeking**, **getting**, and **deleting** queue messages, as well as **creating and deleting queues**. For more information on queues, refer to the [Next Steps] section.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## Download and Install Azure Storage SDK for Python

The [Azure Storage SDK for Python](https://github.com/azure/azure-storage-python) requires Python 2.7, 3.3, 3.4, 3.5, or 3.6.
 
### Install via PyPi

To install via the Python Package Index (PyPI), type:

```bash
pip install azure-storage-queue
```

> [!NOTE]
> If you are upgrading from the Azure Storage SDK for Python version 0.36 or earlier, uninstall the older SDK using `pip uninstall azure-storage` before installing the latest package.

For alternative installation methods, see [Azure Storage SDK for Python](https://github.com/Azure/azure-storage-python/).

## View the sample application

To view and run a sample application that shows how to use Python with Azure Queues, see [Azure Storage: Getting Started with Azure Queues in Python](https://github.com/Azure-Samples/storage-queue-python-getting-started). 

To run the sample application, make sure you have installed both the `azure-storage-queue` and `azure-storage-common` packages.

## How To: Create a Queue

The **QueueService** object lets you work with queues. The following code creates a **QueueService** object. Add the following near the top of any Python file in which you wish to programmatically access Azure Storage:

```python
from azure.storage.queue import QueueService
```

The following code creates a **QueueService** object using the storage account name and account key. Replace 'myaccount' and 'mykey' with your account name and key.

```python
queue_service = QueueService(account_name='myaccount', account_key='mykey')

queue_service.create_queue('taskqueue')
```

## How To: Insert a Message into a Queue
To insert a message into a queue, use the **put\_message** method to
create a new message and add it to the queue.

```python
queue_service.put_message('taskqueue', u'Hello World')
```

## How To: Peek at the Next Message
You can peek at the message in the front of a queue without removing it
from the queue by calling the **peek\_messages** method. By default,
**peek\_messages** peeks at a single message.

```python
messages = queue_service.peek_messages('taskqueue')
for message in messages:
    print(message.content)
```

## How To: Dequeue Messages
Your code removes a message from a queue in two steps. When you call
**get\_messages**, you get the next message in a queue by default. A
message returned from **get\_messages** becomes invisible to any other
code reading messages from this queue. By default, this message stays
invisible for 30 seconds. To finish removing the message from the queue,
you must also call **delete\_message**. This two-step process of removing
a message assures that when your code fails to process a message due to
hardware or software failure, another instance of your code can get the
same message and try again. Your code calls **delete\_message** right
after the message has been processed.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

There are two ways you can customize message retrieval from a queue.
First, you can get a batch of messages (up to 32). Second, you can set a
longer or shorter invisibility timeout, allowing your code more or less
time to fully process each message. The following code example uses the
**get\_messages** method to get 16 messages in one call. Then it processes
each message using a for loop. It also sets the invisibility timeout to
five minutes for each message.

```python
messages = queue_service.get_messages('taskqueue', num_messages=16, visibility_timeout=5*60)
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)        
```

## How To: Change the Contents of a Queued Message
You can change the contents of a message in-place in the queue. If the
message represents a work task, you could use this feature to update the
status of the work task. The code below uses the **update\_message**
method to update a message. The visibility timeout is set to 0, meaning the
message appears immediately and the content is updated.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    queue_service.update_message('taskqueue', message.id, message.pop_receipt, 0, u'Hello World Again')
```

## How To: Get the Queue Length
You can get an estimate of the number of messages in a queue. The
**get\_queue\_metadata** method asks the queue service to return metadata
about the queue, and the **approximate_message_count**. The result is only
approximate because messages can be added or removed after the
queue service responds to your request.

```python
metadata = queue_service.get_queue_metadata('taskqueue')
count = metadata.approximate_message_count
```

## How To: Delete a Queue
To delete a queue and all the messages contained in it, call the
**delete\_queue** method.

```python
queue_service.delete_queue('taskqueue')
```

## Next Steps
Now that you've learned the basics of Queue storage, follow these links
to learn more.

* [Python Developer Center](https://azure.microsoft.com/develop/python/)
* [Azure Storage Services REST API](https://msdn.microsoft.com/library/azure/dd179355)

[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
[Microsoft Azure Storage SDK for Python]: https://github.com/Azure/azure-storage-python
