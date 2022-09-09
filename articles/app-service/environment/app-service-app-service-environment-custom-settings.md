---
title: Configure custom settings
description: Configure settings that apply to the entire Azure App Service environment. Learn how to do it with Azure Resource Manager templates.
author: stefsch

ms.assetid: 1d1d85f3-6cc6-4d57-ae1a-5b37c642d812
ms.topic: tutorial
ms.date: 12/19/2019
ms.author: stefsch
ms.custom: mvc
ms.custom: seodec18
---
# Custom configuration settings for App Service Environments
## Overview
Because App Service Environments (ASEs) are isolated to a single customer, there are certain configuration settings that can be applied exclusively to App Service Environments. This article documents the various specific customizations that are available for App Service Environments.

If you do not have an App Service Environment, see [How to Create an App Service Environment](app-service-web-how-to-create-an-app-service-environment.md).

You can store App Service Environment customizations by using an array in the new **clusterSettings** attribute. This attribute is found in the "Properties" dictionary of the *hostingEnvironments* Azure Resource Manager entity.

The following abbreviated Resource Manager template snippet shows the **clusterSettings** attribute:

    "resources": [
    {
       "apiVersion": "2015-08-01",
       "type": "Microsoft.Web/hostingEnvironments",
       "name": ...,
       "location": ...,
       "properties": {
          "clusterSettings": [
             {
                 "name": "nameOfCustomSetting",
                 "value": "valueOfCustomSetting"
             }
          ],
          "workerPools": [ ...],
          etc...
       }
    }

The **clusterSettings** attribute can be included in a Resource Manager template to update the App Service Environment.

## Use Azure Resource Explorer to update an App Service Environment
Alternatively, you can update the App Service Environment by using [Azure Resource Explorer](https://resources.azure.com).  

1. In Resource Explorer, go to the node for the App Service Environment (**subscriptions** > **resourceGroups** > **providers** > **Microsoft.Web** > **hostingEnvironments**). Then click the specific App Service Environment that you want to update.
2. In the right pane, click **Read/Write** in the upper toolbar to allow interactive editing in Resource Explorer.  
3. Click the blue **Edit** button to make the Resource Manager template editable.
4. Scroll to the bottom of the right pane. The **clusterSettings** attribute is at the very bottom, where you can enter or update its value.
5. Type (or copy and paste) the array of configuration values you want in the **clusterSettings** attribute.  
6. Click the green **PUT** button that's located at the top of the right pane to commit the change to the App Service Environment.

However you submit the change, it takes roughly 30 minutes multiplied by the number of front ends in the App Service Environment for the change to take effect.
For example, if an App Service Environment has four front ends, it will take roughly two hours for the configuration update to finish. While the configuration change is being rolled out, no other scaling operations or configuration change operations can take place in the App Service Environment.

## Enable Internal Encryption

The App Service Environment operates as a black box system where you cannot see the internal components or the communication within the system. To enable higher throughput, encryption is not enabled by default between internal components. The system is secure as the traffic is completely inaccessible to being monitored or accessed. If you have a compliance requirement though that requires complete encryption of the data path from end to end, there is a way to enable this with a clusterSetting.  

        "clusterSettings": [
            {
                "name": "InternalEncryption",
                "value": "1"
            }
        ],
 
After the InternalEncryption clusterSetting is enabled, there can be an impact to your system performance. When you make the change to enable InternalEncryption, your ASE will be in an unstable state until the change is fully propagated. Complete propagation of the change can take a few hours to complete, depending on how many instances you have in your ASE. We highly recommend that you do not enable this on an ASE while it is in use. If you need to enable this on an actively used ASE, we highly recommend that you divert traffic to a backup environment until the operation completes. 

## Disable TLS 1.0 and TLS 1.1

If you want to manage TLS settings on an app by app basis, then you can use the guidance provided with the [Enforce TLS settings](../configure-ssl-bindings.md#enforce-tls-versions) documentation. 

If you want to disable all inbound TLS 1.0 and TLS 1.1 traffic for all of the apps in an ASE, you can set the following **clusterSettings** entry:

        "clusterSettings": [
            {
                "name": "DisableTls1.0",
                "value": "1"
            }
        ],

The name of the setting says 1.0 but when configured, it disables both TLS 1.0 and TLS 1.1.

## Change TLS cipher suite order
Another question from customers is if they can modify the list of ciphers negotiated by their server and this can be achieved by modifying the **clusterSettings** as shown below. The list of cipher suites available can be retrieved from [this MSDN article](https://msdn.microsoft.com/library/windows/desktop/aa374757\(v=vs.85\).aspx).

        "clusterSettings": [
            {
                "name": "FrontEndSSLCipherSuiteOrder",
                "value": "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384_P256,TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256"
            }
        ],

> [!WARNING]
> If incorrect values are set for the cipher suite that SChannel cannot understand, all TLS communication to your server might stop functioning. In such a case, you will need to remove the *FrontEndSSLCipherSuiteOrder* entry from **clusterSettings** and submit the updated Resource Manager template to revert back to the default cipher suite settings.  Please use this functionality with caution.
> 
> 

## Get started
The Azure Quickstart Resource Manager template site includes a template with the base definition for [creating an App Service Environment](https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/).

<!-- LINKS -->

<!-- IMAGES -->
