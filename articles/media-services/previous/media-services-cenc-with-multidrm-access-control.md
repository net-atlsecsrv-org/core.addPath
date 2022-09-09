---
title: Design of a content protection system with access control using Azure Media Services | Microsoft Docs
description: Learn about how to license the Microsoft Smooth Streaming Client Porting Kit.
services: media-services
documentationcenter: ''
author: willzhan
manager: femila
editor: ''

ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: willzhan
ms.reviewer: kilroyh;yanmf;juliako
ms.custom: devx-track-csharp

---
# Design of a content protection system with access control using Azure Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## Overview

Designing and building a digital rights management (DRM) subsystem for an over-the-top (OTT) or online streaming solution is a complex task. Operators/online video providers typically outsource this task to specialized DRM service providers. The goal of this document is to present a reference design and implementation of an end-to-end DRM subsystem in an OTT or online streaming solution.

The targeted readers for this document are engineers who work in DRM subsystems of OTT or online streaming/multiscreen solutions or readers who are interested in DRM subsystems. The assumption is that readers are familiar with at least one of the DRM technologies on the market, such as PlayReady, Widevine, FairPlay, or Adobe Access.

In this discussion of DRM, we also include common encryption (CENC) with multi-DRM. A major trend in online streaming and the OTT industry is to use CENC with multi-native DRM on various client platforms. This trend is a shift from the previous one that used a single DRM and its client SDK for various client platforms. When you use CENC with multi-native DRM, both PlayReady and Widevine are encrypted per the [Common Encryption (ISO/IEC 23001-7 CENC)](https://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/) specification.

The benefits of CENC with multi-DRM are that it:

* Reduces encryption cost because a single encryption process is used to target different platforms with its native DRMs.
* Reduces the cost of managing encrypted assets because only a single copy of encrypted assets is needed.
* Eliminates DRM client licensing cost because the native DRM client is usually free on its native platform.

Microsoft is an active promoter of DASH and CENC together with some major industry players. Azure Media Services provides support for DASH and CENC. For recent announcements, see the following blogs:

*  [Announcing Google Widevine license delivery services in Azure Media Services](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/)
* [Azure Media Services adds Google Widevine packaging for delivering a multi-DRM stream](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)  

### Goals of the article

The goals of this article are to:

* Provide a reference design of a DRM subsystem that uses CENC with multi-DRM.
* Provide a reference implementation on an Azure/Media Services platform.
* Discuss some design and implementation topics.

In the article, the term "multi-DRM" covers the following products:

* Microsoft PlayReady
* Google Widevine
* Apple FairPlay 

The following table summarizes the native platform/native app and browsers supported by each DRM.

| **Client platform** | **Native DRM support** | **Browser/app** | **Streaming formats** |
| --- | --- | --- | --- |
| **Smart TVs, operator STBs, OTT STBs** |PlayReady primarily, and/or Widevine, and/or other |Linux, Opera, WebKit, other |Various formats |
| **Windows 10 devices (Windows PC, Windows tablets, Windows Phone, Xbox)** |PlayReady |Microsoft Edge/IE11/EME<br/><br/><br/>Universal Windows Platform |DASH (for HLS, PlayReady isn't supported)<br/><br/>DASH, Smooth Streaming (for HLS, PlayReady isn't supported) |
| **Android devices (phone, tablet, TV)** |Widevine |Chrome/EME |DASH, HLS |
| **iOS (iPhone, iPad), OS X clients and Apple TV** |FairPlay |Safari 8+/EME |HLS |

Considering the current state of deployment for each DRM, a service typically wants to implement two or three DRMs to make sure you address all the types of endpoints in the best way.

There is a tradeoff between the complexity of the service logic and the complexity on the client side to reach a certain level of user experience on the various clients.

To make your selection, keep in mind:

* PlayReady is natively implemented in every Windows device, on some Android devices, and available through software SDKs on virtually any platform.
* Widevine is natively implemented in every Android device, in Chrome, and in some other devices.
* FairPlay is available only on iOS and Mac OS clients or through iTunes.

There are two options for a typical multi-DRM:

* PlayReady and Widevine
* PlayReady, Widevine, and FairPlay

## A reference design
This section presents a reference design that is agnostic to the technologies used to implement it.

A DRM subsystem can contain the following components:

* Key management
* DRM packaging
* DRM license delivery
* Entitlement check
* Authentication/authorization
* Player
* Origin/content delivery network (CDN)

The following diagram illustrates the high-level interaction among the components in a DRM subsystem:

![DRM subsystem with CENC](./media/media-services-cenc-with-multidrm-access-control/media-services-generic-drm-subsystem-with-cenc.png)

The design has three basic layers:

* A back-office layer (black) is not exposed externally.
* A DMZ layer (dark blue) contains all the endpoints that face the public.
* A public internet layer (light blue) contains the CDN and players with traffic across the public internet.

There also should be a content management tool to control DRM protection, regardless of whether it's static or dynamic encryption. The inputs for DRM encryption include:

* MBR video content
* Content key
* License acquisition URLs

Here's the high-level flow during playback time:

* The user is authenticated.
* An authorization token is created for the user.
* DRM protected content (manifest) is downloaded to the player.
* The player submits a license acquisition request to license servers together with a key ID and an authorization token.

The following section discusses the design of key management.

| **ContentKey-to-asset** | **Scenario** |
| --- | --- |
| 1-to-1 |The simplest case. It provides the finest control. But this arrangement generally results in the highest license delivery cost. At minimum, one license request is required for each protected asset. |
| 1-to-many |You could use the same content key for multiple assets. For example, for all the assets in a logical group, such as a genre or the subset of a genre (or movie gene), you can use a single content key. |
| Many-to-1 |Multiple content keys are needed for each asset. <br/><br/>For example, if you need to apply dynamic CENC protection with multi-DRM for MPEG-DASH and dynamic AES-128 encryption for HLS, you need two separate content keys. Each content key needs its own ContentKeyType. (For the content key used for dynamic CENC protection, use ContentKeyType.CommonEncryption. For the content key used for dynamic AES-128 encryption, use ContentKeyType.EnvelopeEncryption.)<br/><br/>As another example, in CENC protection of DASH content, in theory, you can use one content key to protect the video stream and another content key to protect the audio stream. |
| Many-to-many |Combination of the previous two scenarios. One set of content keys is used for each of the multiple assets in the same asset group. |

Another important factor to consider is the use of persistent and nonpersistent licenses.

Why are these considerations important?

If you use a public cloud for license delivery, persistent and nonpersistent licenses have a direct impact on license delivery cost. The following two different design cases serve to illustrate:

* Monthly subscription: Use a persistent license and 1-to-many content key-to-asset mapping. For example, for all the kids' movies, we use a single content key for encryption. In this case:

    Total number of licenses requested for all kids' movies/device = 1

* Monthly subscription: Use a nonpersistent license and 1-to-1 mapping between content key and asset. In this case:

    Total number of licenses requested for all kids' movies/device = [number of movies watched] x [number of sessions]

The two different designs result in very different license request patterns. The different patterns result in different license delivery cost if license delivery service is provided by a public cloud such as Media Services.

## Map design to technology for implementation
Next, the generic design is mapped to technologies on the Azure/Media Services platform by specifying which technology to use for each building block.

The following table shows the mapping.

| **Building block** | **Technology** |
| --- | --- |
| **Player** |[Azure Media Player](https://azure.microsoft.com/services/media-services/media-player/) |
| **Identity provider (IDP)** |Azure Active Directory (Azure AD) |
| **Security token service (STS)** |Azure AD |
| **DRM protection workflow** |Media Services dynamic protection |
| **DRM license delivery** |* Media Services license delivery (PlayReady, Widevine, FairPlay) <br/>* Axinom license server <br/>* Custom PlayReady license server |
| **Origin** |Media Services streaming endpoint |
| **Key management** |Not needed for reference implementation |
| **Content management** |A C# console application |

In other words, both IDP and STS are used with Azure AD. The [Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/) is used for the player. Both Media Services and Media Player support DASH and CENC with multi-DRM.

The following diagram shows the overall structure and flow with the previous technology mapping:

![CENC on Media Services](./media/media-services-cenc-with-multidrm-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

To set up dynamic CENC encryption, the content management tool uses the following inputs:

* Open content
* Content key from key generation/management
* License acquisition URLs
* A list of information from Azure AD

Here's the output of the content management tool:

* ContentKeyAuthorizationPolicy contains the specification on how license delivery verifies a JSON Web Token (JWT) and DRM license specifications.
* AssetDeliveryPolicy contains specifications on streaming format, DRM protection, and license acquisition URLs.

Here's the flow during runtime:

* Upon user authentication, a JWT is generated.
* One of the claims contained in the JWT is a groups claim that contains the group object ID EntitledUserGroup. This claim is used to pass the entitlement check.
* The player downloads the client manifest of CENC-protected content and identifies the following:
   * Key ID.
   * The content is CENC protected.
   * License acquisition URLs.
* The player makes a license acquisition request based on the browser/DRM supported. In the license acquisition request, the key ID and the JWT are also submitted. The license delivery service verifies the JWT and the claims contained before it issues the needed license.

## Implementation
### Implementation procedures
Implementation includes the following steps:

1. Prepare test assets. Encode/package a test video to multi-bitrate fragmented MP4 in Media Services. This asset is *not* DRM protected. DRM protection is done by dynamic protection later.

2. Create a key ID and a content key (optionally from a key seed). In this instance, the key management system isn't needed because only a single key ID and content key are required for a couple of test assets.

3. Use the Media Services API to configure multi-DRM license delivery services for the test asset. If you use custom license servers by your company or your company's vendors instead of license services in Media Services, you can skip this step. You can specify license acquisition URLs in the step when you configure license delivery. The Media Services API is needed to specify some detailed configurations, such as authorization policy restriction and license response templates for different DRM license services. At this time, the Azure portal doesn't provide the needed UI for this configuration. For API-level information and sample code, see [Use PlayReady and/or Widevine dynamic common encryption](media-services-protect-with-playready-widevine.md).

4. Use the Media Services API to configure the asset delivery policy for the test asset. For API-level information and sample code, see [Use PlayReady and/or Widevine dynamic common encryption](media-services-protect-with-playready-widevine.md).

5. Create and configure an Azure AD tenant in Azure.

6. Create a few user accounts and groups in your Azure AD tenant. Create at least an "Entitled User" group, and add a user to this group. Users in this group pass the entitlement check in license acquisition. Users not in this group fail to pass the authentication check and can't acquire a license. Membership in this "Entitled User" group is a required groups claim in the JWT issued by Azure AD. You specify this claim requirement in the step when you configure multi-DRM license delivery services.

7. Create an ASP.NET MVC app to host your video player. This ASP.NET app is protected with user authentication against the Azure AD tenant. Proper claims are included in the access tokens obtained after user authentication. We recommend OpenID Connect API for this step. Install the following NuGet packages:

   * Install-Package Microsoft.Azure.ActiveDirectory.GraphClient
   * Install-Package Microsoft.Owin.Security.OpenIdConnect
   * Install-Package Microsoft.Owin.Security.Cookies
   * Install-Package Microsoft.Owin.Host.SystemWeb
   * Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

8. Create a player by using the [Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/). Use the [Azure Media Player ProtectionInfo API](https://amp.azure.net/libs/amp/latest/docs/) to specify which DRM technology to use on different DRM platforms.

9. The following table shows the test matrix.

    | **DRM** | **Browser** | **Result for entitled user** | **Result for unentitled user** |
    | --- | --- | --- | --- |
    | **PlayReady** |Microsoft Edge or Internet Explorer 11 on Windows 10 |Succeed |Fail |
    | **Widevine** |Chrome, Firefox, Opera |Succeed |Fail |
    | **FairPlay** |Safari on macOS      |Succeed |Fail |
    | **AES-128** |Most modern browsers  |Succeed |Fail |

For information on how to set up Azure AD for an ASP.NET MVC player app, see [Integrate an Azure Media Services OWIN MVC-based app with Azure Active Directory and restrict content key delivery based on JWT claims](http://gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

For more information, see [JWT token authentication in Azure Media Services and dynamic encryption](http://gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).  

For information on Azure AD:

* You can find developer information in the [Azure Active Directory developer's guide](../../active-directory/azuread-dev/v1-overview.md).
* You can find administrator information in [Administer your Azure AD tenant directory](../../active-directory/fundamentals/active-directory-whatis.md).

### Some issues in implementation
Use the following troubleshooting information for help with implementation issues.

* The issuer URL must end with "/". The audience must be the player application client ID. Also, add "/" at the end of the issuer URL.

    ```xml
    <add key="ida:audience" value="[Application Client ID GUID]" />
    <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />
    ```

    In the [JWT Decoder](http://jwt.calebb.net/), you see **aud** and **iss**, as shown in the JWT:

    ![JWT](./media/media-services-cenc-with-multidrm-access-control/media-services-1st-gotcha.png)

* Add permissions to the application in Azure AD on the **Configure** tab of the application. Permissions are required for each application, both local and deployed versions.

    ![Permissions](./media/media-services-cenc-with-multidrm-access-control/media-services-perms-to-other-apps.png)

* Use the correct issuer when you set up dynamic CENC protection.

    ```xml
    <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>
    ```

    The following doesn't work:

    ```xml
    <add key="ida:issuer" value="https://willzhanad.onmicrosoft.com/" />
    ```

    The GUID is the Azure AD tenant ID. The GUID can be found in the **Endpoints** pop-up menu in the Azure portal.

* Grant group membership claims privileges. Make sure the following is in the Azure AD application manifest file: 

    "groupMembershipClaims": "All"    (the default value is null)

* Set the proper TokenType when you create restriction requirements.

    ```csharp
    objTokenRestrictionTemplate.TokenType = TokenType.JWT;
    ```

    Because you add support for JWT (Azure AD) in addition to SWT (ACS), the default TokenType is TokenType.JWT. If you use SWT/ACS, you must set the token to TokenType.SWT.

## Additional topics for implementation
This section discusses some additional topics in design and implementation.

### HTTP or HTTPS?
The ASP.NET MVC player application must support the following:

* User authentication through Azure AD, which is under HTTPS.
* JWT exchange between the client and Azure AD, which is under HTTPS.
* DRM license acquisition by the client, which must be under HTTPS if license delivery is provided by Media Services. The PlayReady product suite doesn't mandate HTTPS for license delivery. If your PlayReady license server is outside Media Services, you can use either HTTP or HTTPS.

The ASP.NET player application uses HTTPS as a best practice, so Media Player is on a page under HTTPS. However, HTTP is preferred for streaming, so you need to consider the issue of mixed content.

* The browser doesn't allow mixed content. But plug-ins like Silverlight and the OSMF plug-in for smooth and DASH do allow it. Mixed content is a security concern because of the threat of the ability to inject malicious JavaScript, which can cause customer data to be at risk. Browsers block this capability by default. The only way to work around it is on the server (origin) side by allowing all domains (regardless of HTTPS or HTTP). This is probably not a good idea either.
* Avoid mixed content. Both the player application and Media Player should use HTTP or HTTPS. When playing mixed content, the silverlightSS tech requires clearing a mixed-content warning. The flashSS tech handles mixed content without a mixed-content warning.
* If your streaming endpoint was created before August 2014, it won't support HTTPS. In this case, create and use a new streaming endpoint for HTTPS.

In the reference implementation for DRM-protected contents, both the application and streaming are under HTTPS. For open contents, the player doesn't need authentication or a license, so you can use either HTTP or HTTPS.

### Azure Active Directory signing key rollover
Signing key rollover is an important point to take into consideration in your implementation. If you ignore it, the finished system eventually stops working completely, within six weeks at the most.

Azure AD uses industry standards to establish trust between itself and applications that use Azure AD. Specifically, Azure AD uses a signing key that consists of a public and private key pair. When Azure AD creates a security token that contains information about the user, it's signed by Azure AD with a private key before it's sent back to the application. To verify that the token is valid and originated from Azure AD, the application must validate the token's signature. The application uses the public key exposed by Azure AD that is contained in the tenant's federation metadata document. This public key, and the signing key from which it derives, is the same one used for all tenants in Azure AD.

For more information on Azure AD key rollover, see [Important information about signing key rollover in Azure AD](../../active-directory/develop/active-directory-signing-key-rollover.md).

Between the [public-private key pair](https://login.microsoftonline.com/common/discovery/keys/):

* The private key is used by Azure AD to generate a JWT.
* The public key is used by an application such as DRM license delivery services in Media Services to verify the JWT.

For security purposes, Azure AD rotates the certificate periodically (every six weeks). In the case of security breaches, the key rollover can occur any time. Therefore, the license delivery services in Media Services need to update the public key used as Azure AD rotates the key pair. Otherwise, token authentication in Media Services fails and no license is issued.

To set up this service, you set TokenRestrictionTemplate.OpenIdConnectDiscoveryDocument when you configure DRM license delivery services.

Here's the JWT flow:

* Azure AD issues the JWT with the current private key for an authenticated user.
* When a player sees a CENC with multi-DRM protected content, it first locates the JWT issued by Azure AD.
* The player sends a license acquisition request with the JWT to license delivery services in Media Services.
* The license delivery services in Media Services use the current/valid public key from Azure AD to verify the JWT before issuing licenses.

DRM license delivery services always check for the current/valid public key from Azure AD. The public key presented by Azure AD is the key used to verify a JWT issued by Azure AD.

What if the key rollover happens after Azure AD generates a JWT but before the JWT is sent by players to DRM license delivery services in Media Services for verification?

Because a key can be rolled over at any moment, more than one valid public key is always available in the federation metadata document. Media Services license delivery can use any of the keys specified in the document. Because one key might be rolled soon, another might be its replacement, and so forth.

### Where is the access token?
If you look at how a web app calls an API app under [Application identity with OAuth 2.0 client credentials grant](../../active-directory/azuread-dev/web-api.md), the authentication flow is as follows:

* A user signs in to Azure AD in the web application. For more information, see [Web browser to web application](../../active-directory/azuread-dev/web-app.md).
* The Azure AD authorization endpoint redirects the user agent back to the client application with an authorization code. The user agent returns the authorization code to the client application's redirect URI.
* The web application needs to acquire an access token so that it can authenticate to the web API and retrieve the desired resource. It makes a request to the Azure AD token endpoint and provides the credential, client ID, and web API's application ID URI. It presents the authorization code to prove that the user consented.
* Azure AD authenticates the application and returns a JWT access token that's used to call the web API.
* Over HTTPS, the web application uses the returned JWT access token to add the JWT string with a "Bearer" designation in the "Authorization" header of the request to the web API. The web API then validates the JWT. If validation is successful, it returns the desired resource.

In this application identity flow, the web API trusts that the web application authenticated the user. For this reason, this pattern is called a trusted subsystem. The [authorization flow diagram](../../active-directory/azuread-dev/v1-protocols-oauth-code.md) describes how authorization-code-grant flow works.

License acquisition with token restriction follows the same trusted subsystem pattern. The license delivery service in Media Services is the web API resource, or the "back-end resource" that a web application needs to access. So where is the access token?

The access token is obtained from Azure AD. After successful user authentication, an authorization code is returned. The authorization code is then used, together with the client ID and the app key, to exchange for the access token. The access token is used to access a "pointer" application that points to or represents the Media Services license delivery service.

To register and configure the pointer app in Azure AD, take the following steps:

1. In the Azure AD tenant:

   * Add an application (resource) with the sign-on URL https://[resource_name].azurewebsites.net/. 
   * Add an app ID with the URL https://[aad_tenant_name].onmicrosoft.com/[resource_name].

2. Add a new key for the resource app.

3. Update the app manifest file so that the groupMembershipClaims property has the value "groupMembershipClaims": "All".

4. In the Azure AD app that points to the player web app, in the section **Permissions to other applications**, add the resource app that was added in step 1. Under **Delegated permission**, select **Access [resource_name]**. This option gives the web app permission to create access tokens that access the resource app. Do this for both the local and deployed version of the web app if you develop with Visual Studio and the Azure web app.

The JWT issued by Azure AD is the access token used to access the pointer resource.

### What about live streaming?
The previous discussion focused on on-demand assets. What about live streaming?

You can use exactly the same design and implementation to protect live streaming in Media Services by treating the asset associated with a program as a VOD asset.

Specifically, to do live streaming in Media Services, you need to create a channel and then create a program under the channel. To create the program, you need to create an asset that contains the live archive for the program. To provide CENC with multi-DRM protection of the live content, apply the same setup/processing to the asset as if it were a VOD asset before you start the program.

### What about license servers outside Media Services?
Often, customers invested in a license server farm either in their own data center or one hosted by DRM service providers. With Media Services Content Protection, you can operate in hybrid mode. Contents can be hosted and dynamically protected in Media Services, while DRM licenses are delivered by servers outside Media Services. In this case, consider the following changes:

* STS needs to issue tokens that are acceptable and can be verified by the license server farm. For example, the Widevine license servers provided by Axinom require a specific JWT that contains an entitlement message. Therefore, you need to have an STS to issue such a JWT. For information on this type of implementation, go to the [Azure Documentation Center](https://azure.microsoft.com/documentation/) and see [Use Axinom to deliver Widevine licenses to Azure Media Services](media-services-axinom-integration.md).
* You no longer need to configure license delivery service (ContentKeyAuthorizationPolicy) in Media Services. You need to provide the license acquisition URLs (for PlayReady, Widevine, and FairPlay) when you configure AssetDeliveryPolicy to set up CENC with multi-DRM.

### What if I want to use a custom STS?
A customer might choose to use a custom STS to provide JWTs. Reasons include:

* The IDP used by the customer doesn't support STS. In this case, a custom STS might be an option.
* The customer might need more flexible or tighter control to integrate STS with the customer's subscriber billing system. For example, an MVPD operator might offer multiple OTT subscriber packages, such as premium, basic, and sports. The operator might want to match the claims in a token with a subscriber's package so that only the contents in a specific package are made available. In this case, a custom STS provides the needed flexibility and control.

When you use a custom STS, two changes must be made:

* When you configure license delivery service for an asset, you need to specify the security key used for verification by the custom STS instead of the current key from Azure AD. (More details follow.) 
* When a JTW token is generated, a security key is specified instead of the private key of the current X509 certificate in Azure AD.

There are two types of security keys:

* Symmetric key: The same key is used to generate and to verify a JWT.
* Asymmetric key: A public-private key pair in an X509 certificate is used with a private key to encrypt/generate a JWT and with the public key to verify the token.

> [!NOTE]
> If you use .NET Framework/C# as your development platform, the X509 certificate used for an asymmetric security key must have a key length of at least 2048. This is a requirement of the class System.IdentityModel.Tokens.X509AsymmetricSecurityKey in .NET Framework. Otherwise, the following exception is thrown:
> 
> IDX10630: The 'System.IdentityModel.Tokens.X509AsymmetricSecurityKey' for signing cannot be smaller than '2048' bits.

## The completed system and test
This section walks you through the following scenarios in the completed end-to-end system so that you can have a basic picture of the behavior before you get a sign-in account:

* If you need a non-integrated scenario:

    * For video assets hosted in Media Services that are either unprotected or DRM protected but without token authentication (issuing a license to whoever requested it), you can test it without signing in. Switch to HTTP if your video streaming is over HTTP.

* If you need an end-to-end integrated scenario:

    * For video assets under dynamic DRM protection in Media Services, with the token authentication and JWT generated by Azure AD, you need to sign in.

For the player web application and its sign-in, see [this website](https://openidconnectweb.azurewebsites.net/).

### User sign-in
To test the end-to-end integrated DRM system, you need to have an account created or added.

What account?

Although Azure originally allowed access only by Microsoft account users, access is now allowed by users from both systems. All Azure properties now trust Azure AD for authentication, and Azure AD authenticates organizational users. A federation relationship was created where Azure AD trusts the Microsoft account consumer identity system to authenticate consumer users. As a result, Azure AD can authenticate guest Microsoft accounts as well as native Azure AD accounts.

Because Azure AD trusts the Microsoft account domain, you can add any accounts from any of the following domains to the custom Azure AD tenant and use the account to sign in:

| **Domain name** | **Domain** |
| --- | --- |
| **Custom Azure AD tenant domain** |somename.onmicrosoft.com |
| **Corporate domain** |microsoft.com |
| **Microsoft account domain** |outlook.com, live.com, hotmail.com |

You can contact any of the authors to have an account created or added for you.

The following screenshots show different sign-in pages used by different domain accounts:

**Custom Azure AD tenant domain account**: The customized sign-in page of the custom Azure AD tenant domain.

![Custom Azure AD tenant domain account](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain1.png)

**Microsoft domain account with smart card**: The sign-in page customized by Microsoft corporate IT with two-factor authentication.

![Custom Azure AD tenant domain account](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain2.png)

**Microsoft account**: The sign-in page of the Microsoft account for consumers.

![Custom Azure AD tenant domain account](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain3.png)

### Use Encrypted Media Extensions for PlayReady
On a modern browser with Encrypted Media Extensions (EME) for PlayReady support, such as Internet Explorer 11 on Windows 8.1 or later and Microsoft Edge browser on Windows 10, PlayReady is the underlying DRM for EME.

![Use EME for PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready1.png)

The dark player area is because PlayReady protection prevents you from making a screen capture of protected video.

The following screenshot shows the player plug-ins and Microsoft Security Essentials (MSE)/EME support:

![Player plug-ins for PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready2.png)

EME in Microsoft Edge and Internet Explorer 11 on Windows 10 allows [PlayReady SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) to be invoked on Windows 10 devices that support it. PlayReady SL3000 unlocks the flow of enhanced premium content (4K, HDR) and new content delivery models (for enhanced content).

To focus on the Windows devices, PlayReady is the only DRM in the hardware available on Windows devices (PlayReady SL3000). A streaming service can use PlayReady through EME or through a Universal Windows Platform application and offer a higher video quality by using PlayReady SL3000 than another DRM. Typically, content up to 2K flows through Chrome or Firefox, and content up to 4K flows through Microsoft Edge/Internet Explorer 11 or a Universal Windows Platform application on the same device. The amount depends on service settings and implementation.

#### Use EME for Widevine
On a modern browser with EME/Widevine support, such as Chrome 41+ on Windows 10, Windows 8.1, Mac OSX Yosemite, and Chrome on Android 4.4.4, Google Widevine is the DRM behind EME.

![Use EME for Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine1.png)

Widevine doesn't prevent you from making a screen capture of protected video.

![Player plug-ins for Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine2.png)

### Unentitled users
If a user isn't a member of the "Entitled Users" group, the user doesn't pass the entitlement check. The multi-DRM license service then refuses to issue the requested license as shown. The detailed description is "License acquire failed," which is as designed.

![Unentitled users](./media/media-services-cenc-with-multidrm-access-control/media-services-unentitledusers.png)

### Run a custom security token service
If you run a custom STS, the JWT is issued by the custom STS by using either a symmetric or an asymmetric key.

The following screenshot shows a scenario that uses a symmetric key (using Chrome):

![Custom STS with a symmetric key](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts1.png)

The following screenshot shows a scenario that uses an asymmetric key via an X509 certificate (using a Microsoft modern browser):

![Custom STS with an asymmetric key](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts2.png)

In both of the previous cases, user authentication stays the same. It takes place through Azure AD. The only difference is that JWTs are issued by the custom STS instead of Azure AD. When you configure dynamic CENC protection, the license delivery service restriction specifies the type of JWT, either a symmetric or an asymmetric key.

## Summary

This document discussed CENC with multi-native DRM and access control via token authentication, its design, and its implementation by using Azure, Media Services, and Media Player.

* A reference design was presented that contains all the necessary components in a DRM/CENC subsystem.
* A reference implementation was presented on Azure, Media Services, and Media Player.
* Some topics directly involved in the design and implementation were also discussed.

## Additional notes

* Widevine is a service provided by Google Inc. and subject to the terms of service and Privacy Policy of Google, Inc.

## Media Services learning paths
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## Provide feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
 
