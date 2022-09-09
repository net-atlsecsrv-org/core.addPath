---
title: Save costs with Azure VMware Solution reserved instance
description: Learn how to buy a reserved instance for Azure VMware Solution.
ms.topic: how-to
ms.date: 11/12/2020
---

# Save costs with Azure VMware Solution

When you commit to a reserved instance of [Azure VMware Solution](introduction.md), you save money.  The reservation discount is applied automatically to the running Azure VMware Solution hosts that match the reservation scope and attributes. A reserved instance purchase covers only the compute part of your usage and includes software licensing costs. 

## Purchase restriction considerations

Reserved instances are available with some exceptions.

-   **Clouds** - Reservations are available only in the regions listed on the [Products available by region](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware) page.

-   **Insufficient quota** - A reservation scoped to a single/shared subscription must have hosts quota available in the subscription for the new reserved instance. You can [create quota increase request](enable-azure-vmware-solution.md) to resolve this issue.

-   **Offer eligibility**- You'll need an [Azure Enterprise Agreement (EA)](../cost-management-billing/manage/ea-portal-agreements.md) with Microsoft.

-   **Capacity restrictions** - In rare circumstances, Azure limits the purchase of new reservations for Azure VMware Solution host SKUs because of low capacity in a region.

## Buy a reservation

You can buy a reserved instance of an Azure VMware Solution host instance in the [Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D).

You can pay for the reservation [up front or with monthly payments](../cost-management-billing/reservations/prepare-buy-reservation.md).

These requirements apply to buying a reserved dedicated host instance:

-   You must be in an *Owner* role for at least one EA subscription or a subscription with a pay-as-you-go rate.

-   For EA subscriptions, you must enable the **Add Reserved Instances** option in the [EA portal](https://ea.azure.com/). If disabled, you must be an EA Admin for the subscription to enable it.

-   For subscription under a Cloud Solution Provider (CSP) Azure Plan, the partner must purchase the reserved instances in the Azure portal for the customer. 

### Buy reserved instances for an EA subscription

1. Sign in to the [Azure portal](https://portal.azure.com/).

2. Select **All services** > **Reservations**.

3. Select **Purchase Now** and then select **Azure VMware Solution**.

4. Enter the required fields. The selected attributes that match running Azure VMware Solution hosts qualify for the reservation discount.  Attributes include the SKU, regions (where applicable), and scope. Reservation scope selects where the reservation savings apply.

   If you have an EA agreement, you can use the **Add more option** to add instances quickly. The option isn't available for other subscription types.

   | Field        |  Description |
   | ------------ | ------------ |
   | Subscription | The subscription used to pay for the reservation. The payment method on the subscription is charged the costs for the reservation. The subscription type must be an enterprise agreement (offer numbers: MS-AZR-0017P or MS-AZR-0148P), Microsoft Customer Agreement, or an individual subscription with pay-as-you-go rates (offer numbers: MS-AZR-0003P or MS-AZR-0023P). The charges are deducted from the monetary commitment balance, if available, or charged as overage. For a subscription with pay-as-you-go rates, the charges are billed to the subscription's credit card or an invoice payment method. |
   | Scope        | The reservation's scope can cover one subscription or multiple subscriptions (shared scope). If you select:<br><ul><li><b>Single resource group scope</b> - Applies the reservation discount to the matching resources in the selected resource group only.</li><li><b>Single subscription scope</b> - Applies the reservation discount to the matching resources in the selected subscription.</li><li><b>Shared scope</b> - Applies the reservation discount to matching resources in eligible subscriptions that are in the billing context. For EA customers, the billing context is the enrollment. For individual subscriptions with pay-as-you-go rates, the billing scope is all eligible subscriptions created by the account administrator.</li></ul>       |
   | Region       | The Azure region that's covered by the reservation.   |
   | Host Size    | AV36    |
   | Term         | One year or three years.  |
   | Quantity     | The number of instances to purchase within the reservation. The quantity is the number of running Azure VMware Solution hosts that can get the billing discount.    |

### Buy reserved instances for a CSP subscription

CSPs that want to purchase reserved instances for their customers must use the **Admin On Behalf Of** (AOBO) procedure from the [Partner Center documentation](/partner-center/azure-plan-manage). For more information, view the [Admin on behalf of (AOBO)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) video.

1. Sign in to [Partner Center](https://partner.microsoft.com).

2. Select **CSP** to access the **Customers** area.

3. Expand customer details and select **Microsoft Azure Management Portal**. 

   :::image type="content" source="media/reserved-instances/csp-partner-center-aobo.png" alt-text="Microsoft Partner Center customers area" lightbox="media/reserved-instances/csp-partner-center-aobo.png":::

4. In the Azure portal, select **All services** > **Reservations**.

5. Select **Purchase Now** and then select **Azure VMware Solution**.

   :::image type="content" source="media/reserved-instances/csp-buy-ri-azure-portal.png" alt-text="Microsoft Azure portal reservations" lightbox="media/reserved-instances/csp-buy-ri-azure-portal.png":::

6. Enter the required fields. The selected attributes that match running Azure VMware Solution hosts qualify for the reservation discount.  Attributes include the SKU, regions (where applicable), and scope. Reservation scope selects where the reservation savings apply.

   | Field        |  Description |
   | ------------ | ------------ |
   | Subscription | The subscription used to pay for the reservation. The payment method on the subscription is charged the costs for the reservation. The subscription type must be an eligible one, which in this case is a CSP subscription|
   | Scope        | The reservation's scope can cover one subscription or multiple subscriptions (shared scope). If you select:<br><ul><li><b>Single resource group scope</b> - Applies the reservation discount to the matching resources in the selected resource group only.</li><li><b>Single subscription scope</b> - Applies the reservation discount to the matching resources in the selected subscription.</li><li><b>Shared scope</b> - Applies the reservation discount to matching resources in eligible subscriptions that are in the billing context. For EA customers, the billing context is the enrollment. For individual subscriptions with pay-as-you-go rates, the billing scope is all eligible subscriptions created by the account administrator.</li></ul>       |
   | Region       | The Azure region that's covered by the reservation.   |
   | Host Size    | AV36    |
   | Term         | One year or three years.  |
   | Quantity     | The number of instances to purchase within the reservation. The quantity is the number of running Azure VMware Solution hosts that can get the billing discount.     |

To learn more on how to view the purchased reservations for your customer, see [View Azure reservations as a Cloud Solution Provider (CSP)](../cost-management-billing/reservations/how-to-view-csp-reservations.md) article.

## Usage data and reservation usage

Your usage that gets a reservation discount has an effective price of zero. You can see which Azure VMware Solution instance received the reservation discount for each reservation.

For more information about how reservation discounts appear in usage data:

- For EA customers, see [Understand Azure reservation usage for your Enterprise enrollment](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- For individual subscriptions, see [Understand Azure reservation usage for your Pay-As-You-Go subscription](../cost-management-billing/reservations/understand-reserved-instance-usage.md)

## Change a reservation after purchase

You can make these changes to a reservation after purchase:

-   Update reservation scope

-   Instance size flexibility (if applicable)

-   Ownership

You can also split a reservation into smaller chunks or merge reservations. None of the changes cause a new commercial transaction or change the end date of the reservation.

For details about CSP-managed reservations, see [Sell Microsoft Azure reservations to customers using Partner Center, the Azure portal, or APIs](/partner-center/azure-reservations).



>[!NOTE]
>Once you've purchased your reservation, you won't be able to make these types of changes directly:
>
> - An existing reservation’s region
> - SKU
> - Quantity
> - Duration
>
>However, you can *exchange* a reservation if you want to make changes.

## Cancel, exchange, or refund reservations

You can cancel, exchange, or refund reservations with certain limitations. For more information, see [Self-service exchanges and refunds for Azure Reservations](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

CSPs can cancel, exchange, or refund reservations, with certain limitations, purchased for their customer. For more information, see [Manage, cancel, exchange, or refund Microsoft Azure reservations for customers](/partner-center/azure-reservations-manage).