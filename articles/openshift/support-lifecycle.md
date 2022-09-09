---
title: Azure Red Hat OpenShift support lifecycle
description: Understand the support lifecycle and supported versions for Azure Red Hat OpenShift
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 08/11/2020
---

# Support lifecycle for Azure Red Hat OpenShift 4

Red Hat releases minor versions of Red Hat OpenShift Container Platform (OCP) roughly every three months. These releases include new features and improvements. Patch releases are more frequent (typically weekly) and are only intended for critical bug fixes within a minor version. These patch releases may include fixes for security vulnerabilities or major bugs.

Azure Red Hat OpenShift is built from specific releases of OCP. This article covers the versions of OCP that are supported for Azure Red Hat OpenShift and details about upgrades, deprecations, and support policy.

## Red Hat OpenShift versions

Red Hat OpenShift Container Platform uses semantic versioning. Semantic versioning uses different levels of version numbers to specify different levels of versioning. The following table illustrates the different parts of a semantic version number, in this case using the example version number 4.4.11.

|Major version (x)|Minor version (y)|Patch (z)|
|-|-|-|
|4|4|11|

Each number in the version indicates general compatibility with the previous version:

* **Major version**: No major version releases are planned at this time. Major versions change when incompatible API changes or backwards compatibility may be broken.
* **Minor version**: Released approximately every three months. Minor version upgrades can include feature additions, enhancements, deprecations, removals, bug fixes, security enhancements, and other improvements.
* **Patches**: Typically released each week, or as needed. Patch version upgrades can include bug fixes, security enhancements, and other improvements.

Customers should aim to run the latest minor release of the major version they're running. For example, if your production cluster is on 4.4, and 4.5 is the latest generally available minor version for the 4 series, you should upgrade to 4.5 as soon as you can.

### Upgrade channels

Upgrade channels are tied to a minor version of Red Hat OpenShift Container Platform (OCP). For instance, OCP 4.4 upgrade channels will never include an upgrade to a 4.5 release. Upgrade channels control only release selection and don't impact the version of the cluster.

Azure Red Hat OpenShift 4 supports stable channels only. For example: stable-4.4.

You can use the stable-4.5 channel to upgrade from a previous minor version of Azure Red Hat OpenShift. Clusters upgraded using fast, prerelease, and candidate channels will not be supported.

If you change to a channel that does not include your current release, an alert displays and no updates can be recommended, but you can safely change back to your original channel at any point.

## Red Hat OpenShift Container Platform version support policy

Azure Red Hat OpenShift supports two generally available (GA) minor versions of Red Hat OpenShift Container Platform:
* The latest GA minor version that is released in Azure Red Hat OpenShift (which we will refer to as N)
* One previous minor version (N-1)

If available in a stable upgrade channel, newer minor releases (N+1, N+2) available in upstream OCP may be supported as well.

Critical patch updates are applied to clusters automatically by Azure Red Hat OpenShift Site Reliability Engineers (SRE). Customers that wish to install patch updates in advance are free to do so.

For example, if Azure Red Hat OpenShift introduces 4.5.z today, support is provided for the following versions:

|New minor version|Supported version list|
|-|-|
|4.5.z|4.5.z, 4.4.z|

".z" is representative of patch versions. If available in a stable upgrade channel, customers may also upgrade to 4.6.z.

When a new minor version is introduced, the oldest minor version is deprecated and removed. For example, say the current supported version list is 4.5.z and 4.4.z. When Azure Red Hat OpenShift releases 4.6.z, the 4.4.z release will be removed and will be out of support within 30 days.

> [!NOTE]
> Please note that if customers are running an unsupported Red Hat OpenShift version, they may be asked to upgrade when requesting support for the cluster. Clusters running unsupported Red Hat OpenShift releases are not covered by the Azure Red Hat OpenShift SLA.

## Release and deprecation process

You can reference upcoming version releases and deprecations on the Azure Red Hat OpenShift Release Calendar.

For new minor versions of Red Hat OpenShift Container Platform:
* The Azure Red Hat OpenShift SRE team publishes a pre-announcement with the planned date of a new version release and respective old version deprecation on the [Azure Red Hat OpenShift Release notes](https://github.com/Azure/OpenShift/releases) at least 30 days prior to removal.
* The Azure Red Hat OpenShift SRE team publishes a service health notification available to all customers with Azure Red Hat OpenShift and portal access, and sends an email to the subscription administrators with the planned version removal dates.
* Customers have 30 days from version removal to upgrade to a supported minor version release to continue receiving support.

For new patch versions of Red Hat OpenShift Container Platform:
* Because of the urgent nature of patch versions, these can be introduced into the service by Azure Red Hat OpenShift SRE team as they become available.
* In general, the Azure Red Hat OpenShift SRE team does not perform broad communications for the installation of new patch versions. However, the team constantly monitors and validates available CVE patches to support them in a timely manner. If customer action is required, the team will notify customers about the upgrade.

## Supported versions policy exceptions

The Azure Red Hat OpenShift SRE team reserves the right to add or remove new/existing versions, or delay upcoming minor release versions, that have been identified to have one or more critical production impacting bugs or security issues without advance notice.

Specific patch releases may be skipped, or rollout may be accelerated depending on the severity of the bug or security issue.

## Azure portal and CLI versions

When you deploy an Azure Red Hat OpenShift cluster in the portal or with the Azure CLI, the cluster is defaulted to the latest (N) minor version and latest critical patch. For example, if Azure Red Hat OpenShift supports 4.5.z and 4.4.z, the default version for new installations is 4.5.z. Customers that wish to use the latest upstream OCP minor version (N+1, N+2) can upgrade their cluster at any time to any release available in the stable upgrade channels.

## Azure Red Hat OpenShift release calendar

See the following guide for the [past Red Hat OpenShift Container Platform (upstream) release history](https://access.redhat.com/support/policy/updates/openshift/#dates).

|OCP Version|Upstream Release|Azure Red Hat OpenShift General Availability|End of Life|
|-|-|-|-|
|4.3|February 2020|May 2020|August 2020|
|4.4|May 2020|August 2020|4.6 GA|
|4.5|July 2020|October 2020|4.7 GA
|4.6|*Early Q4, 2020|*Late Q4, 2020|4.8 GA|

\* _Pending upstream release date confirmation._

## FAQ

**What happens when a user upgrades an OpenShift cluster with a minor version that is not supported?**

If you are on the N-2 version or older, it means you are outside of support and will be asked to upgrade. When your upgrade from version N-2 to N-1 succeeds, you are back within our support policies. For example:
* If the oldest supported Azure Red Hat OpenShift version is 4.4.z and you are on 4.3.z or older, you are outside of support.
* When the upgrade from 4.3.z to 4.4.z or higher succeeds, you are back within our support policies.

Reverting your cluster to a previous version, or a rollback, isn't supported. Only upgrading to a newer version is supported.

**What does "Outside of Support" mean?**

"Outside of Support" means that the version you are running is outside of the supported versions list, and you may be asked to upgrade the cluster to a supported version when requesting support, unless you are within the 30-day grace period after version deprecation. Additionally, Azure Red Hat OpenShift does not make any runtime or SLA guarantees for clusters outside of the supported versions list at the end of the 30-day grace period.
