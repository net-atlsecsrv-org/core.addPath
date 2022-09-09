---
title: Delete Windows Virtual Desktop host pool - Azure
description: How to delete a host pool in Windows Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 07/11/2020
ms.author: helohr
manager: lizross
---

# Delete a host pool

All host pools created in Windows Virtual Desktop are attached to session hosts and app groups. To delete a host pool, you need to delete its associated app groups and session hosts. Deleting an app group is fairly simple, but deleting a session host is more complicated. When you delete a session host, you need to make sure it doesn't have any active user sessions. All user sessions on the session host should be logged off to prevent users from losing data.

## Delete a host pool with PowerShell

To delete a host pool using PowerShell, you first need to delete all app groups in the host pool. To delete all app groups, run the following PowerShell cmdlet:

```powershell
Remove-AzWvdApplicationGroup -Name <appgroupname> -ResourceGroupName <resourcegroupname>
```

Next, run this cmdlet to delete the host pool:

```powershell
Remove-AzWvdHostPool -Name <hostpoolname> -ResourceGroupName <resourcegroupname> -Force:$true
```

This cmdlet removes all existing user sessions on the host pool's session host. It also unregisters the session host from the host pool. Any related virtual machines (VMs) will still exist within your subscription.

## Delete a host pool with the Azure portal

To delete a host pool in the Azure portal:

1. Sign in to the [Azure portal](https://portal.azure.com/).

2. Search for and select **Windows Virtual Desktop**.

3. Select **Host pools** in the menu on the left side of the page, then select the name of the host pool you want to delete.

4. On the menu on the left side of the page, select **Application groups**.

5. Select all application groups in the host pool you're going to delete, then select **Remove**.

6. Once you've removed the app groups, go to the menu on the left side of the page and select **Overview**.

7. Select **Remove**.

8. If there are session hosts in the host pool you're deleting, you'll see a message asking for your permission to continue. Select **Yes**.

9. The Azure portal will now remove all session hosts and delete the host pool. The VMs related to the session host won't be deleted and will remain in your subscription.

## Next steps

To learn how to create a host pool, check out these articles:

- [Create a host pool with the Azure portal](create-host-pools-azure-marketplace.md)
- [Create a host pool with PowerShell](create-host-pools-powershell.md)

To learn how to configure host pool settings, check out these articles:

- [Customize Remote Desktop Protocol properties for a host pool](customize-rdp-properties.md)
- [Configure the Windows Virtual Desktop load-balancing method](configure-host-pool-load-balancing.md)
- [Configure the personal desktop host pool assignment type](configure-host-pool-personal-desktop-assignment-type.md)