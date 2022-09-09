---
 title: include file
 description: include file
 services: storage
 author: roygara
 ms.service: storage
 ms.topic: include
 ms.date: 08/28/2020
 ms.author: rogarana
 ms.custom: include file
---
Azure Files offers four different tiers of storage, premium, transaction optimized, hot, and cool to allow you to tailor your shares to the performance and price requirements of your scenario:

- **Premium**: Premium file shares are backed by solid-state drives (SSDs) and provide consistent high performance and low latency, within single-digit milliseconds for most IO operations, for IO-intensive workloads. Premium file shares are suitable for a wide variety of workloads like databases, web site hosting, and development environments. Premium file shares can be used with both Server Message Block (SMB) and Network File System (NFS) protocols.
- **Transaction optimized**: Transaction optimized file shares enable transaction heavy workloads that don't need the latency offered by premium file shares. Transaction optimized file shares are offered on the standard storage hardware backed by hard disk drives (HDDs). Transaction optimized has historically been called "standard", however this refers to the storage media type rather than the tier itself (the hot and cool are also "standard" tiers, because they are on standard storage hardware).
- **Hot**: Hot file shares offer storage optimized for general purpose file sharing scenarios such as team shares and Azure File Sync. Hot file shares are offered on the standard storage hardware backed by HDDs.
- **Cool**: Cool file shares offer cost-efficient storage optimized for online archive storage scenarios. Azure File Sync may also be a good fit for lower churn workloads. Cool file shares are offered on the standard storage hardware backed by HDDs.

Premium file shares are deployed in the **FileStorage storage account** kind and are only available in a provisioned billing model. For more information on the provisioned billing model for premium file shares, see [Understanding provisioning for premium file shares](../articles/storage/files/storage-files-planning.md#understanding-provisioning-for-premium-file-shares). Standard file shares, including transaction optimized, hot, and cool file shares, are deployed in the **general purpose version 2 (GPv2) storage account** kind, and are available through pay as you go billing. Hot and cool file shares are available in all Azure Public and Azure Government regions. Transaction optimized file shares are available in all Azure regions, including Azure China and Azure Germany regions.

When selecting a storage tier for your workload, consider your performance and usage requirements. If your workload requires single-digit latency, or you are using SSD storage media on-premises, the premium tier is probably the best fit. If low latency isn't as much of a concern, for example with team shares mounted on-premises from Azure or cached on-premises using Azure File Sync, standard storage may be a better fit from a cost perspective.

The transaction optimized, hot, and cool tiers are stored on the exact same standard storage hardware. The main difference for these three tiers is their data at-rest storage prices, which are lower in cooler tiers, and the transaction prices, which are higher in the cooler tiers. This means:

- Transaction optimized, as the name implies, optimizes the price for high transaction workloads. Transaction optimized has the highest data at-rest storage price, but the lowest transaction prices.
- Hot is for active workloads that do not involve a large number of transactions, and has a slightly lower data at-rest storage price, but slightly higher transaction prices as compared to transaction optimized.
- Cool optimizes the price for workloads which do not have much activity, offering the lowest data at-rest storage price, but the highest transaction prices.

Your workload and activity level will determine the most cost efficient tier for your standard file share. You can view the exact prices for your desired region, redundancy level, and currency on the [Azure Files pricing page](https://azure.microsoft.com/pricing/details/storage/files/).

Once you've created a file share in a storage account, you cannot move it to tiers exclusive to different storage account kinds. For example, to move a transaction optimized file share to the premium tier, you must create a new file share in a FileStorage storage account and copy the data from your original share to a new file share in the FileStorage account. We recommend using AzCopy to copy data between Azure file shares, but you may also use tools like `robocopy` on Windows or `rsync` for macOS and Linux. 

File shares deployed within GPv2 storage accounts can be moved between the standard tiers (transaction optimized, hot, and cool) without creating a new storage account and migrating data, but you will incur transaction costs when you change your tier. When you move a share from a hotter tier to a cooler tier, you will incur the cooler tier's write transaction charge for each file in the share. Moving a file share from a cooler tier to a hotter tier will incur the cool tier's read transaction charge for each file in the share.