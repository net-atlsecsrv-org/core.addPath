---
title: Invite users to Video Indexer - Azure  
titleSuffix: Azure Media Services
description: This article shows how to invite users to Video Indexer.
services: media-services
author: Juliako
manager: femila

ms.service: media-services
ms.subservice: video-indexer
ms.topic: quickstart
ms.date: 02/03/2021
ms.author: juliako
---

# Quickstart: Invite users to Video Indexer

To collaborate with your colleagues, you can invite them to your Video Indexer account. 

> [!NOTE]
> Only the account’s admin can add or remove users.

## Invite new users

1. Sign in on the [Video Indexer](https://www.videoindexer.ai/) website. Make sure you are connected with an admin account.
1. If you are the admin, you see the **Share account** button in the top-right corner. Click on the button and you can invite users. 

    :::image type="content" source="./media/invite-users/share-account.png" alt-text="Share your account":::
1. In the **Share this account with others** dialog, enter an email addresses of a person you want to invite to your Video Indexer account:

    :::image type="content" source="./media/invite-users/share-account-others.png" alt-text="Invite users to this account":::  
1. After you press **Invite**, the person will be added to the list of pending invites. <br/>You can choose from two options for each invitee who didn't yet join the account: **remove invitation** or **copy invitation URL**.

    :::image type="content" source="./media/invite-users/invites-pending.png" alt-text="Pending invites":::  
1. Once the invitee joins the account, you will see three options to choose from. Two options for roles: **contributor** (default) or **owner**. Of, you can choose to remove the invitee by pressing **Remove**.

    :::image type="content" source="./media/invite-users/joined-invitee-options.png" alt-text="Joined invitee":::  

    Users do not receive a notification upon removal. Once removed, users will not be authorized  to log in.

## Manage roles, invite more users

In addition to bringing up the **Share this account with others** dialog by clicking on **Share account** (as described above), you can do it from **Settings**.

1. Press the **Settings** button in the open account. 

    :::image type="content" source="./media/invite-users/settings.png" alt-text="Account settings":::  
1. Click the **Manage roles** button.
1. To invite another user,  click **Invite more people to this account**.

    :::image type="content" source="./media/invite-users/invite-more-people.png" alt-text="Invite more users":::  
1. Once you press **invite more people to this account** , invite dialog appears
 
    :::image type="content" source="./media/invite-users/share-account-others.png" alt-text="Invite users to this account":::  

## Next steps

You can now use the [Video Indexer website](video-indexer-view-edit.md) or [Video Indexer Developer Portal](video-indexer-use-apis.md) to see the insights of the video.

## See also

- [Video Indexer overview](video-indexer-overview.md)
- [How to sign up and upload your first video](video-indexer-get-started.md)
- [Start using APIs](video-indexer-use-apis.md)
