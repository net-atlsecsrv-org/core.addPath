---
title: Reserved compute pricing - Azure Database for PostgreSQL - Single Server
description: Prepay for Azure Database for PostgreSQL compute resources with reserved capacity
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/16/2020
---

# Prepay for Azure Database for PostgreSQL - Single Server compute resources with reserved capacity

Azure Database for PostgreSQL now helps you save money by prepaying for compute resources compared to pay-as-you-go prices. With Azure Database for PostgreSQL reserved capacity, you make an upfront commitment on PostgreSQL server for a one or three year period to get a significant discount on the compute costs. To purchase Azure Database for PostgreSQL reserved capacity, you need to specify the Azure region, deployment type, performance tier, and term. </br>

You don't need to assign the reservation to specific Azure Database for PostgreSQL servers. An already running Azure Database for PostgreSQL (or ones that are newly deployed) will automatically get the benefit of reserved pricing. By purchasing a reservation, you're pre-paying for the compute costs for a period of one or three years. As soon as you buy a reservation, the Azure database for PostgreSQL compute charges that match the reservation attributes are no longer charged at the pay-as-you go rates. A reservation does not cover software, networking, or storage charges associated with the PostgreSQL Database servers. At the end of the reservation term, the billing benefit expires, and the Azure Database for PostgreSQL are billed at the pay-as-you go price. Reservations do not auto-renew. For pricing information, see the [Azure Database for PostgreSQL reserved capacity offering](https://azure.microsoft.com/pricing/details/postgresql/). </br>

> [!IMPORTANT]
> Reserved capacity pricing is available for the Azure Database for PostgreSQL in both [Single server](./overview.md#azure-database-for-postgresql---single-server) and [Hyperscale Citus](./overview.md#azure-database-for-postgresql--hyperscale-citus) deployment options. For information about RI pricing on Hyperscale (Citus), see [this page](concepts-hyperscale-reserved-pricing.md).

You can buy Azure Database for PostgreSQL reserved capacity in the [Azure portal](https://portal.azure.com/). Pay for the reservation [up front or with monthly payments](../cost-management-billing/reservations/prepare-buy-reservation.md). To buy the reserved capacity:

* You must be in the owner role for at least one Enterprise or individual subscription with pay-as-you-go rates.
* For Enterprise subscriptions, **Add Reserved Instances** must be enabled in the [EA portal](https://ea.azure.com/). Or, if that setting is disabled, you must be an EA Admin on the subscription.
* For Cloud Solution Provider (CSP) program, only the admin agents or sales agents can purchase Azure Database for PostgreSQL reserved capacity. </br>

The details on how enterprise customers and Pay-As-You-Go customers are charged for reservation purchases, see [understand Azure reservation usage for your Enterprise enrollment](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) and [understand Azure reservation usage for your Pay-As-You-Go subscription](../cost-management-billing/reservations/understand-reserved-instance-usage.md).


## Determine the right server size before purchase

The size of reservation should be based on the total amount of compute used by the existing or soon-to-be-deployed servers within a specific region and using the same performance tier and hardware generation.</br>

For example, let's suppose that you are running one general purpose Gen5 – 32 vCore PostgreSQL database, and two memory-optimized Gen5 – 16 vCore PostgreSQL databases. Further, let's supposed that you plan to deploy within the next month an additional general purpose Gen5 – 32 vCore database server, and one memory-optimized Gen5 – 16 vCore database server. Let's suppose that you know that you will need these resources for at least one year. In this case, you should purchase a 64 (2x32) vCores, one-year reservation for single database general purpose - Gen5 and a 48 (2x16 + 16) vCore one year reservation for single database memory optimized - Gen5


## Buy Azure Database for PostgreSQL reserved capacity

1. Sign in to the [Azure portal](https://portal.azure.com/).
2. Select **All services** > **Reservations**.
3. Select **Add** and then in the Purchase reservations pane, select **Azure Database for PostgreSQL** to purchase a new reservation for your PostgreSQL databases.
4. Fill in the required fields. Existing or new databases that match the attributes you select qualify to get the reserved capacity discount. The actual number of your Azure Database for PostgreSQL servers that get the discount depend on the scope and quantity selected.


:::image type="content" source="media/concepts-reserved-pricing/postgresql-reserved-price.png" alt-text="Overview of reserved pricing":::


The following table describes required fields.

| Field | Description |
| :------------ | :------- |
| Subscription   | The subscription used to pay for the Azure Database for PostgreSQL reserved capacity reservation. The payment method on the subscription is charged the upfront costs for the Azure Database for PostgreSQL reserved capacity reservation. The subscription type must be an enterprise agreement (offer numbers: MS-AZR-0017P or MS-AZR-0148P) or an individual agreement with pay-as-you-go pricing (offer numbers: MS-AZR-0003P or MS-AZR-0023P). For an enterprise subscription, the charges are deducted from the enrollment's monetary commitment balance or charged as overage. For an individual subscription with pay-as-you-go pricing, the charges are billed to the credit card or invoice payment method on the subscription.
| Scope | The vCore reservation’s scope can cover one subscription or multiple subscriptions (shared scope). If you select: </br></br> **Shared**, the vCore reservation discount is applied to Azure Database for PostgreSQL servers running in any subscriptions within your billing context. For enterprise customers, the shared scope is the enrollment and includes all subscriptions within the enrollment. For Pay-As-You-Go customers, the shared scope is all Pay-As-You-Go subscriptions created by the account administrator.</br></br> **Single subscription**, the vCore reservation discount is applied to Azure Database for PostgreSQL servers in this subscription. </br></br> **Single resource group**, the reservation discount is applied to Azure Database for PostgreSQL servers in the selected subscription and the selected resource group within that subscription.
| Region | The Azure region that’s covered by the Azure Database for PostgreSQL reserved capacity reservation.
| Deployment Type | The Azure Database for PostgreSQL resource type that you want to buy the reservation for.
| Performance Tier | The service tier for the Azure Database for PostgreSQL servers.
| Term | One year
| Quantity | The amount of compute resources being purchased within the Azure Database for PostgreSQL reserved capacity reservation. The quantity is a number of vCores in the selected Azure region and Performance tier that are being reserved and will get the billing discount. For example, if you are running or planning to run an Azure Database for PostgreSQL servers with the total compute capacity of Gen5 16 vCores in the East US region, then you would specify quantity as 16 to maximize the benefit for all servers.

## Cancel, exchange, or refund reservations

You can cancel, exchange, or refund reservations with certain limitations. For more information, see [Self-service exchanges and refunds for Azure Reservations](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## vCore size flexibility

vCore size flexibility helps you scale up or down within a performance tier and region, without losing the reserved capacity benefit. 

## Need help? Contact us

If you have questions or need help, [create a support request](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## Next steps

The vCore reservation discount is applied automatically to the number of Azure Database for PostgreSQL servers that match the Azure Database for PostgreSQL reserved capacity reservation scope and attributes. You can update the scope of the Azure database for PostgreSQL reserved capacity reservation through Azure portal, PowerShell, CLI or through the API.

To learn more about Azure Reservations, see the following articles:

* [What are Azure Reservations](../cost-management-billing/reservations/save-compute-costs-reservations.md)?
* [Manage Azure Reservations](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
* [Understand Azure Reservations discount](../cost-management-billing/reservations/understand-reservation-charges.md)
* [Understand reservation usage for your Pay-As-You-Go subscription](../cost-management-billing/reservations/understand-reservation-charges-postgresql.md)
* [Understand reservation usage for your Enterprise enrollment](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
* [Azure Reservations in Partner Center Cloud Solution Provider (CSP) program](/partner-center/azure-reservations)