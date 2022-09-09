---
title: 'Azure Virtual WAN: User VPN client profiles'
description: This helps you work with the client profile file
services: virtual-wan
author: cherylmc

ms.service: virtual-wan
ms.topic: article
ms.date: 03/18/2020
ms.author: cherylmc

---
# Working with User VPN client profiles

The downloaded profile file contains information that is necessary to configure a VPN connection. This article helps you obtain and understand the information necessary for a User VPN client profile.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* The **OpenVPN folder** contains the *ovpn* profile that needs to be modified to include the key and the certificate. For more information, see [Configure OpenVPN clients](../virtual-wan/howto-openvpn-clients.md#windows).

## Next steps

For more information about Virtual WAN User VPN, see [Create a User VPN connection](virtual-wan-point-to-site-portal.md).
