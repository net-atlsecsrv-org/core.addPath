---
title: Remove TLS 1.0 and 1.1 from use with Azure Cache for Redis
description: Learn how to remove TLS 1.0 and 1.1 from your application when communicating with Azure Cache for Redis
author: yegu-ms

ms.service: cache
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: yegu

---

# Remove TLS 1.0 and 1.1 from use with Azure Cache for Redis

There's an industry-wide push toward the exclusive use of Transport Layer Security (TLS) version 1.2 or later. TLS versions 1.0 and 1.1 are known to be susceptible to attacks such as BEAST and POODLE, and to have other Common Vulnerabilities and Exposures (CVE) weaknesses. They also don't support the modern encryption methods and cipher suites recommended by Payment Card Industry (PCI) compliance standards. This [TLS security blog](https://www.acunetix.com/blog/articles/tls-vulnerabilities-attacks-final-part/) explains some of these vulnerabilities in more detail.

As a part of this effort, we'll be making the following changes to Azure Cache for Redis:

* **Phase 1:** We'll configure the default minimum TLS version to be 1.2 for newly created cache instances (previously TLS 1.0).  Existing cache instances won't be updated at this point. You'll be allowed to [change the minimum TLS version](cache-configure.md#access-ports) back to 1.0 or 1.1 for backward compatibility, if needed. This change can be done through the Azure portal or other management APIs.
* **Phase 2:** We'll stop supporting TLS versions 1.0 and 1.1. After this change, your application will be required to use TLS 1.2 or later to communicate with your cache.

Additionally, as a part of this change, we'll be removing support for older, insecure cypher suites.  Our supported cypher suites will be restricted to the following when the cache is configured with a minimum TLS version of 1.2.

* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256

This article provides general guidance about how to detect dependencies on these earlier TLS versions and remove them from your application.

The dates when these changes take effect are:

| Cloud                | Phase 1 Start Date | Phase 2 Start Date      |
|----------------------|--------------------|-------------------------|
| Azure (global)       |  January 13, 2020  | May 11, 2020            |
| Azure Government     |  March 13, 2020    | May 11, 2020            |
| Azure Germany        |  March 13, 2020    | May 11, 2020            |
| Azure China 21Vianet |  March 13, 2020    | May 11, 2020            |

## Check whether your application is already compliant

The easiest way to find out whether your application will work with TLS 1.2 is to set the **Minimum TLS version** value to TLS 1.2 on a test or staging cache that it uses. The **Minimum TLS version** setting is in the [Advanced settings](cache-configure.md#advanced-settings) of your cache instance in the Azure portal. If the application continues to function as expected after this change, it's probably compliant. You might need to configure some Redis client libraries used by your application specifically to enable TLS 1.2, so they can connect to Azure Cache for Redis over that security protocol.

## Configure your application to use TLS 1.2

Most applications use Redis client libraries to handle communication with their caches. Here are instructions for configuring some of the popular client libraries, in various programming languages and frameworks, to use TLS 1.2.

### .NET Framework

Redis .NET clients use the earliest TLS version by default on .NET Framework 4.5.2 or earlier, and use the latest TLS version on .NET Framework 4.6 or later. If you're using an older version of .NET Framework, you can enable TLS 1.2 manually:

* **StackExchange.Redis:** Set `ssl=true` and `sslprotocols=tls12` in the connection string.
* **ServiceStack.Redis:** Follow the [ServiceStack.Redis](https://github.com/ServiceStack/ServiceStack.Redis#servicestackredis-ssl-support) instructions and requires ServiceStack.Redis v5.6 at a minimum.

### .NET Core

Redis .NET Core clients default to the OS default TLS version which obviously depends on the OS itself. 

Depending on when the OS was released and if any other patches changed the default TLS version, the OS TLS version could be quite varied. While there is no complete information about this, for Windows OS specifically you can find more information [here](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12). 

However, if you are using a old OS or just wanted to be sure we recommend to configure the preferred TLS version manually through the client.


### Java

Redis Java clients use TLS 1.0 on Java version 6 or earlier. Jedis, Lettuce, and Redisson can't connect to Azure Cache for Redis if TLS 1.0 is disabled on the cache. Upgrade your Java framework to use new TLS versions.

For Java 7, Redis clients don't use TLS 1.2 by default but can be configured for it. Jedis allows you to specify the underlying TLS settings with the following code snippet:

``` Java
SSLSocketFactory sslSocketFactory = (SSLSocketFactory) SSLSocketFactory.getDefault();
SSLParameters sslParameters = new SSLParameters();
sslParameters.setEndpointIdentificationAlgorithm("HTTPS");
sslParameters.setProtocols(new String[]{"TLSv1.2"});
 
URI uri = URI.create("rediss://host:port");
JedisShardInfo shardInfo = new JedisShardInfo(uri, sslSocketFactory, sslParameters, null);
 
shardInfo.setPassword("cachePassword");
 
Jedis jedis = new Jedis(shardInfo);
```

The Lettuce and Redisson clients don't yet support specifying the TLS version, so they'll break if the cache accepts only TLS 1.2 connections. Fixes for these clients are being reviewed, so check with those packages for an updated version with this support.

In Java 8, TLS 1.2 is used by default and shouldn't require updates to your client configuration in most cases. To be safe, test your application.

### Node.js

Node Redis and IORedis use TLS 1.2 by default.

### PHP

#### Predis
 
* Versions earlier than PHP 7: Predis supports only TLS 1.0. These versions don't work with TLS 1.2; you must upgrade to use TLS 1.2.
 
* PHP 7.0 to PHP 7.2.1: Predis uses only TLS 1.0 or 1.1 by default. You can use the following workaround to use TLS 1.2. Specify TLS 1.2 when you create the client instance:

  ``` PHP
  $redis=newPredis\Client([
      'scheme'=>'tls',
      'host'=>'host',
      'port'=>6380,
      'password'=>'password',
      'ssl'=>[
          'crypto_type'=>STREAM_CRYPTO_METHOD_TLSv1_2_CLIENT,
      ],
  ]);
  ```

* PHP 7.3 and later versions: Predis uses the latest TLS version.

#### PhpRedis

PhpRedis doesn't support TLS on any PHP version.

### Python

Redis-py uses TLS 1.2 by default.

### GO

Redigo uses TLS 1.2 by default.

## Additional information

- [How to configure Azure Cache for Redis](cache-configure.md)
