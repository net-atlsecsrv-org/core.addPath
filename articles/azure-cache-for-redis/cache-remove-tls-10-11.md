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

Although none of these considerations pose an immediate problem, we recommend that you stop using TLS 1.0 and 1.1 soon. Azure Cache for Redis will stop supporting these TLS versions on March 31, 2020. After that date, your application will be required to use TLS 1.2 or later to communicate with your cache.

This article provides general guidance about how to detect dependencies on these earlier TLS versions and remove them from your application.

## Check whether your application is already compliant

The easiest way to find out whether your application will work with TLS 1.2 is to set the **Minimum TLS version** value to TLS 1.2 on a test or staging cache that it uses. The **Minimum TLS version** setting is in the [Advanced settings](cache-configure.md#advanced-settings) of your cache instance in the Azure portal. If the application continues to function as expected after this change, it's probably compliant. You might need to configure some Redis client libraries used by your application specifically to enable TLS 1.2, so they can connect to Azure Cache for Redis over that security protocol.

## Configure your application to use TLS 1.2

Most applications use Redis client libraries to handle communication with their caches. Here are instructions for configuring some of the popular client libraries, in various programming languages and frameworks, to use TLS 1.2.

### .NET Framework

Redis .NET clients use the earliest TLS version by default on .NET Framework 4.5.2 or earlier, and use the latest TLS version on .NET Framework 4.6 or later. If you're using an older version of .NET Framework, you can enable TLS 1.2 manually:

* **StackExchange.Redis:** Set `ssl=true` and `sslprotocols=tls12` in the connection string.
* **ServiceStack.Redis:** Follow the [ServiceStack.Redis instructions](https://github.com/ServiceStack/ServiceStack.Redis/pull/247).

### .NET Core

Redis .NET Core clients use the latest TLS version by default.

### Java

Redis Java clients use TLS 1.0 on Java version 6 or earlier. Jedis, Lettuce, and Radisson can't connect to Azure Cache for Redis if TLS 1.0 is disabled on the cache. There's currently no known workaround.

On Java 7 or later, Redis clients don't use TLS 1.2 by default but can be configured for it. Lettuce and Radisson don't support this configuration right now. They'll break if the cache accepts only TLS 1.2 connections. Jedis allows you to specify the underlying TLS settings with the following code snippet:

``` Java
SSLSocketFactory sslSocketFactory = (SSLSocketFactory) SSLSocketFactory.getDefault();
SSLParameters sslParameters = new SSLParameters();
sslParameters.setEndpointIdentificationAlgorithm("HTTPS");
sslParameters.setProtocols(new String[]{"TLSv1", "TLSv1.1", "TLSv1.2"});
 
URI uri = URI.create("rediss://host:port");
JedisShardInfo shardInfo = new JedisShardInfo(uri, sslSocketFactory, sslParameters, null);
 
shardInfo.setPassword("cachePassword");
 
Jedis jedis = new Jedis(shardInfo);
```

### Node.js

Node Redis and IORedis use TLS 1.2 by default.

### PHP

Predis on PHP 7 won't work because PHP 7 supports only TLS 1.0. On PHP 7.2.1 or earlier, Predis uses TLS 1.0 or 1.1 by default. You can specify TLS 1.2 when you create the client instance:

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

On PHP 7.3 or above, Predis uses the latest TLS version.

PhpRedis doesn't support TLS on any PHP version.

### Python

Redis-py uses TLS 1.2 by default.

### GO

Redigo uses TLS 1.2 by default.

## Additional information

- [How to configure Azure Cache for Redis](cache-configure.md)
