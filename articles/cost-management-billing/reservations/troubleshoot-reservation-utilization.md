---
title: Troubleshoot Azure reservation utilization
description: This article helps you understand and troubleshoot Azure reservations that show no or zero (0) utilization in the Azure portal. Utilization that doesn't match is also explained.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 10/19/2020
---

# Troubleshoot reservation utilization

This article helps you understand and troubleshoot Azure reservations that show no or zero (0) utilization in the Azure portal. Utilization that doesn't match is also explained.

## Symptoms

1. Sign in to the [Azure portal](https://portal.azure.com) and navigate to **Reservations**.
1. In the list of reservations, look the amount of utilization for a reservation in the **Utilization (%)** column. It might be 0%.
1. Select the reservation.
1. On the reservation overview page, your used percentage in the graph might not match the value shown in the reservation list.

## Cause

The **Utilization (%)** column in the Azure portal shows the value for the current day. The value is calculated as usage data arrives from where resources run. Azure uses the usage to calculate the utilization percentage.

Some resources report usage slower than others. Additionally, some product types like SQL Databases are slow to report their usage data.

The latency can cause the utilization calculation to show lower values than the actual usage. The difference is noticeable at the day boundary. In such cases, if Azure doesn’t get usage data from four to eight hours, it calculates a value of 0%. The 0% value is shown because usage data hasn’t arrived, and it appears that the reservation isn’t applying a benefit to any resources.

As usage data arrives, the value changes toward the correct percentage. When all the usage data is collected, the correct value is determined and is shown accurately in the graph.

## Solution

If you find that your utilization values don't match your expectations, review the graph to get the most view of your actual utilization. Any point value older than two days should be accurate. Longer term averages from seven to 30 days should be accurate.

## Next steps

- For more information about managing reservations, see [Manage Reservations for Azure resources](manage-reserved-vm-instance.md).