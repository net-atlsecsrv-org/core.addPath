---
author: alkohli
ms.service: databox  
ms.topic: include
ms.date: 08/30/2020
ms.author: alkohli
---

1. In the [Azure portal](https://portal.azure.com/), select your Azure Stack Edge resource and then go to the **Overview**. Your device should be online.

2. Select **+ Add storage account** on the device command bar. 

   ![Add a storage account](media/azure-stack-edge-gateway-add-storage-account/add-storage-account-1.png)

3. In the **Add Edge storage account** pane, specify the following settings:

    a. A unique name for the Edge storage account on your device. Storage account names can only contain lowercase numbers and letters. Special characters are not allowed. Storage account name has to be unique within the device (not across the devices).

    b. An optional description for the information on the data the storage account is holding.  
    
    c. By default, the Edge storage account is mapped to an Azure Storage account in the cloud and the data from the storage account is automatically pushed to the cloud. Specify the Azure storage account that your Edge storage account is mapped to.  

    d. Next create a new container or select from an existing container in the Azure storage account. Any data from the device that is written to the Edge storage account is automatically uploaded to the selected storage container in the mapped Azure Storage account.

    <!--![Add a storage account](media/azure-stack-edge-gateway-add-storage-account/add-storage-account-2.png)-->

    e. After all the storage account options are specified, select **Add** to create the Edge storage account. You are notified when the Edge storage account is successfully created. The new Edge storage account is then displayed in the list of storage accounts in the Azure portal. 

    
4. If you select this new storage account and go to **Access keys**, you can find the blob service endpoint and the corresponding storage account name. Copy this information as these values together with the access keys will help you connect to the Edge storage account.

    ![Add a storage account 2](media/azure-stack-edge-gateway-add-storage-account/add-storage-account-4.png)

    You get the access keys by [Connecting to the device local APIs using Azure Resource Manager](../articles/databox-online/azure-stack-edge-j-series-connect-resource-manager.md). 
