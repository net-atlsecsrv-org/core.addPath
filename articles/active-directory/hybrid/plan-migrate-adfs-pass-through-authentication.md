---
title: 'Azure AD Connect: Migrate from federation to PTA for Azure AD'
description: This article has information about moving your hybrid identity environment from federation to pass-through authentication.
services: active-directory
author: billmath
manager: daveba
ms.reviewer: martincoetzer
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/29/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
---

# Migrate from federation to pass-through authentication for Azure Active Directory

This article describes how to move your organization domains from Active Directory Federation Services (AD FS) to pass-through authentication.

> [!NOTE]
> Changing your authentication method requires planning, testing, and potentially downtime. [Staged rollout](how-to-connect-staged-rollout.md) provides an alternative way to test and gradually migrate from federation to cloud authentication using pass-through authentication.
> 
> If you plan on using staged rollout, you should remember to turn off the staged rollout features once you have finished cutting over.  For more information see [Migrate to cloud authentication using staged rollout](how-to-connect-staged-rollout.md)


## Prerequisites for migrating to pass-through authentication

The following prerequisites are required to migrate from using AD FS to using pass-through authentication.

### Update Azure AD Connect

To successfully complete the steps it takes to migrate to using pass-through authentication, you must have [Azure Active Directory Connect](https://www.microsoft.com/download/details.aspx?id=47594) (Azure AD Connect) 1.1.819.0 or a later version. In Azure AD Connect 1.1.819.0, the way sign-in conversion is performed changes significantly. The overall time to migrate from AD FS to cloud authentication in this version is reduced from potentially hours to minutes.

> [!IMPORTANT]
> You might read in outdated documentation, tools, and blogs that user conversion is required when you convert domains from federated identity to managed identity. *Converting users* is no longer required. Microsoft is working to update documentation and tools to reflect this change.

To update Azure AD Connect, complete the steps in [Azure AD Connect: Upgrade to the latest version](./how-to-upgrade-previous-version.md).

### Plan authentication agent number and placement

Pass-through authentication requires deploying lightweight agents on the Azure AD Connect server and on your on-premises computer that's running Windows Server. To reduce latency, install the agents as close as possible to your Active Directory domain controllers.

For most customers, two or three authentication agents are sufficient to provide high availability and the required capacity. A tenant can have a maximum of 12 agents registered. The first agent is always installed on the Azure AD Connect server itself. To learn about agent limitations and agent deployment options, see [Azure AD pass-through authentication: Current limitations](./how-to-connect-pta-current-limitations.md).

### Plan the migration method

You can choose from two methods to migrate from federated identity management to pass-through authentication and seamless single sign-on (SSO). The method you use depends on how your AD FS instance was originally configured.

* **Azure AD Connect**. If you originally configured AD FS by using Azure AD Connect, you *must* change to pass-through authentication by using the Azure AD Connect wizard.

   ???Azure AD Connect automatically runs the **Set-MsolDomainAuthentication** cmdlet when you change the user sign-in method. Azure AD Connect automatically unfederates all the verified federated domains in your Azure AD tenant.

   > [!NOTE]
   > Currently, if you originally used Azure AD Connect to configure AD FS, you can't avoid unfederating all domains in your tenant when you change the user sign-in to pass-through authentication.
???
* **Azure AD Connect with PowerShell**. You can use this method only if you didn't originally configure AD FS by using Azure AD Connect. For this option, you still must change the user sign-in method via the Azure AD Connect wizard. The core difference with this option is that the wizard doesn't automatically run the **Set-MsolDomainAuthentication** cmdlet. With this option, you have full control over which domains are converted and in which order.

To understand which method you should use, complete the steps in the following sections.

#### Verify current user sign-in settings

1. Sign in to the [Azure AD portal](https://aad.portal.azure.com/) by using a Global Administrator account.
2. In the **User sign-in** section, verify the following settings:
   * **Federation** is set to **Enabled**.
   * **Seamless single sign-on** is set to **Disabled**.
   * **Pass-through authentication** is set to **Disabled**.

   ![Screenshot of the settings in the Azure AD Connect User sign-in section](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image1.png)

#### Verify how federation was configured

1. On your Azure AD Connect server, open Azure AD Connect. Select **Configure**.
2. On the **Additional tasks** page, select **View current configuration**, and then select **Next**.<br />
 
   ![Screenshot of the View current configuration option on the Additional tasks page](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image2.png)<br />
3. Under **Additional Tasks > Manage Federation**, scroll to **Active Directory Federation Services (AD FS)**.<br />

   * If the AD FS configuration appears in this section, you can safely assume that AD FS was originally configured by using Azure AD Connect. You can convert your domains from federated identity to managed identity by using the Azure AD Connect **Change user sign-in** option. For more information about the process, see the section **Option A: Configure pass-through authentication by using Azure AD Connect**.
   * If AD FS isn't listed in the current settings, you must manually convert your domains from federated identity to managed identity by using PowerShell. For more information about this process, see the section **Option B: Switch from federation to pass-through authentication by using Azure AD Connect and PowerShell**.

### Document current federation settings

To find your current federation settings, run the **Get-MsolDomainFederationSettings** cmdlet:

``` PowerShell
Get-MsolDomainFederationSettings -DomainName YourDomain.extention | fl *
```

Example:

``` PowerShell
Get-MsolDomainFederationSettings -DomainName Contoso.com | fl *
```

Verify any settings that might have been customized for your federation design and deployment documentation. Specifically, look for customizations in **PreferredAuthenticationProtocol**, **SupportsMfa**, and **PromptLoginBehavior**.

For more information, see these articles:

* [AD FS prompt=login parameter support](/windows-server/identity/ad-fs/operations/ad-fs-prompt-login)
* [Set-MsolDomainAuthentication](/powershell/module/msonline/set-msoldomainauthentication)

> [!NOTE]
> If **SupportsMfa** is set to **True**, you're using an on-premises multi-factor authentication solution to inject a second-factor challenge into the user authentication flow. This setup no longer works for Azure AD authentication scenarios. 
>
> Instead, use the Azure AD Multi-Factor Authentication cloud-based service to perform the same function. Carefully evaluate your multi-factor authentication requirements before you continue. Before you convert your domains, make sure that you understand how to use Azure AD Multi-Factor Authentication, the licensing implications, and the user registration process.

#### Back up federation settings

Although no changes are made to other relying parties in your AD FS farm during the processes described in this article, we recommend that you have a current valid backup of your AD FS farm that you can restore from. You can create a current valid backup by using the free Microsoft [AD FS Rapid Restore Tool](/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool). You can use the tool to back up AD FS, and to restore an existing farm or create a new farm.

If you choose not to use the AD FS Rapid Restore Tool, at a minimum, you should export the Microsoft 365 Identity Platform relying party trust and any associated custom claim rules you added. You can export the relying party trust and associated claim rules by using the following PowerShell example:

``` PowerShell
(Get-AdfsRelyingPartyTrust -Name "Microsoft Office 365 Identity Platform") | Export-CliXML "C:\temp\O365-RelyingPartyTrust.xml"
```

## Deployment considerations and using AD FS

This section describes deployment considerations and details about using AD FS.

### Current AD FS use

Before you convert from federated identity to managed identity, look closely at how you currently use AD FS for Azure AD, Microsoft 365, and other applications (relying party trusts). Specifically, consider the scenarios that are described in the following table:

| If | Then |
|-|-|
| You plan to keep using AD FS with other applications (other than Azure AD and Microsoft 365). | After you convert your domains, you'll use both AD FS and Azure AD. Consider the user experience. In some scenarios, users might be required to authenticate twice: once to Azure AD (where a user gets SSO access to other applications, like Microsoft 365), and again for any applications that are still bound to AD FS as a relying party trust. |
| Your AD FS instance is heavily customized and relies on specific customization settings in the onload.js file (for example, if you changed the sign-in experience so that users use only a **SamAccountName** format for their username instead of a User Principal Name (UPN), or your organization has heavily branded the sign-in experience). The onload.js file can't be duplicated in Azure AD. | Before you continue, you must verify that Azure AD can meet your current customization requirements. For more information and for guidance, see the sections on AD FS branding and AD FS customization.|
| You use AD FS to block earlier versions of authentication clients.| Consider replacing AD FS controls that block earlier versions of authentication clients by using a combination of [Conditional Access controls](../conditional-access/concept-conditional-access-conditions.md) and [Exchange Online Client Access Rules](/exchange/clients-and-mobile-in-exchange-online/client-access-rules/client-access-rules). |
| You require users to perform multi-factor authentication against an on-premises multi-factor authentication server solution when users authenticate to AD FS.| In a managed identity domain, you can't inject a multi-factor authentication challenge via the on-premises multi-factor authentication solution into the authentication flow. However, you can use the Azure AD Multi-Factor Authentication service for multi-factor authentication after the domain is converted.<br /><br /> If your users don't currently use Azure AD Multi-Factor Authentication, a onetime user registration step is required. You must prepare for and communicate the planned registration to your users. |
| You currently use access control policies (AuthZ rules) in AD FS to control access to Microsoft 365.| Consider replacing the policies with the equivalent Azure AD [Conditional Access policies](../conditional-access/overview.md) and [Exchange Online Client Access Rules](/exchange/clients-and-mobile-in-exchange-online/client-access-rules/client-access-rules).|

### Common AD FS customizations

This section describes common AD FS customizations.

#### InsideCorporateNetwork claim

AD FS issues the **InsideCorporateNetwork** claim if the user who is authenticating is inside the corporate network. This claim can then be passed on to Azure AD. The claim is used to bypass multi-factor authentication based on the user's network location. To learn how to determine whether this functionality currently is available in AD FS, see [Trusted IPs for federated users](../authentication/howto-mfa-adfs.md).

The **InsideCorporateNetwork** claim isn't available after your domains are converted to pass-through authentication. You can use [named locations in Azure AD](../reports-monitoring/quickstart-configure-named-locations.md) to replace this functionality.

After you configure named locations, you must update all Conditional Access policies that were configured to either include or exclude the network **All trusted locations** or **MFA Trusted IPs** values to reflect the new named locations.

For more information about the **Location** condition in Conditional Access, see [Active Directory Conditional Access locations](../conditional-access/location-condition.md).

#### Hybrid Azure AD-joined devices

When you join a device to Azure AD, you can create Conditional Access rules that enforce that devices meet your access standards for security and compliance. Also, users can sign in to a device by using an organizational work or school account instead of a personal account. When you use hybrid Azure AD-joined devices, you can join your Active Directory domain-joined devices to Azure AD. Your federated environment might have been set up to use this feature.

To ensure that hybrid join continues to work for any devices that are joined to the domain after your domains are converted to pass-through authentication, for Windows 10 clients, you must use Azure AD Connect to sync Active Directory computer accounts to Azure AD.

For Windows 8 and Windows 7 computer accounts, hybrid join uses seamless SSO to register the computer in Azure AD. You don't have to sync Windows 8 and Windows 7 computer accounts like you do for Windows 10 devices. However, you must deploy an updated workplacejoin.exe file (via an .msi file) to Windows 8 and Windows 7 clients so they can register themselves by using seamless SSO. [Download the .msi file](https://www.microsoft.com/download/details.aspx?id=53554).

For more information, see [Configure hybrid Azure AD-joined devices](../devices/hybrid-azuread-join-plan.md).

#### Branding

If your organization [customized your AD FS sign-in pages](/windows-server/identity/ad-fs/operations/ad-fs-user-sign-in-customization) to display information that's more pertinent to the organization, consider making similar [customizations to the Azure AD sign-in page](../fundamentals/customize-branding.md).

Although similar customizations are available, some visual changes on sign-in pages should be expected after the conversion. You might want to provide information about expected changes in your communications to users.

> [!NOTE]
> Organization branding is available only if you purchase the Premium or Basic license for Azure Active Directory or if you have a Microsoft 365 license.

## Plan for smart lockout

Azure AD smart lockout protects against brute-force password attacks. Smart lockout prevents an on-premises Active Directory account from being locked out when pass-through authentication is being used and an account lockout group policy is set in Active Directory.

For more information, see [Azure Active Directory smart lockout](../authentication/howto-password-smart-lockout.md).

## Plan deployment and support

Complete the tasks that are described in this section to help you plan for deployment and support.

### Plan the maintenance window

Although the domain conversion process is relatively quick, Azure AD might continue to send some authentication requests to your AD FS servers for up to four hours after the domain conversion is finished. During this four-hour window, and depending on various service side caches, Azure AD might not accept these authentications. Users might receive an error. The user can still successfully authenticate against AD FS, but Azure AD no longer accepts the user???s issued token because that federation trust is now removed.

Only users who access the services via a web browser during this post-conversion window before the service side cache is cleared are affected. Legacy clients (Exchange ActiveSync, Outlook 2010/2013) aren't expected to be affected because Exchange Online keeps a cache of their credentials for a set period of time. The cache is used to silently reauthenticate the user. The user doesn't have to return to AD FS. Credentials stored on the device for these clients are used to silently reauthenticate themselves after this cached is cleared. Users aren't expected to receive any password prompts as a result of the domain conversion process.

Modern authentication clients (Office 2016 and Office 2013, iOS, and Android apps) use a valid refresh token to obtain new access tokens for continued access to resources instead of returning to AD FS. These clients are immune to any password prompts resulting from the domain conversion process. The clients will continue to function without additional configuration.

> [!IMPORTANT]
> Don???t shut down your AD FS environment or remove the Microsoft 365 relying party trust until you have verified that all users can successfully authenticate by using cloud authentication.

### Plan for rollback

If you encounter a major issue that you can't resolve quickly, you might decide to roll back the solution to federation. It???s important to plan what to do if your deployment doesn???t roll out as intended. If conversion of the domain or users fails during deployment, or if you need to roll back to federation, you must understand how to mitigate any outage and reduce the effect on your users.

#### To roll back

To plan for rollback, check the federation design and deployment documentation for your specific deployment details. The process should include these tasks:

* Converting managed domains to federated domains by using the **Convert-MSOLDomainToFederated** cmdlet.
* If necessary, configuring additional claims rules.

### Plan communications

An important part of planning deployment and support is ensuring that your users are proactively informed about upcoming changes. Users should know in advance what they might experience and what is required of them.

After both pass-through authentication and seamless SSO are deployed, the user sign-in experience for accessing Microsoft 365 and other resources that are authenticated through Azure AD changes. Users who are outside the network see only the Azure AD sign-in page. These users aren't redirected to the forms-based page that's presented by external-facing web application proxy servers.

Include the following elements in your communication strategy:

* Notify users about upcoming and released functionality by using:
   * Email and other internal communication channels.
   * Visuals, such as posters.
   * Executive, live, or other communications.
* Determine who will customize the communications and who will send the communications, and when.

## Implement your solution

You planned your solution. Now, you can now implement it. Implementation involves the following components:

* Preparing for seamless SSO.
* Changing the sign-in method to pass-through authentication and enabling seamless SSO.

### Step 1: Prepare for seamless SSO

For your devices to use seamless SSO, you must add an Azure AD URL to users' intranet zone settings by using a group policy in Active Directory.

By default, web browsers automatically calculate the correct zone, either internet or intranet, from a URL. For example, **http:\/\/contoso/** maps to the intranet zone and **http:\/\/intranet.contoso.com** maps to the internet zone (because the URL contains a period). Browsers send Kerberos tickets to a cloud endpoint, like the Azure AD URL, only if you explicitly add the URL to the browser's intranet zone.

Complete the steps to [roll out](./how-to-connect-sso-quick-start.md) the required changes to your devices.

> [!IMPORTANT]
> Making this change doesn't modify the way your users sign in to Azure AD. However, it???s important that you apply this configuration to all your devices before you proceed. Users who sign in on devices that haven't received this configuration simply are required to enter a username and password to sign in to Azure AD.

### Step 2: Change the sign-in method to pass-through authentication and enable seamless SSO

You have two options for changing the sign-in method to pass-through authentication and enabling seamless SSO.

#### Option A: Configure pass-through authentication by using Azure AD Connect

Use this method if you initially configured your AD FS environment by using Azure AD Connect. You can't use this method if you *didn't* originally configure your AD FS environment by using Azure AD Connect.

> [!IMPORTANT]
> After you complete the following steps, all your domains are converted from federated identity to managed identity. For more information, review [Plan the migration method](#plan-the-migration-method).

First, change the sign-in method:

1. On the Azure AD Connect server, open the Azure AD Connect wizard.
2. Select **Change user sign-in**, and then select **Next**. 
3. On the **Connect to Azure AD** page, enter the username and password of a Global Administrator account.
4. On the **User sign-in** page, select the **Pass-through authentication** button, select **Enable single sign-on**, and then select **Next**.
5. On the **Enable single sign-on** page, enter the credentials of a Domain Administrator account, and then select **Next**.

   > [!NOTE]
   > Domain Administrator account credentials are required to enable seamless SSO. The process completes the following actions, which require these elevated permissions. The Domain Administrator account credentials aren't stored in Azure AD Connect or in Azure AD. The Domain Administrator account credentials are used only to turn on the feature. The credentials are discarded when the process successfully finishes.
   >
   > 1. A computer account named AZUREADSSOACC (which represents Azure AD) is created in your on-premises Active Directory instance.
   > 2. The computer account's Kerberos decryption key is securely shared with Azure AD.
   > 3. Two Kerberos service principal names (SPNs) are created to represent two URLs that are used during Azure AD sign-in.

6. On the **Ready to configure** page, make sure that the **Start the synchronization process when configuration completes** check box is selected. Then, select **Configure**.<br />

   ![Screenshot of the Ready to configure page](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image8.png)<br />
7. In the Azure AD portal, select **Azure Active Directory**, and then select **Azure AD Connect**.
8. Verify these settings:
   * **Federation** is set to **Disabled**.
   * **Seamless single sign-on** is set to **Enabled**.
   * **Pass-through authentication** is set to **Enabled**.<br />

   ![Screenshot that shows the settings in the User sign-in section](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image9.png)<br />

Next. deploy additional authentication methods:

1. In the Azure portal, go to **Azure Active Directory** > **Azure AD Connect**, and then select **Pass-through authentication**.
2. On the **Pass-through authentication** page, select the **Download** button.
3. On the **Download agent** page, select **Accept terms and download**.

   Additional authentication agents start to download. Install the secondary authentication agent on a domain-joined server. 

   > [!NOTE]
   > The first agent is always installed on the Azure AD Connect server itself as part of the configuration changes made in the **User sign-in** section of the Azure AD Connect tool. Install any additional authentication agents on a separate server. We recommend that you have two or three additional authentication agents available. 

4. Run the authentication agent installation. During installation, you must enter the credentials of a Global Administrator account.

   ![Screenshot that shows the Install button you use to run the Microsoft Azure AD Connect Authentication Agent Package.](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image11.png)

   ![Screenshot that shows the Microsoft sign-in page.](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image12.png)

5. When the authentication agent is installed, you can return to the pass-through authentication agent health page to check the status of the additional agents.

Skip to [Testing and next steps](#testing-and-next-steps).

> [!IMPORTANT]
> Skip the section **Option B: Switch from federation to pass-through authentication by using Azure AD Connect and PowerShell**. The steps in that section don't apply if you chose Option A to change the sign-in method to pass-through authentication and enable seamless SSO. 

#### Option B: Switch from federation to pass-through authentication by using Azure AD Connect and PowerShell

Use this option if you didn't initially configure your federated domains by using Azure AD Connect.

First, enable pass-through authentication:

1. On the Azure AD Connect Server, open the Azure AD Connect wizard.
2. Select **Change user sign-in**, and then select **Next**.
3. On the **Connect to Azure AD** page, enter the username and password of a Global Administrator account.
4. On the **User sign-in** page, select the **Pass-through authentication** button. Select **Enable single sign-on**, and then select **Next**.
5. On the **Enable single sign-on** page, enter the credentials of a Domain Administrator account, and then select **Next**.

   > [!NOTE]
   > Domain Administrator account credentials are required to enable seamless SSO. The process completes the following actions, which require these elevated permissions. The Domain Administrator account credentials aren't stored in Azure AD Connect or in Azure AD. The Domain Administrator account credentials are used only to turn on the feature. The credentials are discarded when the process successfully finishes.
   > 
   > 1. A computer account named AZUREADSSOACC (which represents Azure AD) is created in your on-premises Active Directory instance.
   > 2. The computer account's Kerberos decryption key is securely shared with Azure AD.
   > 3. Two Kerberos service principal names (SPNs) are created to represent two URLs that are used during Azure AD sign-in.

6. On the **Ready to configure** page, make sure that the **Start the synchronization process when configuration completes** check box is selected. Then, select **Configure**.<br />

   ???![Screenshot that shows the Ready to configure page and the Configure button](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image18.png)<br />
   The following steps occur when you select **Configure**:

   1. The first pass-through authentication agent is installed.
   2. The pass-through feature is enabled.
   3. Seamless SSO is enabled.

7. Verify these settings:
   * **Federation** is set to **Enabled**.
   * **Seamless single sign-on** is set to **Enabled**.
   * **Pass-through authentication** is set to **Enabled**.
   
   ![Screenshot that shows the settings to verify in the User sign-in section.](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image19.png)
8. Select **Pass-through authentication** and verify that the status is **Active**.<br />
   
   If the authentication agent isn't active, complete some [troubleshooting steps](./tshoot-connect-pass-through-authentication.md) before you continue with the domain conversion process in the next step. You risk causing an authentication outage if you convert your domains before you validate that your pass-through authentication agents are successfully installed and that their status **Active** in the Azure portal.

Next, deploy additional authentication agents:

1. In the Azure portal, go to **Azure Active Directory** > **Azure AD Connect**, and then select **Pass-through authentication**.
2. On the **Pass-through authentication** page, select the **Download** button. 
3. On the **Download agent** page, select **Accept terms and download**.
 
   The authentication agent starts to download. Install the secondary authentication agent on a domain-joined server.

   > [!NOTE]
   > The first agent is always installed on the Azure AD Connect server itself as part of the configuration changes made in the **User sign-in** section of the Azure AD Connect tool. Install any additional authentication agents on a separate server. We recommend that you have two or three additional authentication agents available.
 
4. Run the authentication agent installation. During the installation, you must enter the credentials of a Global Administrator account.<br />

   ![Screenshot that shows the Install button on the Microsoft Azure AD Connect Authentication Agent Package page](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image23.png)<br />
   ![Screenshot that shows the sign-in page](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image24.png)<br />
5. After the authentication agent is installed, you can return to the pass-through authentication agent health page to check the status of the additional agents.

At this point, federated authentication is still active and operational for your domains. To continue with the deployment, you must convert each domain from federated identity to managed identity so that pass-through authentication starts serving authentication requests for the domain.

You don't have to convert all domains at the same time. You might choose to start with a test domain on your production tenant or start with your domain that has the lowest number of users.

Complete the conversion by using the Azure AD PowerShell module:

1. In PowerShell, sign in to Azure AD by using a Global Administrator account.
2. To convert the first domain, run the following command:
 
   ``` PowerShell
   Set-MsolDomainAuthentication -Authentication Managed -DomainName <domain name>
   ```
 
3. In the Azure AD portal, select **Azure Active Directory** > **Azure AD Connect**.
4. After you convert all your federated domains, verify these settings:
   * **Federation** is set to **Disabled**.
   * **Seamless single sign-on** is set to **Enabled**.
   * **Pass-through authentication** is set to **Enabled**.<br />

   ![Screenshot that shows the settings in the User sign-in section in the Azure AD portal.](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image26.png)<br />

## Testing and next steps

Complete the following tasks to verify pass-through authentication and to finish the conversion process.

### Test pass-through authentication 

When your tenant used federated identity, users were redirected from the Azure AD sign-in page to your AD FS environment. Now that the tenant is configured to use pass-through authentication instead of federated authentication, users aren't redirected to AD FS. Instead, users sign in directly on the Azure AD sign-in page.

To test pass-through authentication:

1. Open Internet Explorer in InPrivate mode so that seamless SSO doesn't sign you in automatically.
2. Go to the Office 365 sign-in page ([https://portal.office.com](https://portal.office.com/)).
3. Enter a user UPN, and then select **Next**. Make sure that you enter the UPN of a hybrid user who was synced from your on-premises Active Directory instance, and who previously used federated authentication. A page on which you enter the username and password appears:

   ![Screenshot that shows the sign-in page in which you enter a username](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image27.png)

   ![Screenshot that shows the sign-in page in which you enter a password](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image28.png)

4. After you enter the password and select **Sign in**, you're redirected to the Office 365 portal.

   ![Screenshot that shows the Office 365 portal](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image29.png)

### Test seamless SSO

To test seamless SSO:

1. Sign in to a domain-joined machine that is connected to the corporate network.
2. In Internet Explorer or Chrome, go to one of the following URLs (replace "contoso" with your domain):

   * https:\/\/myapps.microsoft.com/contoso.com
   * https:\/\/myapps.microsoft.com/contoso.onmicrosoft.com

   The user is briefly redirected to the Azure AD sign-in page, which shows the message "Trying to sign you in." The user isn't prompted for a username or password.<br />

   ![Screenshot that shows the Azure AD sign-in page and message](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image30.png)<br />
3. The user is redirected and is successfully signed in to the access panel:

   > [!NOTE]
   > Seamless SSO works on Microsoft 365 services that support domain hint (for example, myapps.microsoft.com/contoso.com). Currently, the Microsoft 365 portal (portal.office.com) doesn???t support domain hints. Users are required to enter a UPN. After a UPN is entered, seamless SSO retrieves the Kerberos ticket on behalf of the user. The user is signed in without entering a password.

   > [!TIP]
   > Consider deploying [Azure AD hybrid join on Windows 10](../devices/overview.md) for an improved SSO experience.

### Remove the relying party trust

After you validate that all users and clients are successfully authenticating via Azure AD, it's safe to remove the Microsoft 365 relying party trust.

If you don't use AD FS for other purposes (that is, for other relying party trusts), it's safe to decommission AD FS at this point.

### Rollback

If you discover a major issue and can't resolve it quickly, you might choose to roll back the solution to federation.

Consult the federation design and deployment documentation for your specific deployment details. The process should involve these tasks:

* Convert managed domains to federated authentication by using the **Convert-MSOLDomainToFederated** cmdlet.
* If necessary, configure additional claims rules.

### Sync userPrincipalName updates

Historically, updates to the **UserPrincipalName** attribute, which uses the sync service from the on-premises environment, are blocked unless both of these conditions are true:

* The user is in a managed (non-federated) identity domain.
* The user hasn't been assigned a license.

To learn how to verify or turn on this feature, see [Sync userPrincipalName updates](./how-to-connect-syncservice-features.md).

## Roll over the seamless SSO Kerberos decryption key

It's important to frequently roll over the Kerberos decryption key of the AZUREADSSOACC computer account (which represents Azure AD). The AZUREADSSOACC computer account is created in your on-premises Active Directory forest. We highly recommend that you roll over the Kerberos decryption key at least every 30 days to align with the way that Active Directory domain members submit password changes. There's no associated device attached to the AZUREADSSOACC computer account object, so you must perform the rollover manually.

Initiate the rollover of the seamless SSO Kerberos decryption key on the on-premises server that's running Azure AD Connect.

For more information, see [How do I roll over the Kerberos decryption key of the AZUREADSSOACC computer account?](./how-to-connect-sso-faq.md).

## Monitoring and logging

Monitor the servers that run the authentication agents to maintain the solution availability. In addition to general server performance counters, the authentication agents expose performance objects that can help you understand authentication statistics and errors.

Authentication agents log operations to the Windows event logs that are located under Application and Service Logs\Microsoft\AzureAdConnect\AuthenticationAgent\Admin.

You can also turn on logging for troubleshooting.

For more information, see [Troubleshoot Azure Active Directory pass-through authentication](./tshoot-connect-pass-through-authentication.md).

## Next steps

* Learn about [Azure AD Connect design concepts](plan-connect-design-concepts.md).
* Choose the [right authentication](./choose-ad-authn.md).
* Learn about [supported topologies](plan-connect-design-concepts.md).
