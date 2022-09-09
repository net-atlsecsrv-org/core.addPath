---
title: Provide security contact details in Azure Security Center | Microsoft Docs
description: This document shows you how to provide security contact details in Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 26b5dcb4-ce3f-4f22-8d56-d2bf743cfc90
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/11/2020
ms.author: memildin

---
# Set up email notifications for security alerts 

To ensure the right people in your organization are notified about security alerts in your environment, enter their email addresses in the **Email notifications** settings page.

When setting up your notifications, you can configure the emails to be sent to specific individuals or to anyone with a specific RBAC role for a subscription. 

To avoid alert fatigue, Security Center limits the volume of outgoing mails. For each subscription, Security Center sends:

- a maximum of **four** emails per day for **high-severity** alerts
- a maximum of **two** emails per day for **medium-severity** alerts
- a maximum of **one** email per day for **low-severity** alerts

## Availability

- Release state: **Generally Available**
- Required roles: **Security Admin** or **Subscription Owner** 
- Clouds: 
  ✔ Commercial clouds
  ✔ US Gov (partial)
  ✘ National/Sovereign (China Gov, Other Gov)


## Set up email notifications for alerts <a name="email"></a>

You can send email notifications to individuals or to all users with specific RBAC roles.

1. From Security Center's **Pricing & settings** area, the relevant subscription, and select **Email notifications**.

1. Define the recipients for your notifications:

    - From the dropdown list, select from the available roles.
    - And/or enter specific email addresses separated by commas. There is no limit to the number of email addresses that you can enter.

1. To apply the security contact information to your subscription, select **Save**.


## See also
To learn more about security alerts, see the following:

* [Security alerts - a reference guide](alerts-reference.md) -- Learn about the security alerts you might see in Azure Security Center's Threat Protection module
* [Manage and respond to security alerts in Azure Security Center](security-center-managing-and-responding-alerts.md) -- Learn how to manage and respond to security alerts