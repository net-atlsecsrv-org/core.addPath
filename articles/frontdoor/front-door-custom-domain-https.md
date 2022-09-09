---
title: Tutorial - Configure HTTPS on a custom domain for Azure Front Door Service | Microsoft Docs
description: In this tutorial, you learn how to enable and disable HTTPS on your Azure Front Door Service configuration for a custom domain.
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/05/2018
ms.author: sharadag
# As a website owner, I want to enable HTTPS on the custom domain in my Front Door so that my users can use my custom domain to access their content securely.

---

# Tutorial: Configure HTTPS on a Front Door custom domain

This tutorial shows how to enable the HTTPS protocol for a custom domain that's associated with your Front Door under the frontend hosts section. By using the HTTPS protocol on your custom domain (for example, https:\//www.contoso.com), you ensure that your sensitive data is delivered securely via TLS/SSL encryption when it is sent across the internet. When your web browser is connected to a web site via HTTPS, it validates the web site's security certificate and verifies it's issued by a legitimate certificate authority. This process provides security and protects your web applications from attacks.

Azure Front Door Service supports HTTPS on a Front Door default hostname, by default. For example, if you create a Front Door (such as https:\//contoso.azurefd.net), HTTPS is automatically enabled for requests made to https://contoso.azurefd.net. However, once you onboard the custom domain 'www.contoso.com' you will need to additionally enable HTTPS for this frontend host.   

Some of the key attributes of the custom HTTPS feature are:

- No additional cost: There are no costs for certificate acquisition or renewal and no additional cost for HTTPS traffic. 

- Simple enablement: One-click provisioning is available from the [Azure portal](https://portal.azure.com). You can also use REST API or other developer tools to enable the feature.

- Complete certificate management is available: All certificate procurement and management is handled for you. Certificates are automatically provisioned and renewed prior to expiration, which removes the risks of service interruption due to a certificate expiring.

In this tutorial, you learn how to:
> [!div class="checklist"]
> - Enable the HTTPS protocol on your custom domain.
> - Use an AFD-managed certificate 
> - Use your own certificate, that is, a custom SSL certificate
> - Validate the domain
> - Disable the HTTPS protocol on your custom domain


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## Prerequisites

Before you can complete the steps in this tutorial, you must first create a Front Door and with at least one custom domain onboarded. For more information, see [Tutorial: Add a custom domain to your Front Door](front-door-custom-domain.md).

## SSL certificates

To enable the HTTPS protocol for securely delivering content on a Front Door custom domain, you must use an SSL certificate. You can choose to use a certificate that is managed by Azure Front Door Service or use your own certificate.


### Option 1 (default): Use a certificate managed by Front Door

When you use a certificate managed by Azure Front Door Service, the HTTPS feature can be turned on with just a few clicks. Azure Front Door Service completely handles certificate management tasks such as procurement and renewal. After you enable the feature, the process starts immediately. If the custom domain is already mapped to the Front Door's default frontend host (`{hostname}.azurefd.net`), no further action is required. Front Door will process the steps and complete your request automatically. However, if your custom domain is mapped elsewhere, you must use email to validate your domain ownership.

To enable HTTPS on a custom domain, follow these steps:

1. In the [Azure portal](https://portal.azure.com), browse to your **Front Door** profile.

2. In the list of frontend hosts, select the custom domain you want to enable HTTPS for containing your custom domain.

3. Under the section **Custom domain HTTPS**, click **Enabled**, and select **Front Door managed** as the certificate source.

4. Click Save.

5. Proceed to [Validate the domain](#validate-the-domain).


### Option 2: Use your own certificate

You can use your own certificate to enable the HTTPS feature. This process is done through an integration with Azure Key Vault, which allows you to store your certificates securely. Azure Front Door Service uses this secure mechanism to get your certificate and it requires a few additional steps. When you create your SSL certificate, you must create it with an allowed certificate authority (CA). Otherwise, if you use a non-allowed CA, your request will be rejected. For a list of allowed CAs, see [Allowed certificate authorities for enabling custom HTTPS on Azure Front Door Service](front-door-troubleshoot-allowed-ca.md).

#### Prepare your Azure Key vault account and certificate
 
1. Azure Key Vault: You must have a running Azure Key Vault account under the same subscription as your Front Door that you want to enable custom HTTPS. Create an Azure Key Vault account if you don't have one.

> [!WARNING]
> Azure Front Door Service currently only supports Key Vault accounts in the same subscription as the Front Door configuration. Choosing a Key Vault under a different subscription than your Front Door will result in a failure.

2. Azure Key Vault certificates: If you already have a certificate, you can upload it directly to your Azure Key Vault account or you can create a new certificate directly through Azure Key Vault from one of the partner CAs that Azure Key Vault integrates with. Upload your certificate as a **certificate** object, rather than a **secret**.

> [!IMPORTANT]
> You must upload the certificate in PFX format **without** password protection.

#### Register Azure Front Door Service

Register the service principal for Azure Front Door Service as an app in your Azure Active Directory via PowerShell.

> [!NOTE]
> This action need only be performed **once** per tenant.

1. If needed, install [Azure PowerShell](/powershell/azure/install-az-ps) in PowerShell on your local machine.

2. In PowerShell, run the following command:

     `New-AzADServicePrincipal -ApplicationId "ad0e1c7e-6d38-4ba4-9efd-0bc77ba9f037"`              

#### Grant Azure Front Door Service access to your key vault
 
Grant Azure Front Door Service permission to access the  certificates in your Azure Key Vault account.

1. In your key vault account, under SETTINGS, select **Access policies**, then select **Add new** to create a new policy.

2. In **Select principal**, search for **ad0e1c7e-6d38-4ba4-9efd-0bc77ba9f037**, and choose **Microsoft.Azure.Frontdoor**. Click **Select**.

3. In **Secret permissions**, select **Get** to allow Front Door to retrieve the certificate.

4. In **Certificate permissions**, select **Get** to allow Front Door to retrieve the certificate.

5. Select **OK**. 

    Azure Front Door Service can now access this Key Vault and the certificates that are stored in this Key Vault.
 
#### Select the certificate for Azure Front Door Service to deploy
 
1. Return to your Front Door in the portal. 

2. In the list of custom domains, select the custom domain for which you want to enable HTTPS.

    The **Custom domain** page appears.

3. Under Certificate management type, select **Use my own certificate**. 

4. Azure Front Door Service requires that the subscription of the Key Vault account is the same as for your Front Door. Select a key vault, certificate (secret), and certificate version.

    Azure Front Door Service lists the following information: 
    - The key vault accounts for your subscription ID. 
    - The certificates (secrets) under the selected key vault. 
    - The available certificate versions. 
 
5. When you use your own certificate, domain validation is not required. Proceed to [Wait for propagation](#wait-for-propagation).

## Validate the domain

If you already have a custom domain in use that is mapped to your custom endpoint with a CNAME record or you're using your own certificate, proceed to  
[Custom domain is mapped to your Front Door](#custom-domain-is-mapped-to-your-front-door-by-a-cname-record). Otherwise, if the CNAME record entry for your domain no longer exists or it contains the afdverify subdomain, proceed to [Custom domain is not mapped to your Front Door](#custom-domain-is-not-mapped-to-your-front-door).

### Custom domain is mapped to your Front Door by a CNAME record

When you added a custom domain to your Front Door's frontend hosts, you created a CNAME record in the DNS table of your domain registrar to map it to your Front Door's default .azurefd.net hostname. If that CNAME record still exists and does not contain the afdverify subdomain, the DigiCert Certificate Authority uses it to automatically validate ownership of your custom domain. 

If you're using your own certificate, domain validation is not required.

Your CNAME record should be in the following format, where *Name* is your custom domain name and *Value* is your Front Door's default .azurefd.net hostname:

| Name            | Type  | Value                 |
|-----------------|-------|-----------------------|
| <www.contoso.com> | CNAME | contoso.azurefd.net |

For more information about CNAME records, see [Create the CNAME DNS record](https://docs.microsoft.com/azure/cdn/cdn-map-content-to-custom-domain).

If your CNAME record is in the correct format, DigiCert automatically verifies your custom domain name and creates a dedicated certificate for your domain name. DigitCert won't send you a verification email and you won't need to approve your request. The certificate is valid for one year and will be autorenewed before it expires. Proceed to [Wait for propagation](#wait-for-propagation). 

Automatic validation typically takes a few mins. If you don't see your domain validated within an hour, open a support ticket.

>[!NOTE]
>If you have a Certificate Authority Authorization (CAA) record with your DNS provider, it must include DigiCert as a valid CA. A CAA record allows domain owners to specify with their DNS providers which CAs are authorized to issue certificates for their domain. If a CA receives an order for a certificate for a domain that has a CAA record and that CA is not listed as an authorized issuer, it is prohibited from issuing the certificate to that domain or subdomain. For  information about managing CAA records, see [Manage CAA records](https://support.dnsimple.com/articles/manage-caa-record/). For a CAA record tool, see [CAA Record Helper](https://sslmate.com/caa/).

### Custom domain is not mapped to your Front Door

If the CNAME record entry for your endpoint no longer exists or it contains the afdverify subdomain, follow the rest of the instructions in this step.

After you enable HTTPS on your custom domain, the DigiCert CA validates ownership of your domain by contacting its registrant, according to the domain's [WHOIS](http://whois.domaintools.com/) registrant information. Contact is made via the email address (by default) or the phone number listed in the WHOIS registration. You must complete domain validation before HTTPS will be active on your custom domain. You have six business days to approve the domain. Requests that are not approved within six business days are automatically canceled. 

![WHOIS record](./media/front-door-custom-domain-https/whois-record.png)

DigiCert also sends a verification email to additional email addresses. If the WHOIS registrant information is private, verify that you can approve directly from one of the following addresses:

admin@&lt;your-domain-name.com&gt;  
administrator@&lt;your-domain-name.com&gt;  
webmaster@&lt;your-domain-name.com&gt;  
hostmaster@&lt;your-domain-name.com&gt;  
postmaster@&lt;your-domain-name.com&gt;  

You should receive an email in a few minutes, similar to the following example, asking you to approve the request. If you are using a spam filter, add admin@digicert.com to its allow list. If you don't receive an email within 24 hours, contact Microsoft support.

When you click on the approval link, you are directed to an online approval form. Follow the instructions on the form; you have two verification options:

- You can approve all future orders placed through the same account for the same root domain; for example, contoso.com. This approach is recommended if you plan to add additional custom domains for the same root domain.

- You can approve just the specific host name used in this request. Additional approval is required for subsequent requests.

After approval, DigiCert completes the certificate creation for your custom domain name. The certificate is valid for one year and will be autorenewed before it's expired.

## Wait for propagation

After the domain name is validated, it can take up to 6-8 hours for the custom domain HTTPS feature to be activated. When the process is complete, the custom HTTPS status in the Azure portal is set to **Enabled** and the four operation steps in the custom domain dialog are marked as complete. Your custom domain is now ready to use HTTPS.

### Operation progress

The following table shows the operation progress that occurs when you enable HTTPS. After you enable HTTPS, four operation steps appear in the custom domain dialog. As each step becomes active, additional substep details appear under the step as it progresses. Not all of these substeps will occur. After a step successfully completes, a green check mark appears next to it. 

| Operation step | Operation substep details | 
| --- | --- |
| 1 Submitting request | Submitting request |
| | Your HTTPS request is being submitted. |
| | Your HTTPS request has been submitted successfully. |
| 2 Domain validation | Domain is automatically validated if it is CNAME mapped to the default .azurefd.net frontend host of your Front Door. Otherwise, a verification request will be sent to the email listed in your domain's registration record (WHOIS registrant). Verify the domain as soon as possible. |
| | Your domain ownership has been successfully validated. |
| | Domain ownership validation request expired (customer likely didn't respond within 6 days). HTTPS will not be enabled on your domain. * |
| | Domain ownership validation request was rejected by the customer. HTTPS will not be enabled on your domain. * |
| 3 Certificate provisioning | The certificate authority is currently issuing the certificate needed to enable HTTPS on your domain. |
| | The certificate has been issued and is currently being deployed for your Front Door. This process could take up to 1 hour. |
| | The certificate has been successfully deployed for your Front Door. |
| 4 Complete | HTTPS has been successfully enabled on your domain. |

\* This message doesn't appear unless an error has occurred. 

If an error occurs before the request is submitted, the following error message is displayed:

<code>
We encountered an unexpected error while processing your HTTPS request. Please try again and contact support if the issue persists.
</code>



## Clean up resources - disable HTTPS

In the preceding steps, you enabled the HTTPS protocol on your custom domain. If you no longer want to use your custom domain with HTTPS, you can disable HTTPS by performing theses steps:

### Disable the HTTPS feature 

1. In the [Azure portal](https://portal.azure.com), browse to your **Azure Front Door Service** configuration.

2. In the list of frontend hosts, click the custom domain for which you want to disable HTTPS.

3. Click **Disabled** to disable HTTPS, then click **Save**.

### Wait for propagation

After the custom domain HTTPS feature is disabled, it can take up to 6-8 hours for it to take effect. When the process is complete, the custom HTTPS status in the Azure portal is set to **Disabled** and the three operation steps in the custom domain dialog are marked as complete. Your custom domain can no longer use HTTPS.

#### Operation progress

The following table shows the operation progress that occurs when you disable HTTPS. After you disable HTTPS, three operation steps appear in the Custom domain dialog. As each step becomes active, additional details appear under the step. After a step successfully completes, a green check mark appears next to it. 

| Operation progress | Operation details | 
| --- | --- |
| 1 Submitting request | Submitting your request |
| 2 Certificate deprovisioning | Deleting certificate |
| 3 Complete | Certificate deleted |

## Frequently asked questions

1. *Who is the certificate provider and what type of certificate is used?*

    A dedicated/single certificate, provided by Digicert, is used for your custom domain. 

2. *Do you use IP-based or SNI TLS/SSL?*

    Azure Front Door Service uses SNI TLS/SSL.

3. *What if I don't receive the domain verification email from DigiCert?*

    If you have a CNAME entry for your custom domain that points directly to your endpoint hostname (and you are not using the afdverify subdomain name), you won't receive a domain verification email. Validation occurs automatically. Otherwise, if you don't have a CNAME entry and you haven't received an email within 24 hours, contact Microsoft support.

4. *Is using a SAN certificate less secure than a dedicated certificate?*
	
	A SAN certificate follows the same encryption and security standards as a dedicated certificate. All issued SSL certificates use SHA-256 for enhanced server security.

5. *Do I need a Certificate Authority Authorization record with my DNS provider?*

    No, a Certificate Authority Authorization record is not currently required. However, if you do have one, it must include DigiCert as a valid CA.


## Next steps

- Learn how to [create a Front Door](quickstart-create-front-door.md).
- Learn [how Front Door works](front-door-routing-architecture.md).
