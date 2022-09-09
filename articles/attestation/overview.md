---
title: Azure Attestation overview
description: An overview of Microsoft Azure Attestation, a solution for attesting Trusted Execution Environments (TEEs)
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.custom: references_regions

---
# Microsoft Azure Attestation (preview)

Microsoft Azure Attestation (preview) is a unified solution for remotely verifying the trustworthiness of a platform and integrity of the binaries running inside it. The service supports attestation of the platforms backed by Trusted Platform Modules (TPMs) alongside the ability to attest to the state of Trusted Execution Environments (TEEs) such as [Intel® Software Guard Extensions](https://www.intel.com/content/www/us/en/architecture-and-technology/software-guard-extensions.html) (SGX) enclaves and [Virtualization-based Security](/windows-hardware/design/device-experiences/oem-vbs) (VBS) enclaves. 

Attestation is a process for demonstrating that software binaries were properly instantiated on a trusted platform. Remote relying parties can then gain confidence that only such intended software is running on trusted hardware. Azure Attestation is a unified customer-facing service and framework for attestation.

Azure Attestation enables cutting-edge security paradigms such as [Azure Confidential computing](../confidential-computing/overview.md) and Intelligent Edge protection. Customers have been requesting the ability to independently verify the location of a machine, the posture of a virtual machine (VM) on that machine, and the environment within which enclaves are running on that VM. Azure Attestation will empower these and many additional customer requests.

Azure Attestation receives evidence from compute entities, turns them into a set of claims, validates them against configurable policies, and produces cryptographic proofs for claims-based applications (for example, relying parties and auditing authorities).

## Use cases

Azure Attestation provides comprehensive attestation services for multiple environments and distinctive use cases.

### SGX attestation

SGX refers to hardware-grade isolation, which is supported on certain Intel CPUs models. SGX enables code to run in sanitized compartments known as SGX enclaves. Access and memory permissions are then managed by hardware to ensure a minimal attack surface with proper isolation.

Client applications can be designed to take advantage of SGX enclaves by delegating security-sensitive tasks to take place inside those enclaves. Such applications can then make use of Azure Attestation to routinely establish trust in the enclave and its ability to access sensitive data.

### Open Enclave
[Open Enclave](https://openenclave.io/sdk/) (OE) is a collection of libraries targeted at creating a single unified enclaving abstraction for developers to build TEE-based applications. It offers a universal secure app model that minimizes platform specificities. Microsoft views it as an essential stepping-stone toward democratizing hardware-based enclave technologies such as SGX and increasing their uptake on Azure.

OE standardizes specific requirements for verification of an enclave evidence. This qualifies OE as a highly fitting attestation consumer of Azure Attestation.

## Azure Attestation can run in a TEE

Azure Attestation is critical to Confidential Computing scenarios, as it performs the following actions:

- Verifies if the enclave evidence is valid.
- Evaluates the enclave evidence against a customer-defined policy.
- Manages and stores tenant-specific policies.
- Generates and signs a token that is used by relying parties to interact with the enclave.

Azure Attestation is built to run in two types of environments:
- Azure Attestation running in an SGX enabled TEE.
- Azure Attestation running in a non-TEE.

Azure Attestation customers have expressed a requirement for Microsoft to be operationally out of trusted computing base (TCB). This is to prevent Microsoft entities such as VM admins, host admins, and Microsoft developers from modifying attestation requests, policies, and Azure Attestation-issued tokens. Azure Attestation is also built to run in TEE, where features of Azure Attestation like quote validation, token generation, and token signing are moved into an SGX enclave.

## Why use Azure Attestation

Azure Attestation is the preferred choice for attesting TEEs as it offers the following benefits: 

- Unified framework for attesting multiple environments such as TPMs, SGX enclaves and VBS enclaves 
- Multi-tenant service which allows configuration of custom attestation providers and policies to restrict token generation
- Offers default providers which can attest with no configuration from users
- Protects its data while-in use with implementation in an SGX enclave
- Highly available service 

## Business Continuity and Disaster Recovery (BCDR) support

[Business Continuity and Disaster Recovery](../best-practices-availability-paired-regions.md) (BCDR) for Azure Attestation enables to mitigate service disruptions resulting from significant availability issues or disaster events in a region.

Clusters deployed in two regions will operate independently under normal circumstances. In the case of a fault or outage of one region, the following takes place:

- Azure Attestation BCDR will provide seamless failover in which customers do not need to take any extra step to recover
- The [Azure Traffic Manager](../traffic-manager/index.yml) for the region will detect the health probe is degraded and switch the endpoint to paired region
- Existing connections will not work and will receive internal server error or timeout issues
- All control plane operations will be blocked. Customers will not be able to create attestation providers and update policies in the primary region
- All data plane operations, including attest calls, will continue to work in primary region

## Next steps
- Learn about [Azure Attestation basic concepts](basic-concepts.md)
- [How to author and sign an attestation policy](author-sign-policy.md)
- [Set up Azure Attestation using PowerShell](quickstart-powershell.md)
