---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/16/2020
ms.author: rogarana
---
| Resource | Standard file shares\* | Premium file shares |
|----------|---------------|------------------------------------------|
| Minimum size of a file share | No minimum; pay as you go | 100 GiB; provisioned |
| Maximum size of a file share | 100 TiB\*\*, 5 TiB | 100 TiB |
| Maximum size of a file in a file share | 1 TiB | 4 TiB |
| Maximum number of files in a file share | No limit | No limit |
| Maximum IOPS per share | 10,000 IOPS\*\*, 1,000 IOPS or 100 requests in 100ms | 100,000 IOPS |
| Maximum number of stored access policies per file share | 5 | 5 |
| Target throughput for a single file share | up to 300 MiB/sec\*\*, Up to 60 MiB/sec ,  | See premium file share ingress and egress values|
| Maximum egress for a single file share | See standard file share target throughput | Up to 6,204 MiB/s |
| Maximum ingress for a single file share | See standard file share target throughput | Up to 4,136 MiB/s |
| Maximum open handles per file or directory | 2,000 open handles | 2,000 open handles |
| Maximum number of share snapshots | 200 share snapshots | 200 share snapshots |
| Maximum object (directories and files) name length | 2,048 characters | 2,048 characters |
| Maximum pathname component (in the path \A\B\C\D, each letter is a component) | 255 characters | 255 characters |
| Hard link limit (NFS only) | N/A | 178 |
| Maximum number of SMB Multichannel channels | N/A | 4 |

\* The limits for standard file shares apply to all three of the tiers available for standard file shares: transaction optimized, hot, and cool.

\*\* Default on standard file shares is 5 TiB, see [Enable and create large file shares](../articles/storage/files/storage-files-how-to-create-large-file-share.md) for the details on how to increase the standard file shares scale up to 100 TiB.
