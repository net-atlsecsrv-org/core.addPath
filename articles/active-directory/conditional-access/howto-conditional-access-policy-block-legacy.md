---
title: Conditional Access - Block legacy authentication - Azure Active Directory
description: Create a custom Conditional Access policy to block legacy authentication protocols

services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/16/2019

ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya

ms.collection: M365-identity-device-management
---
# Conditional Access: Block legacy authentication

Due to the increased risk associated with legacy authentication protocols, Microsoft recommends that organizations block authentication requests using these protocols and require modern authentication.

## Create a Conditional Access policy

The following steps will help create a Conditional Access policy to block legacy authentication requests.

1. Sign in to the **Azure portal** as a global administrator, security administrator, or Conditional Access administrator.
1. Browse to **Azure Active Directory** > **Conditional Access**.
1. Select **New policy**.
1. Give your policy a name. We recommend that organizations create a meaningful standard for the names of their policies.
1. Under **Assignments**, select **Users and groups**
   1. Under **Include**, select **All users**.
   1. Under **Exclude**, select **Users and groups** and choose any accounts that must maintain the ability to use legacy authentication. 
   1. Select **Done**.
1. Under **Cloud apps or actions** > **Include**, select **All cloud apps**.
   1. If you must exclude specific applications from your policy, you can choose them from the **Exclude** tab under **Select excluded cloud apps** and choose **Select**.
   1. Select **Done**.
1. Under **Conditions** > **Client apps (preview)**, set **Configure** to **Yes**.
   1. Check only the boxes **Mobile apps and desktop clients** > **Other clients**.
   2. Select **Done**.
1. Under **Access controls** > **Grant**, select **Block access**.
   1. Select **Select**.
1. Confirm your settings and set **Enable policy** to **On**.
1. Select **Create** to create to enable your policy.

## Next steps

[Conditional Access common policies](concept-conditional-access-policy-common.md)

[Simulate sign in behavior using the Conditional Access What If tool](troubleshoot-conditional-access-what-if.md)
