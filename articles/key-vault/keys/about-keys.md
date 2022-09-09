---
title: About keys - Azure Key Vault
description: Overview of Azure Key Vault REST interface and developer details for keys.
services: key-vault
author: amitbapat
manager: msmbaldwin
tags: azure-resource-manager

ms.service: key-vault
ms.subservice: keys
ms.topic: overview
ms.date: 09/15/2020
ms.author: ambapat
---

# About keys

Azure Key Vault provides two types of resources to store and manage cryptographic keys:

|Resource type|Key protection methods|Data-plane endpoint base URL|
|--|--|--|
| **Vaults** | Software-protected<br/><br/>and<br/><br/>HSM-protected (with Premium SKU)</li></ul> | https://{vault-name}.vault.azure.net |
| **Managed HSM pools** | HSM-protected | https://{hsm-name}.managedhsm.azure.net |
||||

- **Vaults** - Vaults provide a low-cost, easy to deploy, multi-tenant, zone-resilient (where available), highly available key management solution suitable for most common cloud application scenarios.
- **Managed HSMs** - Managed HSM provides single-tenant, zone-resilient (where available), highly available HSMs to store and manage your cryptographic keys. Most suitable for applications and usage scenarios that handle high value keys. Also helps to meet most stringent security, compliance, and regulatory requirements. 

> [!NOTE]
> Vaults also allow you to store and manage several types of objects like secrets, certificates and storage account keys, in addition to cryptographic keys.

Cryptographic keys in Key Vault are represented as JSON Web Key [JWK] objects. The JavaScript Object Notation (JSON) and JavaScript Object Signing and Encryption (JOSE) specifications are:

-   [JSON Web Key (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key)  
-   [JSON Web Encryption (JWE)](http://tools.ietf.org/html/draft-ietf-jose-json-web-encryption)  
-   [JSON Web Algorithms (JWA)](http://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms)  
-   [JSON Web Signature (JWS)](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature) 

The base JWK/JWA specifications are also extended to enable key types unique to the Azure Key Vault and Managed HSM implementations. 

HSM-protected keys (also referred to as HSM-keys) are processed in an HSM (Hardware Security Module) and always remain HSM protection boundary. 

- Vaults use **FIPS 140-2 Level 2** validated HSMs to protect HSM-keys in shared HSM backend infrastructure. 
- Managed HSM pools uses **FIPS 140-2 Level 3** validated HSM modules to protect your keys. Each HSM pool is an isolated single-tenant instance with it's own [security domain](../managed-hsm/security-domain.md) providing complete cryptographic isolation from all other HSM pools sharing the same hardware infrastructure.

These keys are protected in single-tenant HSM-pools. You can import an RSA, EC, and symmetric key, in soft form or by exporting from a supported HSM device. You can also generate keys in HSM pools. When you import HSM keys using the method described in the [BYOK (bring your own key) specification](../keys/byok-specification.md), it enables secure transportation key material into Managed HSM pools. 

For more information on geographical boundaries, see [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/privacy/)

## Key types and protection methods

Key Vault supports RSA, EC and symmetric keys. 

### HSM-protected keys

|Key type|Vaults (Premium SKU only)|Managed HSM pools|
|--|--|--|--|
**EC-HSM**: Elliptic Curve key|FIPS 140-2 Level 2 HSM|FIPS 140-2 Level 3 HSM
**RSA-HSM**: RSA key|FIPS 140-2 Level 2 HSM|FIPS 140-2 Level 3 HSM
**oct-HSM**: Symmetric|Not supported|FIPS 140-2 Level 3 HSM
||||

### Software-protected keys

|Key type|Vaults|Managed HSM pools|
|--|--|--|--|
**RSA**: "Software-protected" RSA key|FIPS 140-2 Level 1|Not supported
**EC**: "Software-protected" Elliptic Curve key|FIPS 140-2 Level 1|Not supported
||||

Please see [Key types, algorithms, and operations](about-keys-details.md) for details about each key type, algorithms, operations, attributes and tags.

## Next steps
- [About Key Vault](../general/overview.md)
- [About Managed HSM](../managed-hsm/overview.md)
- [About secrets](../secrets/about-secrets.md)
- [About certificates](../certificates/about-certificates.md)
- [Key Vault REST API overview](../general/about-keys-secrets-certificates.md)
- [Authentication, requests, and responses](../general/authentication-requests-and-responses.md)
- [Key Vault Developer's Guide](../general/developers-guide.md)