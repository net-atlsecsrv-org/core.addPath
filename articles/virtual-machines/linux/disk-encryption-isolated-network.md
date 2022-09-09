---
title: Azure Disk Encryption on an isolated network
description: In this article, learn about troubleshooting tips for Microsoft Azure Disk Encryption on Linux VMs.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 02/27/2020

ms.custom: seodec18

---
# Azure Disk Encryption on an isolated network

When connectivity is restricted by a firewall, proxy requirement, or network security group (NSG) settings, the ability of the extension to perform needed tasks might be disrupted. This disruption can result in status messages such as "Extension status not available on the VM."

## Package management

Azure Disk Encryption depends on a number of components, which are typically installed as part of ADE enablement if not already present. When behind a firewall or otherwise isolated from the Internet, these packages must be pre-installed or available locally.

Here are the packages necessary for each distribution. For a full list of supported distros and volume types, see [supported VMs and operating systems](disk-encryption-overview.md#supported-vms-and-operating-systems).

- **Ubuntu 14.04, 16.04, 18.04**: lsscsi, psmisc, at, cryptsetup-bin, python-parted, python-six, procps
- **CentOS 7.2 - 7.7**: lsscsi, psmisc, lvm2, uuid, at, patch, cryptsetup, cryptsetup-reencrypt, pyparted, procps-ng, util-linux
- **CentOS 6.8**: lsscsi, psmisc, lvm2, uuid, at, cryptsetup-reencrypt, pyparted, python-six
- **RedHat 7.2 - 7.7**: lsscsi, psmisc, lvm2, uuid, at, patch, cryptsetup, cryptsetup-reencrypt, procps-ng, util-linux
- **RedHat 6.8**: lsscsi, psmisc, lvm2, uuid, at, patch, cryptsetup-reencrypt
- **openSUSE 42.3, SLES 12-SP4, 12-SP3**: lsscsi, cryptsetup

On Red Hat, when a proxy is required, you must make sure that the subscription-manager and yum are set up properly. For more information, see [How to troubleshoot subscription-manager and yum problems](https://access.redhat.com/solutions/189533).  

When packages are installed manually, they must also be manually upgraded as new versions are released.

## Network security groups
Any network security group settings that are applied must still allow the endpoint to meet the documented network configuration prerequisites for disk encryption.  See [Azure Disk Encryption: Networking requirements](disk-encryption-overview.md#networking-requirements)

## Azure Disk Encryption with Azure AD (previous version)

If using [Azure Disk Encryption with Azure AD (previous version)](disk-encryption-overview-aad.md), the [Azure Active Directory Library](../../active-directory/azuread-dev/active-directory-authentication-libraries.md) will need to be installed manually for all distros (in addition to the packages appropriate for the distro, as [listed above](#package-management)).

When encryption is being enabled with [Azure AD credentials](disk-encryption-linux-aad.md), the target VM must allow connectivity to both Azure Active Directory endpoints and Key Vault endpoints. Current Azure Active Directory authentication endpoints are maintained in sections 56 and 59 of the [Microsoft 365 URLs and IP address ranges](/microsoft-365/enterprise/urls-and-ip-address-ranges) documentation. Key Vault instructions are provided in the documentation on how to [Access Azure Key Vault behind a firewall](../../key-vault/general/access-behind-firewall.md).

### Azure Instance Metadata Service 

The virtual machine must be able to access the [Azure Instance Metadata service](instance-metadata-service.md) endpoint, which uses a well-known non-routable IP address (`169.254.169.254`) that can be accessed only from within the VM.  Proxy configurations that alter local HTTP traffic to this address (for example, adding an X-Forwarded-For header) are not supported.

## Next steps

- See more steps for [Azure disk encryption troubleshooting](disk-encryption-troubleshooting.md)
- [Azure data encryption at rest](../../security/fundamentals/encryption-atrest.md)
