---
title: 'Azure AD Connect cloud provisioning agent: Automatic upgrade | Microsoft Docs'
description: This topic describes the built-in automatic upgrade feature in the Azure AD Connect cloud provisioning agent.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath

ms.collection: M365-identity-device-management
---
# Azure AD Connect cloud provisioning agent: Automatic upgrade

Making sure your Azure AD Connect cloud provisioning agent installation is always up to date has never been easier with the **automatic upgrade** feature. This feature is enabled by default and cannot be disabled.

The agent is installed here:  **"Program files\Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe"**

You can verify your version by right-clicking on the executable and selecting properties and then details.

![Agent file version](media/how-to-automatic-upgrade/agent1.png)

The agent updater is installed here:  **"Program files\Azure AD Connect Provisioning Agent Updater\AzureADConnectAgentUpdater.exe"**

You can verify your version by right-clicking on the executable and selecting properties and then details.

![Agent updater version](media/how-to-automatic-upgrade/agent2.png)

## Uninstalling the agent
To remove the agent, navigate to **Uninstall or change a program** and uninstall the following:

- Microsoft Azure AD Connect Agent Updater
- Microsoft Azure AD Connect Provisioning Agent
- Microsoft Azure AD Connect Provisioning Agent Package

![Agent removal](media/how-to-automatic-upgrade/agent3.png)

## Next steps 

- [What is provisioning?](what-is-provisioning.md)
- [What is Azure AD Connect cloud provisioning?](what-is-cloud-provisioning.md)

