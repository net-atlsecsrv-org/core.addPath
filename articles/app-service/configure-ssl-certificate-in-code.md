---
title: Use SSL certificate in code - Azure App Service | Microsoft Docs
description: Learn how to use client certificates to connect to remote resources that require them.
services: app-service
documentationcenter: 
author: cephalin
manager: gwallace
editor: ''

ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: cephalin
ms.reviewer: yutlin
ms.custom: seodec18

---

# Use an SSL certificate in your code in Azure App Service

In your application code, you can access the [public or private certificates you add to App Service](configure-ssl-certificate.md). Your app code may act as a client and access an external service that requires certificate authentication, or it may need to perform cryptographic tasks. This how-to guide shows how to use public or private certificates in your application code.

This approach to using certificates in your code makes use of the SSL functionality in App Service, which requires your app to be in **Basic** tier or above. If your app is in **Free** or **Shared** tier, you can [include the certificate file in your app repository](#load-certificate-from-file).

When you let App Service manage your SSL certificates, you can maintain the certificates and your application code separately and safeguard your sensitive data.

## Prerequisites

To follow this how-to guide:

- [Create an App Service app](/azure/app-service/)
- [Add a certificate to your app](configure-ssl-certificate.md)

## Find the thumbprint

In the <a href="https://portal.azure.com" target="_blank">Azure portal</a>, from the left menu, select **App Services** > **\<app-name>**.

From the left navigation of your app, select **TLS/SSL settings**, then select **Private Key Certificates (.pfx)** or **Public Key Certificates (.cer)**.

Find the certificate you want to use and copy the thumbprint.

![Copy the certificate thumbprint](./media/configure-ssl-certificate/create-free-cert-finished.png)

## Make the certificate accessible

To access a certificate in your app code, add its thumbprint to the `WEBSITE_LOAD_CERTIFICATES` app setting, by running the following command in the <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

To make all your certificates accessible, set the value to `*`.

## Load certificate in Windows apps

The `WEBSITE_LOAD_CERTIFICATES` app setting makes the specified certificates accessible to your Windows hosted app in the Windows certificate store, and the location depends on the [pricing tier](overview-hosting-plans.md):

- **Isolated** tier - in [Local Machine\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores). 
- All other tiers - in [Current User\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores).

In C# code, you access the certificate by the certificate thumbprint. The following code loads a certificate with the thumbprint `E661583E8FABEF4C0BEF694CBC41C28FB81CD870`.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
X509Store certStore = new X509Store(StoreName.My, StoreLocation.CurrentUser);
certStore.Open(OpenFlags.ReadOnly);
X509Certificate2Collection certCollection = certStore.Certificates.Find(
                            X509FindType.FindByThumbprint,
                            // Replace below with your certificate's thumbprint
                            "E661583E8FABEF4C0BEF694CBC41C28FB81CD870",
                            false);
// Get the first cert with the thumbprint
if (certCollection.Count > 0)
{
    X509Certificate2 cert = certCollection[0];
    // Use certificate
    Console.WriteLine(cert.FriendlyName);
}
certStore.Close();
...
```

In Java code, you access the certificate from the "Windows-MY" store using the Subject Common Name field (see [Public key certificate](https://en.wikipedia.org/wiki/Public_key_certificate)). The following code shows how to load a private key certificate:

```java
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.bind.annotation.RequestMapping;
import java.security.KeyStore;
import java.security.cert.Certificate;
import java.security.PrivateKey;

...
KeyStore ks = KeyStore.getInstance("Windows-MY");
ks.load(null, null); 
Certificate cert = ks.getCertificate("<subject-cn>");
PrivateKey privKey = (PrivateKey) ks.getKey("<subject-cn>", ("<password>").toCharArray());

// Use the certificate and key
...
```

For languages that don't support or offer insufficient support for the Windows certificate store, see [Load certificate from file](#load-certificate-from-file).

## Load certificate in Linux apps

The `WEBSITE_LOAD_CERTIFICATES` app settings makes the specified certificates accessible to your Linux hosted apps (including custom container apps) as files. The files are found under the following directories:

- Private certificates - `/var/ssl/private` ( `.p12` files)
- Public certificates - `/var/ssl/certs` ( `.der` files)

The certificate file names are the certificate thumbprints. The following C# code shows how to load a public certificate in a Linux app.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
var bytes = System.IO.File.ReadAllBytes("/var/ssl/certs/<thumbprint>.der");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

To see how to load an SSL certificate from a file in Node.js, PHP, Python, Java, or Ruby, see the documentation for the respective language or web platform.

## Load certificate from file

If you need to load a certificate file that you upload manually, it's better to upload the certificate using [FTPS](deploy-ftp.md) instead of [Git](deploy-local-git.md), for example. You should keep sensitive data like a private certificate out of source control.

> [!NOTE]
> ASP.NET and ASP.NET Core on Windows must access the certificate store even if you load a certificate from a file. To load a certificate file in a Windows .NET app, load the current user profile with the following command in the <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>:
>
> ```azurecli-interactive
> az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
> ```
>
> This approach to using certificates in your code makes use of the SSL functionality in App Service, which requires your app to be in **Basic** tier or above.

The following C# example loads a public certificate from a relative path in your app:

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
var bytes = System.IO.File.ReadAllBytes("~/<relative-path-to-cert-file>");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

To see how to load an SSL certificate from a file in Node.js, PHP, Python, Java, or Ruby, see the documentation for the respective language or web platform.

## More resources

* [Secure a custom DNS name with an SSL binding](configure-ssl-bindings.md)
* [Enforce HTTPS](configure-ssl-bindings.md#enforce-https)
* [Enforce TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions)
* [FAQ : App Service Certificates](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
