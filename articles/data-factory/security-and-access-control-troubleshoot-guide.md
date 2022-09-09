---
title: Troubleshoot security and access control issues
description: Learn how to troubleshoot security and access control issues in Azure Data Factory. 
services: data-factory
author: lrtoyou1223
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/19/2020
ms.author: lle
ms.reviewer: craigg
---

# Troubleshoot Azure Data Factory security and access control issues

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

This article explores common troubleshooting methods for security and access control in Azure Data Factory.

## Common errors and messages

### Invalid or empty Authentication key issue after disabling public network access

#### Symptoms

Self-hosted integration runtime throws error “The Authentication key is invalid or empty” after disabling public network access for Data Factory.

#### Cause

The problem is most likely caused by DNS resolution issue, as disabling public connectivity and establishing a private endpoint does no help for reconnecting.

#### Resolution

1. Did a PsPing to ADF’s FQDN and found that the buffer was going to a public endpoint of ADF, even after having it disabled.

1. Change the host file in VM to map private IP to FQDN and run a PsPing again. Buffer will be able to go to the correct private IP of ADF then.

1. Re-register the self-hosted integration runtime and we will find it up and running.
 

### Unable to register IR authentication key on Self-hosted VMs due to private link

#### Symptoms

Unable to register IR authentication key on Self-hosted VM due to private link enabled.

The error information shows as below:

`
Failed to get service token from ADF service with key *************** and time cost is: 0.1250079 seconds, the error code is: InvalidGatewayKey, activityId is: XXXXXXX and detailed error message is Client IP address is not valid private ip Cause Data factory couldn’t access the public network thereby not able to reach out to the cloud to make the successful connection.
`

#### Cause

The issue could be caused by the VM where the SHIR is trying to be installed. Public network access should be enabled for connecting to the cloud.

#### Resolution

 **Solution 1:** You can follow below steps to resolve the issue:

1. Navigate to the below link: 
    
    https://docs.microsoft.com/rest/api/datafactory/Factories/Update

1. Click on **Try It** option and fill all required details. Paste below property in the **Body** as well:

    ```
    { "tags": { "publicNetworkAccess":"Enabled" } }
    ``` 

1. Click **Run** at the end of the page to run the function. Make sure that you would get Response Code 200. The property we pasted will be shown in JSON definition as well.

1. Then you can try to add IR authentication key again in the integration runtime.


**Solution 2:** You can refer to below article for solution:

https://docs.microsoft.com/azure/data-factory/data-factory-private-link

Try to enable the public network access with user interface.

![Enable public network access](media/self-hosted-integration-runtime-troubleshoot-guide/enable-public-network-access.png)

## Next steps

For more help with troubleshooting, try the following resources:

*  [Private Link for Data Factory](data-factory-private-link.md)
*  [Data Factory blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory feature requests](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure videos](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&A question page](/answers/topics/azure-data-factory.html)
*  [Stack overflow forum for Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter information about Data Factory](https://twitter.com/hashtag/DataFactory)