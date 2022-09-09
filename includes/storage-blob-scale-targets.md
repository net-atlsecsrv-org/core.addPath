---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/23/2020
ms.author: tamram
---

| Resource | Target | Target (Preview) |
|-|-|-|
| Maximum size of single blob container | Same as maximum storage account capacity |  |
| Maximum number of blocks in a block blob or append blob | 50,000 blocks |  |
| Maximum size of a block in a block blob | 100 MiB | 4000 MiB (preview) |
| Maximum size of a block blob | 50,000 X 100 MiB (approximately 4.75 TiB) | 50,000 X 4000 MiB (approximately 190.7 TiB) (preview) |
| Maximum size of a block in an append blob | 4 MiB |  |
| Maximum size of an append blob | 50,000 x 4 MiB (approximately 195 GiB) |  |
| Maximum size of a page blob | 8 TiB |  |
| Maximum number of stored access policies per blob container | 5 |  |
| Target request rate for a single blob | Up to 500 requests per second |  |
| Target throughput for a single page blob | Up to 60 MiB per second |  |
| Target throughput for a single block blob | Up to storage account ingress/egress limits<sup>1</sup> |  |

<sup>1</sup> Throughput for a single blob depends on several factors, including, but not limited to: concurrency, request size, performance tier, speed of source for uploads, and destination for downloads. To take advantage of the performance enhancements of [high-throughput block blobs](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/), upload larger blobs or blocks. Specifically, call the [Put Blob](/rest/api/storageservices/put-blob) or [Put Block](/rest/api/storageservices/put-block) operation with a blob or block size that is greater than 4 MiB for standard storage accounts. For premium block blob or for Data Lake Storage Gen2 storage accounts, use a block or blob size that is greater than 256 KiB.

The following table describes the maximum block and blob sizes permitted by service version.

| Service version | Maximum block size (via Put Block) | Maximum blob size (via Put Block List) | Maximum blob size via single write operation (via Put Blob) |
|-|-|-|-|
| Version 2019-12-12 and later | 4000 MiB (preview) | Approximately 190.7 TiB (4000 MiB X 50,000 blocks) (preview) | 5000 MiB (preview) |
| Version 2016-05-31 through version 2019-07-07 | 100 MiB | Approximately 4.75 TiB (100 MiB X 50,000 blocks) | 256 MiB |
| Versions prior to 2016-05-31 | 4 MiB | Approximately 195 GiB (4 MiB X 50,000 blocks) | 64 MiB |
