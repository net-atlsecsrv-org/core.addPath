---
title: 'Configure an Always-On VPN tunnel'
titleSuffix: Azure VPN Gateway
description: Learn how to use gateways with Windows 10 Always On to establish and configure persistent device tunnels to Azure.
services: vpn-gateway
author: cherylmc

ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: cherylmc

---
# Configure an Always On VPN device tunnel

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## Configure the gateway

Configure the VPN gateway to use IKEv2 and certificate-based authentication using the [Configure a Point-to-Site VPN connection](vpn-gateway-howto-point-to-site-resource-manager-portal.md) article.

## Configure the device tunnel

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## To remove a profile

To remove the profile, run the following command:

![Cleanup](./media/vpn-gateway-howto-always-on-device-tunnel/cleanup.png)

## Next steps

For troubleshooting, see [Azure point-to-site connection problems](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
