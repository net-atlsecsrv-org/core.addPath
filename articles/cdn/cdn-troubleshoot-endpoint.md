---
title: Troubleshooting Azure CDN endpoints - 404 status code
description: Troubleshoot 404 response codes with Azure CDN endpoints.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''

ms.assetid: b588a1eb-ab69-4fc7-ae4d-157c3e46f4a8
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/23/2017
ms.author: mazha

---
# Troubleshooting Azure CDN endpoints that return a 404 status code
This article enables you to troubleshoot issues with Azure Content Delivery Network (CDN) endpoints that return 404 HTTP response status codes.

If you need more help at any point in this article, you can contact the Azure experts on [the MSDN Azure and the Stack Overflow forums](https://azure.microsoft.com/support/forums/). Alternatively, you can also file an Azure support incident. Go to the [Azure Support site](https://azure.microsoft.com/support/options/) and select **Get Support**.

## Symptom
You've created a CDN profile and an endpoint, but your content doesn't seem to be available on the CDN. Users who attempt to access your content via the CDN URL receive an HTTP 404 status code. 

## Cause
There are several possible causes, including:

* The file's origin isn't visible to the CDN.
* The endpoint is misconfigured, causing the CDN to look in the wrong place.
* The host is rejecting the host header from the CDN.
* The endpoint hasn't had time to propagate throughout the CDN.

## Troubleshooting steps
> [!IMPORTANT]
> After creating a CDN endpoint, it will not immediately be available for use, as it takes time for the registration to propagate through the CDN:
> - For **Azure CDN Standard from Microsoft** profiles, propagation usually completes in ten minutes. 
> - For **Azure CDN Standard from Akamai** profiles, propagation usually completes within one minute. 
> - For **Azure CDN Standard from Verizon** and **Azure CDN Premium from Verizon** profiles, propagation usually completes within 90 minutes. 
> 
> If you complete the steps in this document and you're still getting 404 responses, consider waiting a few hours to check again before opening a support ticket.
> 
> 

### Check the origin file
First, verify that the file to cache is available on the origin server and is publicly accessible on the internet. The quickest way to do that is to open a browser in a private or incognito session and browse directly to the file. Type or paste the URL into the address box and verify that it results in the file you expect. For example, suppose you have a file in an Azure Storage account, accessible at https:\//cdndocdemo.blob.core.windows.net/publicblob/lorem.txt. If you can successfully load this file's contents, it passes the test.

![Success!](./media/cdn-troubleshoot-endpoint/cdn-origin-file.png)

> [!WARNING]
> While this is the quickest and easiest way to verify your file is publicly available, some network configurations in your organization could make it appear that a file is publicly available when it is, in fact, only visible to users of your network (even if it's hosted in Azure). To ensure that this isn't the case, test the file with an external browser, such as a mobile device that is not connected to your organization's network, or a virtual machine in Azure.
> 
> 

### Check the origin settings
After you've verified the file is publicly available on the internet, verify your origin settings. In the [Azure Portal](https://portal.azure.com), browse to your CDN profile and select the endpoint you're troubleshooting. From the resulting **Endpoint** page, select the origin.  

![Endpoint page with origin highlighted](./media/cdn-troubleshoot-endpoint/cdn-endpoint.png)

The **Origin** page appears. 

![Origin page](./media/cdn-troubleshoot-endpoint/cdn-origin-settings.png)

#### Origin type and hostname
Verify that the values of the **Origin type** and **Origin hostname** are correct. In this example, https:\//cdndocdemo.blob.core.windows.net/publicblob/lorem.txt, the hostname portion of the URL is *cdndocdemo.blob.core.windows.net*, which is correct. Because Azure Storage, Web App, and Cloud Service origins use a drop-down list value for the **Origin hostname** field, incorrect spellings aren't an issue. However, if you use a custom origin, ensure that your hostname is spelled correctly.

#### HTTP and HTTPS ports
Check your **HTTP** and **HTTPS ports**. In most cases, 80 and 443 are correct, and you will require no changes.  However, if the origin server is listening on a different port, that will need to be represented here. If you're not sure, view the URL for your origin file. The HTTP and HTTPS specifications use ports 80 and 443 as the defaults. In the example URL, https:\//cdndocdemo.blob.core.windows.net/publicblob/lorem.txt, a port is not specified, so the default of 443 is assumed and the settings are correct.  

However, suppose the URL for the origin file that you tested earlier is http:\//www.contoso.com:8080/file.txt. Note the *:8080* portion at the end of the hostname segment. That number instructs the browser to use port 8080 to connect to the web server at www\.contoso.com, therefore you'll need to enter *8080* in the **HTTP port** field. It's important to note that these port settings affect only what port the endpoint uses to retrieve information from the origin.

> [!NOTE]
> **Azure CDN Standard from Akamai** endpoints do not allow the full TCP port range for origins.  For a list of origin ports that are not allowed, see [Azure CDN from Akamai Allowed Origin Ports](/previous-versions/azure/mt757337(v=azure.100)).  
> 
> 

### Check the endpoint settings
On the **Endpoint** page, select the **Configure** button.

![Endpoint page with configure button highlighted](./media/cdn-troubleshoot-endpoint/cdn-endpoint-configure-button.png)

The CDN endpoint **Configure** page appears.

![Configure page](./media/cdn-troubleshoot-endpoint/cdn-configure.png)

#### Protocols
For **Protocols**, verify that the protocol being used by the clients is selected. Because the same protocol used by the client is the one used to access the origin, it's important to have the origin ports configured correctly in the previous section. The CDN endpoint listens only on the default HTTP and HTTPS ports (80 and 443), regardless of the origin ports.

Let's return to our hypothetical example with http:\//www.contoso.com:8080/file.txt.  As you'll remember, Contoso specified *8080* as their HTTP port, but let's also assume they specified *44300* as their HTTPS port.  If they created an endpoint named *contoso*, their CDN endpoint hostname would be *contoso.azureedge.net*.  A request for http:\//contoso.azureedge.net/file.txt is an HTTP request, so the endpoint would use HTTP on port 8080 to retrieve it from the origin.  A secure request over HTTPS, https:\//contoso.azureedge.net/file.txt, would cause the endpoint to use HTTPS on port 44300 when retrieving the file from the origin.

#### Origin host header
The **Origin host header** is the host header value sent to the origin with each request.  In most cases, this should be the same as the **Origin hostname** we verified earlier.  An incorrect value in this field won't generally cause 404 statuses, but is likely to cause other 4xx statuses, depending on what the origin expects.

#### Origin path
Lastly, we should verify our **Origin path**.  By default this is blank.  You should only use this field if you want to narrow the scope of the origin-hosted resources you want to make available on the CDN.  

In the example endpoint, we wanted all resources on the storage account to be available, so **Origin path** was left blank.  This means that a request to https:\//cdndocdemo.azureedge.net/publicblob/lorem.txt results in a connection from the endpoint to cdndocdemo.core.windows.net that requests */publicblob/lorem.txt*.  Likewise, a request for https:\//cdndocdemo.azureedge.net/donotcache/status.png results in the endpoint requesting */donotcache/status.png* from the origin.

But what if you don't want to use the CDN for every path on your origin?  Say you only wanted to expose the *publicblob* path.  If we enter */publicblob* in the **Origin path** field, that will cause the endpoint to insert */publicblob* before every request being made to the origin.  This means that the request for https:\//cdndocdemo.azureedge.net/publicblob/lorem.txt will now actually take the request portion of the URL, */publicblob/lorem.txt*, and append */publicblob* to the beginning. This results in a request for */publicblob/publicblob/lorem.txt* from the origin.  If that path doesn't resolve to an actual file, the origin will return a 404 status.  The correct URL to retrieve lorem.txt in this example would actually be https:\//cdndocdemo.azureedge.net/lorem.txt.  Note that we don't include the */publicblob* path at all, because the request portion of the URL is */lorem.txt* and the endpoint adds */publicblob*, resulting in */publicblob/lorem.txt* being the request passed to the origin.

