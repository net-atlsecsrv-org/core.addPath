---
title: "include file"
description: "include file"
services: storage
author: tamram
ms.service: storage
ms.topic: "include"
ms.date: 11/06/2019
ms.author: tamram
ms.custom: "include file"
---

To view and copy your storage account access keys or connection string from the Azure portal:

1. Navigate to the [Azure portal](https://portal.azure.com).
2. Locate your storage account.
3. Under **Settings**, select **Access keys**. Your account access keys appear, as well as the complete connection string for each key.
4. Find the **Key** value under **key1**, and click the **Copy** button to copy the account key.
5. Alternately, you can copy the entire connection string. Find the **Connection string** value under **key1**, and click the **Copy** button to copy the connection string.

    ![Screenshot showing how to view access keys in the Azure portal](media/storage-view-keys-include/portal-connection-string.png)

You can use either key to access Azure Storage, but in general it's a good practice to use the first key, and reserve the use of the second key for when you are rotating keys.
