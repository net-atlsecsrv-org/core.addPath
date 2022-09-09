---
title: Azure Attestation Basic Concepts
description: Basic concepts of Azure Attestation.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.custom: references_regions
---

# Basic Concepts

Below are some basic concepts related to Microsoft Azure Attestation.

## JSON Web Token (JWT)

[JSON Web Token](https://jwt.io/) (JWT) is an open standard [RFC7519](https://tools.ietf.org/html/rfc7519) method for securely transmitting information between parties as a JavaScript Object Notation (JSON) object. This information can be verified and trusted because it is digitally signed. JWTs can be signed using a secret or a public/private key pair.

## JSON Web Key (JWK)

[JSON Web Key](https://tools.ietf.org/html/rfc7517) (JWK) is a JSON data structure that represents a cryptographic key. This specification also defines a JWK Set JSON data structure that represents a set of JWKs.

## Attestation provider

Attestation provider belongs to Azure resource provider named Microsoft.Attestation. The resource provider is a service endpoint that provides Azure Attestation REST contract and is deployed using [Azure Resource Manager](../azure-resource-manager/management/overview.md). Each attestation provider honors a specific, discoverable policy. Attestation providers get created with a default policy for each attestation type (note that VBS enclave has no default policy). See [examples of an attestation policy](policy-examples.md) for more details on the default policy for SGX.

### Regional shared provider

Azure Attestation provides a regional shared provider in every available region. Customers can choose to use the regional shared provider for attestation, or create their own providers with custom policies. The shared providers are accessible by any Azure AD user and the policy associated with it cannot be altered.

| Region | Attest Uri | 
|--|--|
| East US | `https://sharedeus.eus.attest.azure.net` | 
| West US | `https://sharedwus.wus.attest.azure.net` | 
| UK South | `https://shareduks.uks.attest.azure.net` | 
| UK West| `https://sharedukw.ukw.attest.azure.net	` | 
| Canada East | `https://sharedcae.cae.attest.azure.net` | 
| Canada Central | `https://sharedcac.cac.attest.azure.net` | 
| North Europe | `https://sharedneu.neu.attest.azure.net` | 
| West Europe| `https://sharedweu.weu.attest.azure.net` | 
| US East 2 | `https://sharedeus2.eus2.attest.azure.net` | 
| Central US | `https://sharedcus.cus.attest.azure.net` | 

## Attestation request

Attestation request is a serialized JSON object sent by client application to attestation provider. 
The request object for SGX enclave has two properties: 
- “Quote” – The value of the “Quote” property is a string containing a Base64URL encoded representation of the attestation quote
- “EnclaveHeldData” – The value of the “EnclaveHeldData” property is a string containing a Base64URL encoded representation of the Enclave Held Data.

Azure Attestation will validate the provided “Quote”, and will then ensure that the SHA256 hash of the provided Enclave Held Data is expressed in the first 32 bytes of the reportData field in the quote. 

## Attestation policy

Attestation policy is used to process the attestation evidence and is configurable by customers. At the core of Azure Attestation is a policy engine, which processes claims constituting the evidence. Policies are used to determine whether Azure Attestation shall issue an attestation token based on evidence (or not) , and thereby endorse the Attester (or not). Accordingly, failure to pass all the policies will result in no JWT token being issued.

If the default policy in the attestation provider doesn’t meet the needs, customers will be able to create custom policies in any of the regions supported by Azure Attestation. Policy management is a key feature provided to customers by Azure Attestation. Policies will be attestation type specific and can be used to identify enclaves or add claims to the output token or modify claims in an output token. 

See [examples of an attestation policy](policy-examples.md) for policy samples.

## Benefits of policy signing

An attestation policy is what ultimately determines if an attestation token will be issued by Azure Attestation. Policy also determines the claims to be generated in the attestation token. It is thus of utmost importance that the policy evaluated by the service is in fact the policy written by the administrator and it has not been tampered or modified by external entities. 

Trust model defines the authorization model of attestation provider to define and update policy.  Two models are supported – one based on Azure AD authorization and one based on possession of customer-managed cryptographic keys (referred as isolated model).  Isolated model will enable Azure Attestation to ensure that the customer-submitted policy is not tampered.

In isolated model, administrator creates an attestation provider specifying a set of trusted signing X.509 certificates in a file. The administrator can then add a signed policy to the attestation provider. While processing the attestation request, Azure Attestation will validate the signature of the policy using the public key represented by either the “jwk” or the “x5c” parameter in the header.  Azure Attestation will also verify if public key in the request header is in the list of trusted signing certificates associated with the attestation provider. In this way, the relying party (Azure Attestation) can trust a policy signed using the X.509 certificates it knows about. 

See [examples of policy signer certificate](policy-signer-examples.md) for samples.

## Attestation token

Azure Attestation response will be a JSON string whose value contains JWT. Azure Attestation will package the claims and generates a signed JWT. The signing operation is performed using a self-signed certificate with subject name matching the AttestUri element of the attestation provider.

The Get OpenID Metadata API returns an OpenID Configuration response as specified by the [OpenID Connect Discovery protocol](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderConfig). The API retrieves metadata about the signing certificates in use by Azure Attestation.

Example of JWT generated for an SGX enclave:

```
{
  "alg": "RS256",
  "jku": "https://tradewinds.us.attest.azure.net/certs",
  "kid": <self signed certificate reference to perform signature verification of attestation token,
  "typ": "JWT"
}.{
  "aas-ehd": <input enclave held data>,
  "exp": 1568187398,
  "iat": 1568158598,
  "is-debuggable": false,
  "iss": "https://tradewinds.us.attest.azure.net",
  "maa-attestationcollateral": 
    {
      "qeidcertshash": <SHA256 value of QE Identity issuing certs>,
      "qeidcrlhash": <SHA256 value of QE Identity issuing certs CRL list>,
      "qeidhash": <SHA256 value of the QE Identity collateral>,
      "quotehash": <SHA256 value of the evaluated quote>, 
      "tcbinfocertshash": <SHA256 value of the TCB Info issuing certs>, 
      "tcbinfocrlhash": <SHA256 value of the TCB Info issuing certs CRL list>, 
      "tcbinfohash": <SHA256 value of the TCB Info collateral>
     },
  "maa-ehd": <input enclave held data>,
  "nbf": 1568158598,
  "product-id": 4639,
  "sgx-mrenclave": <SGX enclave mrenclave value>,
  "sgx-mrsigner": <SGX enclave msrigner value>,
  "svn": 0,
  "tee": "sgx"
  "x-ms-attestation-type": "sgx", 
  "x-ms-policy-hash": <>,
  "x-ms-sgx-collateral": 
    {
      "qeidcertshash": <SHA256 value of QE Identity issuing certs>,
      "qeidcrlhash": <SHA256 value of QE Identity issuing certs CRL list>,
      "qeidhash": <SHA256 value of the QE Identity collateral>,
      "quotehash": <SHA256 value of the evaluated quote>, 
      "tcbinfocertshash": <SHA256 value of the TCB Info issuing certs>, 
      "tcbinfocrlhash": <SHA256 value of the TCB Info issuing certs CRL list>, 
      "tcbinfohash": <SHA256 value of the TCB Info collateral>
     },
  "x-ms-sgx-ehd": <>, 
  "x-ms-sgx-is-debuggable": true,
  "x-ms-sgx-mrenclave": <SGX enclave mrenclave value>,
  "x-ms-sgx-mrsigner": <SGX enclave msrigner value>, 
  "x-ms-sgx-product-id": 1, 
  "x-ms-sgx-svn": 1,
  "x-ms-ver": "1.0"
}.[Signature]
```
Some of the claims used above are considered deprecated but are fully supported.  It is recommended that all future code and tooling use the non-deprecated claim names. See [claims issued by Azure Attestation](claim-sets.md) for more information.

## Encryption of data at rest

To safeguard customer data, Azure Attestation persists its data in Azure Storage. Azure storage provides encryption of data at rest as it's written into data centers, and decrypts it for customers to access it. This encryption occurs using a Microsoft managed encryption key. 

In addition to protecting data in Azure storage,  Azure Attestation also leverages Azure Disk Encryption (ADE) to encrypt service VMs. For Azure Attestation running in an enclave in Azure confidential computing environments, ADE extension is currently not supported. In such scenarios, to prevent data from being stored in-memory, page file is disabled. 

No customer data is being persisted on the Azure Attestation instance local hard disk drives.


## Next steps

- [How to author and sign an attestation policy](author-sign-policy.md)
- [Set up Azure Attestation using PowerShell](quickstart-powershell.md)
