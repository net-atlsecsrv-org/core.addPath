---
title: Use Azure PowerShell to create a Service Bus queue
description: In this quickstart, you learn how to create a Service Bus namespace and a queue in the namespace by using the Azure PowerShell.  
author: spelluru
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 08/12/2020
ms.author: spelluru 
ms.custom: devx-track-azurepowershell
---

# Use Azure PowerShell to create a Service Bus namespace and a queue
This quickstart shows you how to create a Service Bus namespace and a queue using the Azure PowerShell. It also shows you how to get authorization credentials that a client application can use to send/receive messages to/from the queue. 

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]


## Prerequisites

To complete this quickstart, make sure you have an Azure subscription. If you don't have an Azure subscription, you can create a [free account][] before you begin. 

In this quickstart, you use Azure Cloud Shell that you can launch after sign into the Azure portal. For details about Azure Cloud Shell, see [Overview of Azure Cloud Shell](../cloud-shell/overview.md). You can also [install](/powershell/azure/install-Az-ps) and use Azure PowerShell on your machine. 


## Provision resources
1. Sign into the [Azure portal](https://portal.azure.com).
2. Launch Azure Cloud Shell by selecting the icon shown in the following image: 

    :::image type="content" source="./media/service-bus-quickstart-powershell/launch-cloud-shell.png" alt-text="Launch Cloud Shell":::
3. In the bottom Cloud Shell window, switch from **Bash** to **PowerShell**. 

    :::image type="content" source="./media/service-bus-quickstart-powershell/cloud-power-shell.png" alt-text="Switch to PowerShell mode":::    
4. Run the following command to create an Azure resource group. Update the resource group name and the location if you want. 

    ```azurepowershell-interactive
    New-AzResourceGroup –Name ContosoRG –Location eastus
    ```
5. Run the following command to create a Service Bus messaging namespace. In this example, `ContosoRG` is the resource group you created in the previous step. `ContosoSBusNS` is the name of the Service Bus namespace created in that resource group. 

    ```azurepowershell-interactive
    New-AzServiceBusNamespace -ResourceGroupName ContosoRG -Name ContosoSBusNS -Location eastus
    ```
6. Run the following to create a queue in the namespace you created in the previous step. 

    ```azurepowershell-interactive
    New-AzServiceBusQueue -ResourceGroupName ContosoRG -NamespaceName ContosoSBusNS -Name ContosoOrdersQueue 
    ```
7. Get the primary connection string for the namespace. You use this connection string to connect to the queue and send and receive messages. 

    ```azurepowershell-interactive    
    Get-AzServiceBusKey -ResourceGroupName ContosoRG -Namespace ContosoSBusNS -Name RootManageSharedAccessKey
    ```

    Note down the connection string and the queue name. You use them to send and receive messages. 


## Next steps
In this article, you created a Service Bus namespace and a queue in the namespace. To learn how to send/receive messages to/from the queue, see one of the following quickstarts in the **Send and receive messages** section. 

- [.NET](service-bus-dotnet-get-started-with-queues.md)
- [Java](service-bus-java-how-to-use-queues.md)
- [JavaScript](service-bus-nodejs-how-to-use-queues.md)
- [Python](service-bus-python-how-to-use-queues.md)
- [PHP](service-bus-php-how-to-use-queues.md)
- [Ruby](service-bus-ruby-how-to-use-queues.md)

[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio

