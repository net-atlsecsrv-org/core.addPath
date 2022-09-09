---
title: azcopy login | Microsoft Docs
description: This article provides reference information for the azcopy login command.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
---

# azcopy login

Logs in to Azure Active Directory to access Azure Storage resources.

## Synopsis

Log in to Azure Active Directory to access Azure Storage resources.

To be authorized to your Azure Storage account, you must assign the **Storage Blob Data Contributor** role to your user account in the context of either the Storage account, parent resource group or parent subscription.

This command will cache encrypted login information for current user using the OS built-in mechanisms.

Please refer to the examples for more information.

> [!IMPORTANT]
> If you set an environment variable by using the command line, that variable will be readable in your command line history. Consider clearing variables that contain credentials from your command line history. To keep variables from appearing in your history, you can use a script to prompt the user for their credentials, and to set the environment variable.

```azcopy
azcopy login [flags]
```

## Examples

Log in interactively with default AAD tenant ID set to common:

```azcopy
azcopy login
```

Log in interactively with a specified tenant ID:

```azcopy
azcopy login --tenant-id "[TenantID]"
```

Log in by using a VM's system-assigned identity:

```azcopy
azcopy login --identity
```

Log in by using a VM's user-assigned identity with a client ID of the service identity:

```azcopy
azcopy login --identity --identity-client-id "[ServiceIdentityClientID]"
```

Log in using a VM's user-assigned identity with an object ID of the service identity:

```azcopy
azcopy login --identity --identity-object-id "[ServiceIdentityObjectID]"
```

Log in using a VM's user-assigned identity with a resource ID of the service identity:

```azcopy
azcopy login --identity --identity-resource-id "/subscriptions/<subscriptionId>/resourcegroups/myRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID"
```

Log in as a service principal using a client secret. Set the environment variable AZCOPY_SPA_CLIENT_SECRET to the client secret for secret based service principal auth.

```azcopy
azcopy login --service-principal
```

Log in as a service principal using a certificate and password. Set the environment variable AZCOPY_SPA_CERT_PASSWORD to the certificate's password for cert-based service principal authorization.

```azcopy
azcopy login --service-principal --certificate-path /path/to/my/cert
```

Make sure to treat /path/to/my/cert as a path to a PEM or PKCS12 file. AzCopy does not reach into the system cert store to obtain your certificate.

--certificate-path is mandatory when doing cert-based service principal auth.

## Options

|Option|Description|
|--|--|
|--application-id string|Application ID of user-assigned identity. Required for service principal auth.|
|--certificate-path string|Path to certificate for SPN authentication. Required for certificate-based service principal auth.|
|-h, --help|Show help content for the login command.|
|--identity|log in using virtual machine's identity, also known as managed service identity (MSI).|
|--identity-client-id string|Client ID of user-assigned identity.|
|--identity-object-id string|Object ID of user-assigned identity.|
|--identity-resource-id string|Resource ID of user-assigned identity.|
|--service-principal|Log in via SPN (Service Principal Name) by using a certificate or a secret. The client secret or certificate password must be placed in the appropriate environment variable. Type `AzCopy env` to see names and descriptions of environment variables.|
|--tenant-id string| the Azure active directory tenant ID to use for OAuth device interactive login.|

## Options inherited from parent commands

|Option|Description|
|---|---|
|--cap-mbps uint32|Caps the transfer rate, in megabits per second. Moment-by-moment throughput might vary slightly from the cap. If this option is set to zero, or it is omitted, the throughput isn't capped.|
|--output-type string|Format of the command's output. The choices include: text, json. The default value is "text".|

## See also

- [azcopy](storage-ref-azcopy.md)
