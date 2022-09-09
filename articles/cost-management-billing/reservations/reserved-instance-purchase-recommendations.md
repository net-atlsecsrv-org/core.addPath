---
title: Azure reservation recommendations
description: Learn about Azure reservation recommendations.
author: banders
ms.author: banders
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 08/04/2020
---

# Reservation recommendations

Azure reserved instance (RI) purchase recommendations are provided through Azure Consumption [Reservation Recommendation API](/rest/api/consumption/reservationrecommendations), [Azure Advisor](../../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs), and through the reservation purchase experience in the Azure portal.

The following steps define how recommendations are calculated:

1. The recommendation engine evaluates the hourly usage for your resources in the given scope over the past 7, 30, and 60 days.
2. Based on the usage data, the engine simulates your costs with and without reservations.
3. The costs are simulated for different quantities, and the quantity that maximizes the savings is recommended.
4. If your resources are shut down regularly, the simulation won't find any savings, and no purchase recommendation is provided.

## Recommendations in the Azure portal

Reservation purchase recommendations are also shown in the Azure portal in the purchase experience. Recommendations are shown with the **Recommended Quantity**. When purchased, the quantity that Azure recommends will give the maximum savings possible. Although you can buy any quantity that you like, if you buy a different quantity your savings will not be optimal.

Let's look at some examples why.

In the following example image for the selected recommendation, Azure recommends a purchase quantity of 6.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity.png" alt-text="Example showing a reservation purchase recommendation" lightbox="./media/reserved-instance-purchase-recommendations/recommended-quantity.png" :::

More information about the recommendation is shown when you select the **See details** link. The following image shows details about the recommendation. The quantity recommended is calculated for the highest possible usage, based on your historical usage. Your recommendation might not be for 100% utilization if you have inconsistent usage. In the example, note that utilization fluctuated over time. The cost of the reservation, possible savings, and utilization percentage is shown.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity-details.png" alt-text="Example showing a reservation purchase recommendation details" :::

When you increase or decrease reservation the quantity beyond the recommendation, the chart and estimated values change. By increasing the reservation quantity, your savings will be reduced because you'll end up with reduced reservation utilization. In other words, you'll pay for reservations that aren't fully used.

If you decrease the reservation quantity, your savings will also be reduced. Although you'll have increased utilization, there will likely be periods when your reservations won't fully cover your use. Usage beyond your reservation quantity will be used by more expensive pay-as-you-go resources. The following example image illustrates the point. We've manually reduced the reservation quantity to 4. The reservation utilization is increased, but the overall savings is reduced because pay-as-you go costs are present.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity-details-changed.png" alt-text="Example showing changed reservation purchase recommendation details" :::

To maximize savings with reservations, try to purchase reservations as close to the recommendation as possible.

## Recommendations in Azure Advisor

Reservation purchase recommendations are available in Azure Advisor. Keep in mind the following points:

- Advisor has only single-subscription scope recommendations.
- Recommendations are calculated by considering past 30-day usage trend.
- Recommendations quantity and savings are for 3-Year reservation where available. If 3 Year reservation is not sold for the service, the recommendation is calculated using 1 year reservation price.
- Recommendation recommendations factor any special discounts that your might have on your on-demand usage rates.
- If you purchase a shared-scope reservation, Advisor reservation purchase recommendations can take up to 30 days to disappear.

## Other expected API behavior

- When using a look-back period of seven days, you might not get recommendations when VMs are shut down for more than a day.

## Next steps

- Learn about [how the Azure reservation discount is applied to virtual machines](../manage/understand-vm-reservation-charges.md).
