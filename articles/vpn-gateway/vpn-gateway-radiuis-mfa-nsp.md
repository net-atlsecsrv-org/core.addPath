---
title: Secure Azure VPN gateway RADIUS authentication with NPS server for Multi-Factor Authentication | Microsoft Docs'
description: Describes integrate Azure gateway RADIUS authentication with NPS server for Multi-Factor Authentication.
services: vpn-gateway
documentationcenter: na
author: ahmadnyasin  
manager: willchen
editor: ''
tags: azure-resource-manager

ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli

---
# Integrate Azure VPN gateway RADIUS authentication with NPS server for Multi-Factor Authentication 

The article describes how to integrate Network Policy Server (NPS) with Azure VPN gateway RADIUS authentication to deliver Multi-Factor Authentication (MFA) for point-to-site VPN connections. 

## Prerequisite

To enable MFA, the users must be in Azure Active Directory (Azure AD), which must be synced from either the on-premises or cloud environment. Also, the user must have already completed the auto-enrollment process for MFA.  For more information, see [Set up my account for two-step verification](../active-directory/user-help/multi-factor-authentication-end-user-first-time.md)

## Detailed steps

### Step 1: Create a virtual network gateway

1. Log on to the [Azure portal](https://portal.azure.com).
2. In the virtual network that will host the virtual network gateway, select **Subnets**, and then select **Gateway subnet** to create a subnet. 

    ![The image about how to add gateway subnet](./media/vpn-gateway-radiuis-mfa-nsp/gateway-subnet.png)
3. Create a virtual network gateway by specifying the following settings:

    - **Gateway type**: Select **VPN**.
    - **VPN type**: Select **Route-based**.
    - **SKU**: Select a SKU type based on your requirements.
    - **Virtual network**: Select the virtual network in which you created the gateway subnet.

        ![The image about virtual network gateway settings](./media/vpn-gateway-radiuis-mfa-nsp/create-vpn-gateway.png)


 
### Step 2 Configure the NPS for Azure MFA

1. On the NPS server, [install the NPS extension for Azure MFA](../active-directory/authentication/howto-mfa-nps-extension.md#install-the-nps-extension).
2. Open the NPS console, right-click **RADIUS Clients**, and then select **New**. Create the RADIUS client by specifying the following settings:

    - **Friendly Name**: Type any name.
    - **Address (IP or DNS)**: Type the gateway subnet that you created in the Step 1.
    - **Shared secret**: type any secret key, and remember it for later use.

      ![The image about RADIUS client settings](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client1.png)

 
3.  On the **Advanced** tab, set the vendor name to **RADIUS Standard** and make sure that the **Additional Options** check box is not selected.

    ![The image about RADIUS client Advanced settings](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client2.png)

4. Go to **Policies** > **Network Policies**, double-click **Connections to Microsoft Routing and Remote Access server** policy, select **Grant access**, and then click **OK**.

### Step 3 Configure the virtual network gateway

1. Log on to [Azure portal](https://portal.azure.com).
2. Open the virtual network gateway that you created. Make sure that the gateway type is set to **VPN** and that the VPN type is **route-based**.
3. Click **Point to site configuration** > **Configure now**, and then specify the following settings:

    - **Address pool**: Type the gateway subnet you created in the step 1.
    - **Authentication type**: Select **RADIUS authentication**.
    - **Server IP address**: Type the IP address of the NPS server.

      ![The image about point to site settings](./media/vpn-gateway-radiuis-mfa-nsp/configure-p2s.png)

## Next steps

- [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md)
- [Integrate your existing NPS infrastructure with Azure Multi-Factor Authentication](../active-directory/authentication/howto-mfa-nps-extension.md)
