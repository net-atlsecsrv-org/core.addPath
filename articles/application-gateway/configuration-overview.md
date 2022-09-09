---
title: Azure Application Gateway configuration overview
description: This article describes how to configure the components of Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 07/30/2020
ms.author: absha
---

# Application Gateway configuration overview

Azure Application Gateway consists of several components that you can configure in various ways for different scenarios. This article shows you how to configure each component.

![Application Gateway components flow chart](./media/configuration-overview/configuration-overview1.png)

This image illustrates an application that has three listeners. The first two are multi-site listeners for `http://acme.com/*` and `http://fabrikam.com/*`, respectively. Both listen on port 80. The third is a basic listener that has end-to-end Transport Layer Security (TLS) termination, previously known as Secure Sockets Layer (SSL) termination.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## Prerequisites

### Azure virtual network and dedicated subnet

An application gateway is a dedicated deployment in your virtual network. Within your virtual network, a dedicated subnet is required for the application gateway. You can have multiple instances of a given application gateway deployment in a subnet. You can also deploy other application gateways in the subnet. But you can't deploy any other resource in the application gateway subnet.

> [!NOTE]
> You can't mix Standard_v2 and Standard Azure Application Gateway on the same subnet.

#### Size of the subnet

Application Gateway uses one private IP address per instance, plus another private IP address if a private front-end IP is configured.

Azure also reserves five IP addresses in each subnet for internal use: the first four and the last IP addresses. For example, consider 15 application gateway instances with no private front-end IP. You need at least 20 IP addresses for this subnet: five for internal use and 15 for the application gateway instances. So, you need a /27 subnet size or larger.

Consider a subnet that has 27 application gateway instances and an IP address for a private front-end IP. In this case, you need 33 IP addresses: 27 for the application gateway instances, one for the private front end, and five for internal use. So, you need a /26 subnet size or larger.

We recommend that you use a subnet size of at least /28. This size gives you 11 usable IP addresses. If your application load requires more than 10 Application Gateway instances, consider a /27 or /26 subnet size.

#### Network security groups on the Application Gateway subnet

Network security groups (NSGs) are supported on Application Gateway. But there are some restrictions:

- You must allow incoming Internet traffic on TCP ports 65503-65534 for the Application Gateway v1 SKU, and TCP ports 65200-65535 for the v2 SKU with the destination subnet as **Any** and source as **GatewayManager** service tag. This port range is required for Azure infrastructure communication. These ports are protected (locked down) by Azure certificates. External entities, including the customers of those gateways, can't communicate on these endpoints.

- Outbound Internet connectivity can't be blocked. Default outbound rules in the NSG allow Internet connectivity. We recommend that you:

  - Don't remove the default outbound rules.
  - Don't create other outbound rules that deny any outbound connectivity.

- Traffic from the **AzureLoadBalancer** tag with the destination subnet as **Any** must be allowed.

#### Allow Application Gateway access to a few source IPs

For this scenario, use NSGs on the Application Gateway subnet. Put the following restrictions on the subnet in this order of priority:

1. Allow incoming traffic from a source IP or IP range with the destination as the entire Application Gateway subnet address range and destination port as your inbound access port, for example, port 80 for HTTP access.
2. Allow incoming requests from source as **GatewayManager** service tag and destination as **Any** and destination ports as 65503-65534 for the Application Gateway v1 SKU, and ports 65200-65535 for v2 SKU for [back-end health status communication](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics). This port range is required for Azure infrastructure communication. These ports are protected (locked down) by Azure certificates. Without appropriate certificates in place, external entities can't initiate changes on those endpoints.
3. Allow incoming Azure Load Balancer probes (*AzureLoadBalancer* tag) and inbound virtual network traffic (*VirtualNetwork* tag) on the [network security group](https://docs.microsoft.com/azure/virtual-network/security-overview).
4. Block all other incoming traffic by using a deny-all rule.
5. Allow outbound traffic to the Internet for all destinations.

#### User-defined routes supported on the Application Gateway subnet

> [!IMPORTANT]
> Using UDRs on the Application Gateway subnet might cause the health status in the [back-end health view](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health) to appear as **Unknown**. It also might cause generation of Application Gateway logs and metrics to fail. We recommend that you don't use UDRs on the Application Gateway subnet so that you can view the back-end health, logs, and metrics.

- **v1**

   For the v1 SKU, user-defined routes (UDRs) are supported on the Application Gateway subnet, as long as they don't alter end-to-end request/response communication. For example, you can set up a UDR in the Application Gateway subnet to point to a firewall appliance for packet inspection. But you must make sure that the packet can reach its intended destination after inspection. Failure to do so might result in incorrect health-probe or traffic-routing behavior. This includes learned routes or default 0.0.0.0/0 routes that are propagated by Azure ExpressRoute or VPN gateways in the virtual network. Any scenario in which 0.0.0.0/0 needs to be redirected on-premises (forced tunneling) isn't supported for v1.

- **v2**

   For the v2 SKU, there are supported and unsupported scenarios:

   **v2 supported scenarios**
   > [!WARNING]
   > An incorrect configuration of the route table could result in asymmetrical routing in Application Gateway v2. Ensure that all management/control plane traffic is sent directly to the Internet and not through a virtual appliance. Logging and metrics could also be affected.


  **Scenario 1**: UDR to disable Border Gateway Protocol (BGP) Route Propagation to the Application Gateway subnet

   Sometimes the default gateway route (0.0.0.0/0) is advertised via the ExpressRoute or VPN gateways associated with the Application Gateway virtual network. This breaks management plane traffic, which requires a direct path to the Internet. In such scenarios, a UDR can be used to disable BGP route propagation. 

   To disable BGP route propagation, use the following steps:

   1. Create a Route Table resource in Azure.
   2. Disable the **Virtual network gateway route propagation** parameter. 
   3. Associate the Route Table to the appropriate subnet. 

   Enabling the UDR for this scenario shouldn't break any existing setups.

  **Scenario 2**: UDR to direct 0.0.0.0/0 to the Internet

   You can create a UDR to send 0.0.0.0/0 traffic directly to the Internet. 

  **Scenario 3**: UDR for Azure Kubernetes Service with kubenet

  If you're using kubenet with Azure Kubernetes Service (AKS) and Application Gateway Ingress Controller (AGIC), you'll need a route table to allow traffic sent to the pods from Application Gateway to be routed to the correct node. This won't be necessary if you use Azure CNI. 

  To use the route table to allow kubenet to work, follow the steps below:

  1. Go to the resource group created by AKS (the name of the resource group should begin with "MC_")
  2. Find the route table created by AKS in that resource group. The route table should be populated with the following information:
     - Address prefix should be the IP range of the pods you want to reach in AKS. 
     - Next hop type should be Virtual Appliance. 
     - Next hop address should be the IP address of the node hosting the pods.
  3. Associate this route table to the Application Gateway subnet. 
    
  **v2 unsupported scenarios**

  **Scenario 1**: UDR for Virtual Appliances

  Any scenario where 0.0.0.0/0 needs to be redirected through any virtual appliance, a hub/spoke virtual network, or on-premise (forced tunneling) isn't supported for V2.

## Front-end IP

You can configure the application gateway to have a public IP address, a private IP address, or both. A public IP is required when you host a back end that clients must access over the Internet via an Internet-facing virtual IP (VIP).

> [!NOTE]
> Application Gateway V2 currently does not support only private IP mode. It supports the following combinations:
>* Private IP and Public IP
>* Public IP only
>
> For more information, see [Frequently asked questions about Application Gateway](application-gateway-faq.md#how-do-i-use-application-gateway-v2-with-only-private-frontend-ip-address).


A public IP isn't required for an internal endpoint that's not exposed to the Internet. That's known as an *internal load-balancer* (ILB) endpoint or private frontend IP. An application gateway ILB is useful for internal line-of-business applications that aren't exposed to the Internet. It's also useful for services and tiers in a multi-tier application within a security boundary that aren't exposed to the Internet but that require round-robin load distribution, session stickiness, or TLS termination.

Only one public IP address or one private IP address is supported. You choose the front-end IP when you create the application gateway.

- For a public IP, you can create a new public IP address or use an existing public IP in the same location as the application gateway. For more information, see [static vs. dynamic public IP address](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#static-versus-dynamic-public-ip-address).

- For a private IP, you can specify a private IP address from the subnet where the application gateway is created. If you don't specify one, an arbitrary IP address is automatically selected from the subnet. The IP address type that you select (static or dynamic) can't be changed later. For more information, see [Create an application gateway with an internal load balancer](https://docs.microsoft.com/azure/application-gateway/application-gateway-ilb-arm).

A front-end IP address is associated to a *listener*, which checks for incoming requests on the front-end IP.

## Listeners

A listener is a logical entity that checks for incoming connection requests by using the port, protocol, host, and IP address. When you configure the listener, you must enter values for these that match the corresponding values in the incoming request on the gateway.

When you create an application gateway by using the Azure portal, you also create a default listener by choosing the protocol and port for the listener. You can choose whether to enable HTTP2 support on the listener. After you create the application gateway, you can edit the settings of that default listener (*appGatewayHttpListener*) or create new listeners.

### Listener type

When you create a new listener, you choose between [*basic* and *multi-site*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#types-of-listeners).

- If you want all of your requests (for any domain) to be accepted and forwarded to backend pools, choose basic. Learn [how to create an application gateway with a basic listener](https://docs.microsoft.com/azure/application-gateway/quick-create-portal).

- If you want to forward requests to different backend pools based on the *host* header or host names, choose multi-site listener, where you must also specify a host name that matches with the incoming request. This is because Application Gateway relies on HTTP 1.1 host headers to host more than one website on the same public IP address and port. To learn more, see [hosting multiple sites using Application Gateway](multiple-site-overview.md).

#### Order of processing listeners

For the v1 SKU, requests are matched according to the order of the rules and the type of listener. If a rule with basic listener comes first in the order, it's processed first and will accept any request for that port and IP combination. To avoid this, configure the rules with multi-site listeners first and push the rule with the basic listener to the last in the list.

For the v2 SKU, multi-site listeners are processed before basic listeners.

### Front-end IP

Choose the front-end IP address that you plan to associate with this listener. The listener will listen to incoming requests on this IP.

### Front-end port

Choose the front-end port. Select an existing port or create a new one. Choose any value from the [allowed range of ports](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#ports). You can use not only well-known ports, such as 80 and 443, but any allowed custom port that's suitable. A port can be used for public-facing listeners or private-facing listeners.

### Protocol

Choose HTTP or HTTPS:

- If you choose HTTP, the traffic between the client and the application gateway is unencrypted.

- Choose HTTPS if you want [TLS termination](features.md#secure-sockets-layer-ssltls-termination) or [end-to-end TLS encryption](https://docs.microsoft.com/azure/application-gateway/ssl-overview). The traffic between the client and the application gateway is encrypted. And the TLS connection terminates at the application gateway. If you want end-to-end TLS encryption, you must choose HTTPS and configure the **back-end HTTP** setting. This ensures that traffic is re-encrypted when it travels from the application gateway to the back end.


To configure TLS termination and end-to-end TLS encryption, you must add a certificate to the listener to enable the application gateway to derive a symmetric key. This is dictated by the TLS protocol specification. The symmetric key is used to encrypt and decrypt the traffic that's sent to the gateway. The gateway certificate must be in Personal Information Exchange (PFX) format. This format lets you export the private key that the gateway uses to encrypt and decrypt traffic.

#### Supported certificates

See [certificates supported for TLS termination](https://docs.microsoft.com/azure/application-gateway/ssl-overview#certificates-supported-for-ssl-termination).

### Additional protocol support

#### HTTP2 support

HTTP/2 protocol support is available to clients that connect to application gateway listeners only. The communication to back-end server pools is over HTTP/1.1. By default, HTTP/2 support is disabled. The following Azure PowerShell code snippet shows how to enable this:

```azurepowershell
$gw = Get-AzApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzApplicationGateway -ApplicationGateway $gw
```

#### WebSocket support

WebSocket support is enabled by default. There's no user-configurable setting to enable or disable it. You can use WebSockets with both HTTP and HTTPS listeners.

### Custom error pages

You can define custom error at the global level or the listener level. But creating global-level custom error pages from the Azure portal is currently not supported. You can configure a custom error page for a 403 web application firewall error or a 502 maintenance page at the listener level. You must also specify a publicly accessible blob URL for the given error status code. For more information, see [Create Application Gateway custom error pages](https://docs.microsoft.com/azure/application-gateway/custom-error).

![Application Gateway error codes](https://docs.microsoft.com/azure/application-gateway/media/custom-error/ag-error-codes.png)

To configure a global custom error page, see [Azure PowerShell configuration](https://docs.microsoft.com/azure/application-gateway/custom-error#azure-powershell-configuration).

### TLS policy

You can centralize TLS/SSL certificate management and reduce encryption-decryption overhead for a back-end server farm. Centralized TLS handling also lets you specify a central TLS policy that's suited to your security requirements. You can choose *default*, *predefined*, or *custom* TLS policy.

You configure TLS policy to control TLS protocol versions. You can configure an application gateway to use a minimum protocol version for TLS handshakes from TLS1.0, TLS1.1, and TLS1.2. By default, SSL 2.0 and 3.0 are disabled and aren't configurable. For more information, see [Application Gateway TLS policy overview](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview).

After you create a listener, you associate it with a request-routing rule. That rule determines how requests that are received on the listener are routed to the back end.

## Request routing rules

When you create an application gateway by using the Azure portal, you create a default rule (*rule1*). This rule binds the default listener (*appGatewayHttpListener*) with the default back-end pool (*appGatewayBackendPool*) and the default back-end HTTP settings (*appGatewayBackendHttpSettings*). After you create the  gateway, you can edit the settings of the default rule or create new rules.

### Rule type

When you create a rule, you choose between [*basic* and *path-based*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#request-routing-rules).

- Choose basic if you want to forward all requests on the associated listener (for example, *blog<i></i>.contoso.com/\*)* to a single back-end pool.
- Choose path-based if you want to route requests from specific URL paths to specific back-end pools. The path pattern is applied only to the path of the URL, not to its query parameters.

#### Order of processing rules

For the v1 and v2 SKU, pattern matching of incoming requests is processed in the order that the paths are listed in the URL path map of the path-based rule. If a request matches the pattern in two or more paths in the path map, the path that's listed first is matched. And the request is forwarded to the back end that's associated with that path.

### Associated listener

Associate a listener to the rule so that the *request-routing rule* that's associated with the listener is evaluated to determine the back-end pool to route the request to.

### Associated back-end pool

Associate to the rule the back-end pool that contains the back-end targets that serve requests that the listener receives.

 - For a basic rule, only one back-end pool is allowed. All requests on the associated listener are forwarded to that back-end pool.

 - For a path-based rule, add multiple back-end pools that correspond to each URL path. The requests that match the URL path that's entered are forwarded to the corresponding back-end pool. Also, add a default back-end pool. Requests that don't match any URL path in the rule are forwarded to that pool.

### Associated back-end HTTP setting

Add a back-end HTTP setting for each rule. Requests are routed from the application gateway to the back-end targets by using the port number, protocol, and other information that's specified in this setting.

For a basic rule, only one back-end HTTP setting is allowed. All requests on the associated listener are forwarded to the corresponding back-end targets by using this HTTP setting.

For a path-based rule, add multiple back-end HTTP settings that correspond to each URL path. Requests that match the URL path in this setting are forwarded to the corresponding back-end targets by using the HTTP settings that correspond to each URL path. Also, add a default HTTP setting. Requests that don't match any URL path in this rule are forwarded to the default back-end pool by using the default HTTP setting.

### Redirection setting

If redirection is configured for a basic rule, all requests on the associated listener are redirected to the target. This is *global* redirection. If redirection is configured for a path-based rule, only requests in a specific site area are redirected. An example is a shopping cart area that's denoted by */cart/\**. This is *path-based* redirection.

For more information about redirects, see [Application Gateway redirect overview](redirect-overview.md).

#### Redirection type

Choose the type of redirection required: *Permanent(301)*, *Temporary(307)*, *Found(302)*, or *See other(303)*.

#### Redirection target

Choose another listener or an external site as the redirection target.

##### Listener

Choose listener as the redirection target to redirect traffic from one listener to another on the gateway. This setting is required when you want to enable HTTP-to-HTTPS redirection. It redirects traffic from the source listener that checks for incoming HTTP requests to the destination listener that checks for incoming HTTPS requests. You can also choose to include the query string and path from the original request in the request that's forwarded to the redirection target.

![Application Gateway components dialog box](./media/configuration-overview/configure-redirection.png)

For more information about HTTP-to-HTTPS redirection, see:
- [HTTP-to-HTTPS redirection by using the Azure portal](redirect-http-to-https-portal.md)
- [HTTP-to-HTTPS redirection by using PowerShell](redirect-http-to-https-powershell.md)
- [HTTP-to-HTTPS redirection by using the Azure CLI](redirect-http-to-https-cli.md)

##### External site

Choose external site when you want to redirect the traffic on the listener that's associated with this rule to an external site. You can choose to include the query string from the original request in the request that's forwarded to the redirection target. You can't forward the path to the external site that was in the original request.

For more information about redirection, see:
- [Redirect traffic to an external site by using PowerShell](redirect-external-site-powershell.md)
- [Redirect traffic to an external site by using the CLI](redirect-external-site-cli.md)

### Rewrite HTTP headers and URL

By using rewrite rules, you can add, remove, or update HTTP(S) request and response headers as well as URL path and query string parameters as the request and response packets move between the client and backend pools via the application gateway.

The headers and URL parameters can be set to static values or to other headers and server variables. This helps with important use cases, such as extracting client IP addresses, removing sensitive information about the backend, adding more security, and so on.
For more information, see:

 - [Rewrite HTTP headers and URL overview](rewrite-http-headers-url.md)
 - [Configure HTTP header rewrite](rewrite-http-headers-portal.md)
 - [Configure URL rewrite](rewrite-url-portal.md)

## HTTP settings

The application gateway routes traffic to the back-end servers by using the configuration that you specify here. After you create an HTTP setting, you must associate it with one or more request-routing rules.

### Cookie-based affinity

Azure Application Gateway uses gateway-managed cookies for maintaining user sessions. When a user sends the first request to Application Gateway, it sets an affinity cookie in the response with a hash value which contains the session details, so that the subsequent requests carrying the affinity cookie will be routed to the same backend server for maintaining stickiness. 

This feature is useful when you want to keep a user session on the same server and when session state is saved locally on the server for a user session. If the application can't handle cookie-based affinity, you can't use this feature. To use it, make sure that the clients support cookies.

The [Chromium browser](https://www.chromium.org/Home) [v80 update](https://chromiumdash.appspot.com/schedule) brought a mandate where HTTP cookies without [SameSite](https://tools.ietf.org/id/draft-ietf-httpbis-rfc6265bis-03.html#rfc.section.5.3.7) attribute has to be treated as SameSite=Lax. In the case of CORS (Cross-Origin Resource Sharing) requests, if the cookie has to be sent in a third-party context, it has to use *SameSite=None; Secure* attributes and it should be sent over HTTPS only. Otherwise, in a HTTP only scenario, the browser doesn't send the cookies in the third-party context. The goal of this update from Chrome is to enhance security and to avoid Cross-Site Request Forgery (CSRF) attacks. 

To support this change, starting February 17 2020, Application Gateway (all the SKU types) will inject another cookie called *ApplicationGatewayAffinityCORS* in addition to the existing *ApplicationGatewayAffinity* cookie. The *ApplicationGatewayAffinityCORS* cookie has two more attributes added to it (*"SameSite=None; Secure"*) so that sticky session are maintained even for cross-origin requests.

Note that the default affinity cookie name is *ApplicationGatewayAffinity* and you can change it. In case you're using a custom affinity cookie name, an additional cookie is added with CORS as suffix. For example, *CustomCookieNameCORS*.

> [!NOTE]
> If the attribute *SameSite=None* is set, it is mandatory that the cookie also contains the *Secure* flag, and must be sent over HTTPS.  If session affinity is required over CORS, you must migrate your workload to HTTPS. 
Please refer to TLS offload and End-to-End TLS documentation for Application Gateway here – [Overview](ssl-overview.md), [Configure an application gateway with TLS termination using the Azure portal](create-ssl-portal.md), [Configure end-to-end TLS by using Application Gateway with the portal](end-to-end-ssl-portal.md).

### Connection draining

Connection draining helps you gracefully remove back-end pool members during planned service updates. You can apply this setting to all members of a back-end pool by enabling connection draining on the HTTP setting. It ensures that all deregistering instances of a back-end pool continue to maintain existing connections and serve on-going requests for a configurable timeout and don't receive any new requests or connections. The only exception to this are requests bound for deregistering instances because of gateway-managed session affinity and will continue to be forwarded to the deregistering instances. Connection draining applies to back-end instances that are explicitly removed from the back-end pool.

### Protocol

Application Gateway supports both HTTP and HTTPS for routing requests to the back-end servers. If you choose HTTP, traffic to the back-end servers is unencrypted. If unencrypted communication isn't acceptable, choose HTTPS.

This setting combined with HTTPS in the listener supports [end-to-end TLS](ssl-overview.md). This allows you to securely transmit sensitive data encrypted to the back end. Each back-end server in the back-end pool that has end-to-end TLS enabled must be configured with a certificate to allow secure communication.

### Port

This setting specifies the port where the back-end servers listen to traffic from the application gateway. You can configure ports ranging from 1 to 65535.

### Request timeout

This setting is the number of seconds that the application gateway waits to receive a response from the back-end server.

### Override back-end path

This setting lets you configure an optional custom forwarding path to use when the request is forwarded to the back end. Any part of the incoming path that matches the custom path in the **override backend path** field is copied to the forwarded path. The following table shows how this feature works:

- When the HTTP setting is attached to a basic request-routing rule:

  | Original request  | Override back-end path | Request forwarded to back end |
  | ----------------- | --------------------- | ---------------------------- |
  | /home/            | /override/            | /override/home/              |
  | /home/secondhome/ | /override/            | /override/home/secondhome/   |

- When the HTTP setting is attached to a path-based request-routing rule:

  | Original request           | Path rule       | Override back-end path | Request forwarded to back end |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/home/            | /pathrule*      | /override/            | /override/home/              |
  | /pathrule/home/secondhome/ | /pathrule*      | /override/            | /override/home/secondhome/   |
  | /home/                     | /pathrule*      | /override/            | /override/home/              |
  | /home/secondhome/          | /pathrule*      | /override/            | /override/home/secondhome/   |
  | /pathrule/home/            | /pathrule/home* | /override/            | /override/                   |
  | /pathrule/home/secondhome/ | /pathrule/home* | /override/            | /override/secondhome/        |
  | /pathrule/                 | /pathrule/      | /override/            | /override/                   |

### Use for app service

This is a UI only shortcut that selects the two required settings for the Azure App Service back end. It enables **pick host name from back-end address**, and it creates a new custom probe if you don't have one already. (For more information, see the [Pick host name from back-end address](#pick) setting section of this article.) A new probe is created, and the probe header is picked from the back-end member's address.

### Use custom probe

This setting associates a [custom probe](application-gateway-probe-overview.md#custom-health-probe) with an HTTP setting. You can associate only one custom probe with an HTTP setting. If you don't explicitly associate a custom probe, the [default probe](application-gateway-probe-overview.md#default-health-probe-settings) is used to monitor the health of the back end. We recommend that you create a custom probe for greater control over the health monitoring of your back ends.

> [!NOTE]
> The custom probe doesn't monitor the health of the back-end pool unless the corresponding HTTP setting is explicitly associated with a listener.

### <a name="pick"></a>Pick host name from back-end address

This capability dynamically sets the *host* header in the request to the host name of the back-end pool. It uses an IP address or FQDN.

This feature helps when the domain name of the back end is different from the DNS name of the application gateway, and the back end relies on a specific host header to resolve to the correct endpoint.

An example case is multi-tenant services as the back end. An app service is a multi-tenant service that uses a shared space with a single IP address. So, an app service can only be accessed through the hostnames that are configured in the custom domain settings.

By default, the custom domain name is *example.azurewebsites.net*. To access your app service by using an application gateway through a hostname that's not explicitly registered in the app service or through the application gateway's FQDN, you override the hostname in the original request to the app service's hostname. To do this, enable the **pick host name from backend address** setting.

For a custom domain whose existing custom DNS name is mapped to the app service, you don't have to enable this setting.

> [!NOTE]
> This setting is not required for App Service Environment, which is a dedicated deployment.

### Host name override

This capability replaces the *host* header in the incoming request on the application gateway with the host name that you specify.

For example, if *www.contoso.com* is specified in the **Host name** setting, the original request *`https://appgw.eastus.cloudapp.azure.com/path1` is changed to *`https://www.contoso.com/path1` when the request is forwarded to the back-end server.

## Back-end pool

You can point a back-end pool to four types of backend members: a specific virtual machine, a virtual machine scale set, an IP address/FQDN, or an app service. 

After you create a back-end pool, you must associate it with one or more request-routing rules. You must also configure health probes for each back-end pool on your application gateway. When a request-routing rule condition is met, the application gateway forwards the traffic to the healthy servers (as determined by the health probes) in the corresponding back-end pool.

## Health probes

An application gateway monitors the health of all resources in its back end by default. But we strongly recommend that you create a custom probe for each back-end HTTP setting to get greater control over health monitoring. To learn how to configure a custom probe, see [Custom health probe settings](application-gateway-probe-overview.md#custom-health-probe-settings).

> [!NOTE]
> After you create a custom health probe, you need to associate it to a back-end HTTP setting. A custom probe won't monitor the health of the back-end pool unless the corresponding HTTP setting is explicitly associated with a listener using a rule.

## Next steps

Now that you know about Application Gateway components, you can:

- [Create an application gateway in the Azure portal](quick-create-portal.md)
- [Create an application gateway by using PowerShell](quick-create-powershell.md)
- [Create an application gateway by using the Azure CLI](quick-create-cli.md)
