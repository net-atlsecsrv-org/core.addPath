---
title: Immutable blob storage - Azure Storage
description: Azure Storage offers WORM (Write Once, Read Many) support for Blob (object) storage that enables users to store data in a non-erasable, non-modifiable state for a specified interval.
services: storage
author: tamram

ms.service: storage
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: tamram
ms.reviewer: hux
ms.subservice: blobs
---

# Store business-critical blob data with immutable storage

Immutable storage for Azure Blob storage enables users to store business-critical data objects in a WORM (Write Once, Read Many) state. This state makes the data non-erasable and non-modifiable for a user-specified interval. For the duration of the retention interval, blobs can be created and read, but cannot be modified or deleted. Immutable storage is available for general-purpose v2 and Blob storage accounts in all Azure regions.

For information about how to set and clear legal holds or create a time-based retention policy using the Azure portal, PowerShell, or Azure CLI, see [Set and manage immutability policies for Blob storage](storage-blob-immutability-policies-manage.md).

## About immutable Blob storage

Immutable storage helps healthcare organization, financial institutions, and related industries&mdash;particularly broker-dealer organizations&mdash;to store data securely. Immutable storage can also be leveraged in any scenario to protect critical data against modification or deletion.

Typical applications include:

- **Regulatory compliance**: Immutable storage for Azure Blob storage helps organizations address SEC 17a-4(f), CFTC 1.31(d), FINRA, and other regulations. A technical whitepaper by Cohasset Associates details how Immutable storage addresses these regulatory requirements is downloadable via the [Microsoft Service Trust Portal](https://aka.ms/AzureWormStorage). The [Azure Trust Center](https://www.microsoft.com/trustcenter/compliance/compliance-overview) contains detailed information about our compliance certifications.

- **Secure document retention**: Immutable storage for Azure Blob storage ensures that data can't be modified or deleted by any user, including users with account administrative privileges.

- **Legal hold**: Immutable storage for Azure Blob storage enables users to store sensitive information that is critical to litigation or business use in a tamper-proof state for the desired duration until the hold is removed. This feature is not limited only to legal use cases but can also be thought of as an event-based hold or an enterprise lock, where the need to protect data based on event triggers or corporate policy is required.

Immutable storage supports the following features:

- **[Time-based retention policy support](#time-based-retention-policies)**: Users can set policies to store data for a specified interval. When a time-based retention policy is set, blobs can be created and read, but not modified or deleted. After the retention period has expired, blobs can be deleted but not overwritten.

- **[Legal hold policy support](#legal-holds)**: If the retention interval is not known, users can set legal holds to store immutable data until the legal hold is cleared.  When a legal hold policy is set, blobs can be created and read, but not modified or deleted. Each legal hold is associated with a user-defined alphanumeric tag (such as a case ID, event name, etc.) that is used as an identifier string. 

- **Support for all blob tiers**: WORM policies are independent of the Azure Blob storage tier and apply to all the tiers: hot, cool, and archive. Users can transition data to the most cost-optimized tier for their workloads while maintaining data immutability.

- **Container-level configuration**: Users can configure time-based retention policies and legal hold tags at the container level. By using simple container-level settings, users can create and lock time-based retention policies, extend retention intervals, set and clear legal holds, and more. These policies apply to all the blobs in the container, both existing and new.

- **Audit logging support**: Each container includes a policy audit log. It shows up to seven time-based retention commands for locked time-based retention policies and contains the user ID, command type, time stamps, and retention interval. For legal holds, the log contains the user ID, command type, time stamps, and legal hold tags. This log is retained for the lifetime of the policy, in accordance with the SEC 17a-4(f) regulatory guidelines. The [Azure Activity Log](../../azure-monitor/platform/activity-logs-overview.md) shows a more comprehensive log of all the control plane activities; while enabling [Azure Diagnostic Logs](../../azure-monitor/platform/resource-logs-overview.md) retains and shows data plane operations. It is the user's responsibility to store those logs persistently, as might be required for regulatory or other purposes.

## How it works

Immutable storage for Azure Blob storage supports two types of WORM or immutable policies: time-based retention and legal holds. When a time-based retention policy or legal hold is applied on a container, all existing blobs move into an immutable WORM state in less than 30 seconds. All new blobs that are uploaded to that container will also move into the immutable state. Once all blobs have moved into the immutable state, the immutable policy is confirmed and all overwrite or delete operations for existing and new objects in the immutable container are not allowed.

Container and storage account deletion are not permitted if there are any blobs in the container or storage account that are protected by an immutable policy. The container deletion operation will fail if at least one blob exists with a locked time-based retention policy or a legal hold. The storage account deletion operation will fail if there is at least one WORM container with a legal hold or a blob with an active retention interval.

### Time-based retention policies

> [!IMPORTANT]
> A time-based retention policy must be *locked* for the blob to be in a compliant immutable (write and delete protected) state for SEC 17a-4(f) and other regulatory compliance. We recommend that you lock the policy in a reasonable amount of time, typically less than 24 hours. The initial state of an applied time-based retention policy is *unlocked*, allowing you to test the feature and make changes to the policy before you lock it. While the *unlocked* state provides immutability protection, we don't recommend using the *unlocked* state for any purpose other than short-term feature trials. 

When a time-based retention policy is applied on a container, all blobs in the container will stay in the immutable state for the duration of the *effective* retention period. The effective retention period for existing blobs is equal to the difference between the blob creation time and the user-specified retention interval.

For new blobs, the effective retention period is equal to the user-specified retention interval. Because users can extend the retention interval, immutable storage uses the most recent value of the user-specified retention interval to calculate the effective retention period.

For example, suppose that a user creates a time-based retention policy with a retention interval of five years. An existing blob in that container, _testblob1_, was created one year ago. The effective retention period for _testblob1_ is four years. When a new blob, _testblob2_, is uploaded to the container, the effective retention period for the new blob is five years.

An unlocked time-based retention policy is recommended only for feature testing and a policy must be locked in order to be compliant with SEC 17a-4(f) and other regulatory compliance. Once a time-based retention policy is locked, the policy cannot be removed and a maximum of five increases to the effective retention period is allowed. For more information on how to set and lock time-based retention policies, see [Set and manage immutability policies for Blob storage](storage-blob-immutability-policies-manage.md).

The following limits apply to retention policies:

- For a storage account, the maximum number of containers with locked time-based immutable policies is 1,000.
- The minimum retention interval is one day. The maximum is 146,000 days (400 years).
- For a container, the maximum number of edits to extend a retention interval for locked time-based immutable policies is 5.
- For a container, a maximum of seven time-based retention policy audit logs are retained for a locked policy.

### Legal holds

When you set a legal hold, all existing and new blobs stay in the immutable state until the legal hold is cleared. For more information on how to set and clear legal holds, see [Set and manage immutability policies for Blob storage](storage-blob-immutability-policies-manage.md).

A container can have both a legal hold and a time-based retention policy at the same time. All blobs in that container stay in the immutable state until all legal holds are cleared, even if their effective retention period has expired. Conversely, a blob stays in an immutable state until the effective retention period expires, even though all legal holds have been cleared.

The following table shows the types of Blob storage operations that are disabled for the different immutable scenarios. For more information, see the [Azure Blob Service REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api) documentation.

|Scenario  |Blob state  |Blob operations not allowed  |
|---------|---------|---------|
|Effective retention interval on the blob has not yet expired and/or legal hold is set     |Immutable: both delete and write-protected         | Put Blob<sup>1</sup>, Put Block<sup>1</sup>, Put Block List<sup>1</sup>, Delete Container, Delete Blob, Set Blob Metadata, Put Page, Set Blob Properties,  Snapshot Blob, Incremental Copy Blob, Append Block         |
|Effective retention interval on the blob has expired     |Write-protected only (delete operations are allowed)         |Put Blob<sup>1</sup>, Put Block<sup>1</sup>, Put Block List<sup>1</sup>, Set Blob Metadata, Put Page, Set Blob Properties,  Snapshot Blob, Incremental Copy Blob, Append Block         |
|All legal holds cleared, and no time-based retention policy is set on the container     |Mutable         |None         |
|No WORM policy is created (time-based retention or legal hold)     |Mutable         |None         |

<sup>1</sup> The application allows these operations to create a new blob once. All subsequent overwrite operations on an existing blob path in an immutable container are not allowed.

The following limits apply to legal holds:

- For a storage account, the maximum number of containers with a legal hold setting is 1,000.
- For a container, the maximum number of legal hold tags is 10.
- The minimum length of a legal hold tag is three alphanumeric characters. The maximum length is 23 alphanumeric characters.
- For a container, a maximum of 10 legal hold policy audit logs are retained for the duration of the policy.

## Pricing

There is no additional charge for using this feature. Immutable data is priced in the same way as mutable data. For pricing details on Azure Blob storage, see the [Azure Storage pricing page](https://azure.microsoft.com/pricing/details/storage/blobs/).

## FAQ

**Can you provide documentation of WORM compliance?**

Yes. To document compliance, Microsoft retained a leading independent assessment firm that specializes in records management and information governance, Cohasset Associates, to evaluate immutable Blob storage and its compliance with requirements specific to the financial services industry. Cohasset validated that immutable Blob storage, when used to retain time-based Blobs in a WORM state, meets the relevant storage requirements of CFTC Rule 1.31(c)-(d), FINRA Rule 4511, and SEC Rule 17a-4. Microsoft targeted this set of rules, as they represent the most prescriptive guidance globally for records retention for financial institutions. The Cohasset report is available in the [Microsoft Service Trust Center](https://aka.ms/AzureWormStorage). To request a letter of attestation from Microsoft regarding WORM compliance, please contact Azure support.

**Does the feature apply to only block blobs, or to page and append blobs as well?**

Immutable storage can be used with any blob type as it is set at the container level, but we recommend that you use WORM for containers that mainly store block blobs. Unlike block blobs, any new page blobs and append blobs need to be created outside a WORM container, and then copied in. After you copy these blobs into a WORM container, no further *appends* to an append blob or changes to a page blob are allowed. Setting a WORM policy on a container that stores VHDs (page blobs) for any active virtual machines is discouraged as it will lock the VM disk.

**Do I need to create a new storage account to use this feature?**

No, you can use immutable storage with any existing or newly created general-purpose v2 or Blob storage accounts. This feature is intended for usage with block blobs in GPv2 and Blob storage accounts. General-purpose v1 storage accounts are not supported but can be easily upgraded to general-purpose v2. For information on upgrading an existing general-purpose v1 storage account, see [Upgrade a storage account](../common/storage-account-upgrade.md).

**Can I apply both a legal hold and time-based retention policy?**

Yes, a container can have both a legal hold and a time-based retention policy at the same time. All blobs in that container stay in the immutable state until all legal holds are cleared, even if their effective retention period has expired. Conversely, a blob stays in an immutable state until the effective retention period expires, even though all legal holds have been cleared.

**Are legal hold policies only for legal proceedings or are there other use scenarios?**

No, Legal Hold is just the general term used for a non-time-based retention policy. It does not need to only be used for litigation-related proceedings. Legal Hold policies are useful for disabling overwrite and deletes for protecting important enterprise WORM data, where the retention period is unknown. You may use it as an enterprise policy to protect your mission critical WORM workloads or use it as a staging policy before a custom event trigger requires the use of a time-based retention policy. 

**Can I remove a _locked_ time-based retention policy or legal hold?**

Only unlocked time-based retention policies can be removed from a container. Once a time-based retention policy is locked, it cannot be removed; only effective retention period extensions are allowed. Legal hold tags can be deleted. When all legal tags are deleted, the legal hold is removed.

**What happens if I try to delete a container with a *locked* time-based retention policy or legal hold?**

The Delete Container operation will fail if at least one blob exists with a locked time-based retention policy or a legal hold. The Delete Container operation will succeed only if no blob with an active retention interval exists and there are no legal holds. You must delete the blobs before you can delete the container.

**What happens if I try to delete a storage account with a WORM container that has a *locked* time-based retention policy or legal hold?**

The storage account deletion will fail if there is at least one WORM container with a legal hold or a blob with an active retention interval. You must delete all WORM containers before you can delete the storage account. For information on container deletion, see the preceding question.

**Can I move the data across different blob tiers (hot, cool, cold) when the blob is in the immutable state?**

Yes, you can use the Set Blob Tier command to move data across the blob tiers while keeping the data in the compliant immutable state. Immutable storage is supported on hot, cool, and archive blob tiers.

**What happens if I fail to pay and my retention interval has not expired?**

In the case of non-payment, normal data retention policies will apply as stipulated in the terms and conditions of your contract with Microsoft.

**Do you offer a trial or grace period for just trying out the feature?**

Yes. When a time-based retention policy is first created, it is in an *unlocked* state. In this state, you can make any desired change to the retention interval, such as increase or decrease and even delete the policy. After the policy is locked, it stays locked until the retention interval expires. This locked policy prevents deletion and modification to the retention interval. We strongly recommend that you use the *unlocked* state only for trial purposes and lock the policy within a 24-hour period. These practices help you comply with SEC 17a-4(f) and other regulations.

**Can I use soft delete alongside Immutable blob policies?**

Yes. [Soft delete for Azure Blob storage](storage-blob-soft-delete.md) applies for all containers within a storage account regardless of a legal hold or time-based retention policy. We recommend enabling soft delete for additional protection before any immutable WORM policies are applied and confirmed.

**Where is the feature available?**

Immutable storage is available in Azure Public, China, and Government regions. If immutable storage is not available in your region, please contact support and email azurestoragefeedback@microsoft.com.

## Next steps

[Set and manage immutability policies for Blob storage](storage-blob-immutability-policies-manage.md)