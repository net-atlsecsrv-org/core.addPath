---
title: Troubleshoot Azure AD Connect install issues | Microsoft Docs'
description: This topic provides steps for how to troubleshoot issues with installing Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
---

# Troubleshoot: Azure AD Connect install issues

## **Recommended Steps**
Please check which [Azure AD Connect installation type](./how-to-connect-install-select-installation.md) is suitable for you. If you meet the criteria of express installation, then we highly recommend you to go with the express installation. The express installation gives you minimal options needed to finish the installation, therefore there is less likelihood of any issues. 

However, if you don’t meet the express installation criteria and must do the custom installation then here are some best practices you can follow to avoid common issues. For the sake of simplicity only selective options are mentioned here:

* Ensure you are an administrator on the machine on which you are installing AAD Connect. Log in on to the machine with same administrator credentials.

* Let all the options to be default on the following page, except for “Use an existing SQL Server”, if you want to use existing SQL Server. Here are [more details](./how-to-connect-install-custom.md) about how to use custom installation options. 

    ![Use Existing SQL Server](media/tshoot-connect-install-issues/tshoot-connect-install-issues/useexistingsqlserver.png)

* On the following page, pick option “Create new AD account", to avoid any permission issues with existing account.

    ![AD Forest Account](media/tshoot-connect-install-issues/tshoot-connect-install-issues/createnewaccount.png)

### **Common Issues**

* [Connectivity issues with on-premises Active Directory](./reference-connect-adconnectivitytools.md).

* [Connectivity issues with online Azure Active Directory](./tshoot-connect-connectivity.md).

* [Permission issues with on-premises Active Directory](./how-to-connect-configure-ad-ds-connector-account.md).

## **Recommended Documents**
* [Prerequisites for Azure AD Connect](./how-to-connect-install-prerequisites.md)
* [Select which installation type to use for Azure AD Connect](./how-to-connect-install-select-installation.md)
* [Getting started with Azure AD Connect using express settings](./how-to-connect-install-express.md)
* [Custom installation of Azure AD Connect](./how-to-connect-install-custom.md)
* [Azure AD Connect: Upgrade from a previous version to the latest](./how-to-upgrade-previous-version.md)
* [Azure AD Connect: What is staging server?](./plan-connect-topologies.md#staging-server)
* [What is the ADConnectivityTool PowerShell Module?](./how-to-connect-adconnectivitytools.md)

## Next steps
- [Azure AD Connect sync](how-to-connect-sync-whatis.md).
- [What is hybrid identity?](whatis-hybrid-identity.md)