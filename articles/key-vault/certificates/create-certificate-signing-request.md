---
title: Creating and merging CSR in Azure Key Vault 
description: Creating and merging CSR in Azure Key Vault 
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager

ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.date: 06/17/2020
ms.author: sebansal
---

# Creating and merging CSR in Key Vault

Azure Key Vault supports storing digital certificate issued by any Certificate Authority of your choice in your key vault. It supports creating the certificate signing request with private-public key pair which can be signed by any chosen Certificate Authority. It could be internal enterprise CA or external public CA. A certificate  signing request (also CSR or certification request) is a message that is sent by the user to a certificate authority (CA) in order to request issuance of a digital certificate.

For more general information about Certificates, see [Azure Key Vault Certificates](./about-certificates.md).

If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.

## Adding Certificate in Key Vault issued by partnered CA
Key Vault partners with following two Certificate Authorities to simplify the creation of certificates. 

|Provider|Certificate type|Configuration setup  
|--------------|----------------------|------------------|  
|DigiCert|Key Vault offers OV or EV SSL certificates with DigiCert| [Integration Guide](./how-to-integrate-certificate-authority.md)
|GlobalSign|Key Vault offers OV or EV SSL certificates with GlobalSign| [Integration Guide](https://support.globalsign.com/digital-certificates/digital-certificate-installation/generating-and-importing-certificate-microsoft-azure-key-vault)

## Adding Certificate in Key Vault issued by non-partnered CA

The following steps will help you create a certificate from certificate authorities that are not partnered with Key Vault (for example, GoDaddy is not a trusted key vault CA) 



# [Portal](#tab/azure-portal)

1.	To generate CSR for the CA of your choice, navigate to the Key vault you want to add the certificate.
2.	On the Key Vault properties pages, select **Certificates**.
3.	Select **Generate/Import** tab.
4.	On the **Create a certificate** screen choose the following values:
    - **Method of Certificate Creation:** Generate.
    - **Certificate Name:** ContosoManualCSRCertificate.
    - **Type of Certificate Authority (CA):** Certificate issued by a non-integrated CA
    - **Subject:** `"CN=www.contosoHRApp.com"`
    - Select the other values as desired. Click **Create**.

    ![Certificate properties](../media/certificates/create-csr-merge-csr/create-certificate.png)  


6.	You will see that certificate has now been added in Certificates list. Select this new certificate you had just created. The current state of the certificate would be ‘disabled’ as it hasn’t been issued by the CA yet.
7. Click on **Certificate Operation** tab and select **Download CSR**.

   ![Screenshot that highlights the Download CSR button.](../media/certificates/create-csr-merge-csr/download-csr.png)
 
8.	Take .csr file to the CA for the request to get signed.
9.	Once the request is signed by the CA, bring back the certificate file to **merge the Signed request** in the same Certificate Operation screen.

The certificate request has now been successfully merged.


# [PowerShell](#tab/azure-powershell)



1. First, **create the certificate policy**. Key Vault will not enroll or renew the certificate from the Issuer on behalf of the user as CA chosen in this scenario is not a supported one and hence the IssuerName is set to Unknown.

   ```azurepowershell
   $policy = New-AzKeyVaultCertificatePolicy -SubjectName "CN=www.contosoHRApp.com" -ValidityInMonths 1  -IssuerName Unknown
   ```
    
   > [!NOTE]
   > If you're using a Relative Distinguished Name (RDN) that has a comma (,) in the value, use single quotes and wrap the value that contains the special character in double quotes. Example: `$policy = New-AzKeyVaultCertificatePolicy -SubjectName 'OU="Docs,Contoso",DC=Contoso,CN=www.contosoHRApp.com' -ValidityInMonths 1  -IssuerName Unknown`. In this example, the `OU` value reads as **Docs, Contoso**. This format works for all values that contain a comma.

2. Create a **certificate signing request**

   ```azurepowershell
   $csr = Add-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -CertificatePolicy $policy
   $csr.CertificateSigningRequest
   ```

3. Getting the CSR **request signed by the CA**
The `$csr.CertificateSigningRequest` is the base4 encoded certificate signing request for the certificate. You can take this blob and dump into Issuer’s certificate request website. This step varies from CA to CA, the best way would be to look up your CA’s guidelines on how to execute this step. You can also use tools such as certreq or openssl to get the certificate request signed and complete the process of generating a certificate.


4. **Merging the signed request** in Key Vault
After the certificate request has been signed by the Issuer, you can bring back the signed certificate and merge it with the initial private-public key pair created in Azure Key Vault

    ```azurepowershell-interactive
    Import-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -FilePath C:\test\OutputCertificateFile.cer
    ```

    Certificate request has now been successfully merged.
---

> [!NOTE]
> If your RDN values have commas, you also can add them in the **Subject** field by surrounding the value in double quotes as shown in step 4.
> Example entry to "Subject": `DC=Contoso,OU="Docs,Contoso",CN=www.contosoHRApp.com`
> In this example, the RDN `OU` contains a value with a comma in the name. The resulting output for `OU` is **Docs, Contoso**.

## Adding more information to CSR

If you want to add more information when creating CSR, for instance - 
    - Country:
    - City / Locality:
    - State / Province:
    - Organization:
    - Organizational Unit:
You can add all that information when creating a CSR by defining that in subjectName.

Example
    ```SubjectName="CN = docs.microsoft.com, OU = Microsoft Corporation, O = Microsoft Corporation, L = Redmond, S = WA, C = US"
    ```

> [!NOTE]
> If you're requesting a DV cert with all those details in the CSR, the CA might reject the request because it might not be able to validate all the information in the request. If you're requesting an OV cert, it would be more appropriate to add all that information in the CSR.


## Troubleshoot

- To monitor or manage certificate request response, learn more [here](https://docs.microsoft.com/azure/key-vault/certificates/create-certificate-scenarios)

- **Error type 'The public key of the end-entity certificate in the specified X.509 certificate content does not match the public part of the specified private key. Please check if certificate is valid'**
    This error can occur if you are not merging the CSR with the same CSR request initiated. Each time a CSR is created, it creates a private key which has to be matched when merging the signed request.
    
- When CSR is merged, would it merge the entire chain?
    Yes, it will merge the entire chain, provided the user has brought back p7b file to merge.

- If the certificate issued is in 'disabled' status in the Azure portal, proceed to view the **Certificate Operation** to review the error message for that certificate.

For more information, see the [Certificate operations in the Key Vault REST API reference](/rest/api/keyvault). For information on establishing permissions, see [Vaults - Create or Update](/rest/api/keyvault/vaults/createorupdate) and [Vaults - Update Access Policy](/rest/api/keyvault/vaults/updateaccesspolicy).

- **Error type 'The subject name provided is not a valid X500 name'**
    This error can occur if you have included any "special characters" in the values of SubjectName. See Notes in Azure portal and PowerShell instructions respectively. 

---
## Next steps

- [Authentication, requests, and responses](../general/authentication-requests-and-responses.md)
- [Key Vault Developer's Guide](../general/developers-guide.md)
