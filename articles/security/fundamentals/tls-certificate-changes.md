---
title: Azure TLS Certificate Changes
description: Azure TLS Certificate Changes
services: security
author: msmbaldwin
tags: azure-resource-manager

ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.date: 11/10/2020
ms.author: mbaldwin

---

# Azure TLS certificate changes  

Microsoft is updating Azure services to use TLS certificates from a different set of Root Certificate Authorities (CAs). This change is being made because the current CA certificates do not comply with one of the CA/Browser Forum Baseline requirements.

## When will this change happen?

Existing Azure endpoints have been transitioning in a phased manner since August 13, 2020. All newly created Azure TLS/SSL endpoints contain updated certificates chaining up to the new Root CAs.

Service specific details:

- [Azure Active Directory](../../active-directory/index.yml) (Azure AD) services began this transition on July 7, 2020.
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub) and [DPS](../../iot-dps/index.yml) will remain on Baltimore CyberTrust Root CA but their intermediate CAs will change. [Click here for details](https://techcommunity.microsoft.com/t5/internet-of-things/azure-iot-tls-changes-are-coming-and-why-you-should-care/ba-p/1658456).
- [Azure Storage](../../storage/index.yml) will remain on Baltimore CyberTrust Root CA but their intermediate CAs will change. [Click here for details](https://techcommunity.microsoft.com/t5/azure-storage/azure-storage-tls-changes-are-coming-and-why-you-care/ba-p/1705518).
- [Azure Cache for Redis](../../azure-cache-for-redis/index.yml) will remain on Baltimore CyberTrust Root CA but their intermediate CAs will change. [Click here for details](../../azure-cache-for-redis/cache-whats-new.md).
- Azure Instance Metadata Service will remain on Baltimore CyberTrust Root CA but their intermediate CAs will change. [Click here for details](https://docs.microsoft.com/answers/questions/172717/action-required-for-attested-data-tls-with-azure-i.html).

> [!IMPORTANT]
> Customers may need to update their application(s) after this change to prevent connectivity failures when attempting to connect to Azure services.

## What is changing?

Today, most of the TLS certificates used by Azure services chain up to the following Root CA:

| Common name of the CA | Thumbprint (SHA1) |
|--|--|
| [Baltimore CyberTrust Root](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a50882c78db2852cae474 |

TLS certificates used by Azure services will chain up to one of the following Root CAs:

| Common name of the CA | Thumbprint (SHA1) |
|--|--|
| [DigiCert Global Root G2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt) | df3c24f9bfd666761b268073fe06d1cc8d4f82a4 |
| [DigiCert Global Root CA](https://cacerts.digicert.com/DigiCertGlobalRootCA.crt) | a8985d3a65e5e5c4b2d7d66d40c6dd2fb19c5436 |
| [Baltimore CyberTrust Root](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a50882c78db2852cae474 |
| [D-TRUST Root Class 3 CA 2 2009](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt) | 58e8abb0361533fb80f79b1b6d29d3ff8d5f00f0 |
| [Microsoft RSA Root Certificate Authority 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20RSA%20Root%20Certificate%20Authority%202017.crt) | 73a5e64a3bff8316ff0edccc618a906e4eae4d74 | 
| [Microsoft ECC Root Certificate Authority 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20ECC%20Root%20Certificate%20Authority%202017.crt) | 999a64c37ff47d9fab95f14769891460eec4c3c5 |

## When can I retire the old intermediate thumbprint?

The current CA certificates will *not* be revoked until February 15, 2021. After that date you can remove the old thumbprints from your code.

If this date changes, you will be notified of the new revocation date.

## Will this change affect me? 

We expect that **most Azure customers will not** be impacted.  However, your application may be impacted if it explicitly specifies a list of acceptable CAs. This practice is known as certificate pinning.

Here are some ways to detect if your application is impacted:

- Search your source code for the thumbprint, Common Name, and other cert properties of any of the Microsoft IT TLS CAs found [here](https://www.microsoft.com/pki/mscorp/cps/default.htm). If there is a match, then your application will be impacted. To resolve this problem, update the source code include the new CAs. As a best practice, ensure that CAs can be added or edited on short notice. Industry regulations require CA certificates to be replaced within seven days and hence customers relying on pinning need to react swiftly.

- If you have an application that integrates with Azure APIs or other Azure services and you are unsure if it uses certificate pinning, check with the application vendor.

- Different operating systems and language runtimes that communicate with Azure services may require additional steps to correctly build the certificate chain with these new roots:
    - **Linux**: Many distributions require you to add CAs to /etc/ssl/certs. For specific instructions, refer to the distribution’s documentation.
    - **Java**: Ensure that the Java key store contains the CAs listed above.
    - **Windows running in disconnected environments**: Systems running in disconnected environments will need to have the new roots added to the Trusted Root Certification Authorities store, and the intermediates added to the Intermediate Certification Authorities store.
    - **Android**: Check the documentation for your device and version of Android.
    - **Other hardware devices, especially IoT**: Contact the device manufacturer.

- If you have an environment where firewall rules are set to allow outbound calls to only specific Certificate Revocation List (CRL) download and/or Online Certificate Status Protocol (OCSP) verification locations. You will need to allow the following CRL and OCSP URLs:

    - http://crl3&#46;digicert&#46;com
    - http://crl4&#46;digicert&#46;com
    - http://ocsp&#46;digicert&#46;com
    - http://www&#46;d-trust&#46;net
    - http://root-c3-ca2-2009&#46;ocsp&#46;d-trust&#46;net
    - http://crl&#46;microsoft&#46;com
    - http://oneocsp&#46;microsoft&#46;com
    - http://ocsp&#46;msocsp&#46;com
    - http://www&#46;microsoft&#46;com/pkiops

## Next steps

If you have additional questions, contact us through [support](https://azure.microsoft.com/support/options/).
