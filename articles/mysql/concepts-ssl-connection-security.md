---
title: SSL/TLS connectivity - Azure Database for MySQL
description: Information for configuring Azure Database for MySQL and associated applications to properly use SSL connections
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 07/09/2020
---

# SSL/TLS connectivity in Azure Database for MySQL

Azure Database for MySQL supports connecting your database server to client applications using Secure Sockets Layer (SSL). Enforcing SSL connections between your database server and your client applications helps protect against "man in the middle" attacks by encrypting the data stream between the server and your application.

> [!NOTE]
> Updating the `require_secure_transport` server parameter value does not affect the MySQL service's behavior. Use the SSL and TLS enforcement features outlined in this article to secure connections to your database.

> [!IMPORTANT] 
> SSL root certificate is set to expire starting October 26th, 2020 (10/26/2020). Please update your application to use the [new certificate](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem). To learn more , see [planned certificate updates](concepts-certificate-rotation.md)

## SSL Default settings

By default, the database service should be configured to require SSL connections when connecting to MySQL.  We recommend to avoid disabling the SSL option whenever possible.

When provisioning a new Azure Database for MySQL server through the Azure portal and CLI, enforcement of SSL connections is enabled by default. 

Connection strings for various programming languages are shown in the Azure portal. Those connection strings include the required SSL parameters to connect to your database. In the Azure portal, select your server. Under the **Settings** heading, select the **Connection strings**. The SSL parameter varies based on the connector, for example "ssl=true" or "sslmode=require" or "sslmode=required" and other variations.

In some cases, applications require a local certificate file generated from a trusted Certificate Authority (CA) certificate file to connect securely. Currently customers can **only use** the predefined certificate to connect to an Azure Database for MySQL server which is located at https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem. 

Similarly, the following links point to the certificates for servers in sovereign clouds: [Azure Government](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem), [Azure China](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem), and [Azure Germany](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt).

To learn how to enable or disable SSL connection when developing application, refer to [How to configure SSL](howto-configure-ssl.md).

## TLS enforcement in Azure Database for MySQL

Azure Database for MySQL supports encryption for clients connecting to your database server using Transport Layer Security (TLS). TLS is an industry standard protocol that ensures secure network connections between your database server and client applications, allowing you to adhere to compliance requirements.

### TLS settings

Azure Database for MySQL provides the ability to enforce the TLS version for the client connections. To enforce the TLS version, use the **Minimum TLS version** option setting. The following values are allowed for this option setting:

|  Minimum TLS setting             | Client TLS version supported                |
|:---------------------------------|-------------------------------------:|
| TLSEnforcementDisabled (default) | No TLS required                      |
| TLS1_0                           | TLS 1.0, TLS 1.1, TLS 1.2  and higher           |
| TLS1_1                           | TLS 1.1, TLS 1.2   and higher                   |
| TLS1_2                           | TLS version 1.2  and higher                     |


For example, setting the value of minimum TLS setting version to TLS 1.0 means your server will allow connections from clients using TLS 1.0, 1.1, and 1.2+. Alternatively, setting this to 1.2 means that you only allow connections from clients using TLS 1.2+ and all connections with TLS 1.0 and TLS 1.1 will be rejected.

> [!Note] 
> By default, Azure Database for MySQL does not enforce a minimum TLS version (the setting `TLSEnforcementDisabled`).
>
> Once you enforce a minimum TLS version, you cannot later disable minimum version enforcement.

To learn how to set the TLS setting for your Azure Database for MySQL, refer to [How to configure TLS setting](howto-tls-configurations.md).

## Next steps

- [Connection libraries for Azure Database for MySQL](concepts-connection-libraries.md)
- Learn how to [configure SSL](howto-configure-ssl.md)
- Learn how to [configure TLS](howto-tls-configurations.md)
