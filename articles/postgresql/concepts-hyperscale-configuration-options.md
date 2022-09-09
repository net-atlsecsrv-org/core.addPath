---
title: Performance options – Hyperscale (Citus) - Azure Database for PostgreSQL
description: Options for a Hyperscale (Citus) server group, including node compute, storage, and regions.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 2/18/2020
---

# Azure Database for PostgreSQL – Hyperscale (Citus) performance options

## Compute and storage
 
You can select the compute and storage settings independently for
worker nodes and the coordinator node in a Hyperscale (Citus) server
group.  Compute resources are provided as vCores, which represent
the logical CPU of the underlying hardware. The storage size for
provisioning refers to the capacity available to the coordinator
and worker nodes in your Hyperscale (Citus) server group. The storage
includes  database files, temporary files, transaction logs, and
the Postgres server logs. The total amount of storage you provision
also defines the I/O capacity available to each worker and coordinator
node.
 
|                       | Worker node           | Coordinator node      |
|-----------------------|-----------------------|-----------------------|
| Compute, vCores       | 4, 8, 16, 32, 64      | 4, 8, 16, 32, 64      |
| Memory per vCore, GiB | 8                     | 4                     |
| Storage size, TiB     | 0.5, 1, 2             | 0.5, 1, 2             |
| Storage type          | General purpose (SSD) | General purpose (SSD) |
| IOPS                  | Up to 3 IOPS/GiB      | Up to 3 IOPS/GiB      |


## Regions
Hyperscale (Citus) server groups are available in the following Azure regions:

* Americas:
	* Canada Central
	* Central US
	* East US
	* East US 2
	* North Central US
	* West US 2
* Asia Pacific:
	* Australia East
	* Japan East
	* Korea Central
	* Southeast Asia
* Europe:
	* North Europe
	* UK South
	* West Europe

Some of these regions may not be initially activated on all Azure
subscriptions. If you want to use a region from the list above and don't see it
in your subscription, or if you want to use a region not on this list, open a
[support
request](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## Pricing
For the most up-to-date pricing information, see the service
[pricing page](https://azure.microsoft.com/pricing/details/postgresql/).
To see the cost for the configuration you want, the
[Azure portal](https://portal.azure.com/#create/Microsoft.PostgreSQLServer)
shows the monthly cost on the **Configure** tab based on the options you
select. If you don't have an Azure subscription, you can use the Azure pricing
calculator to get an estimated price. On the
[Azure pricing calculator](https://azure.microsoft.com/pricing/calculator/)
website, select **Add items**, expand the **Databases** category, and choose
**Azure Database for PostgreSQL – Hyperscale (Citus)** to customize the
options.
 
## Next steps
Learn how to [create a Hyperscale (Citus) server group in the portal](quickstart-create-hyperscale-portal.md).
