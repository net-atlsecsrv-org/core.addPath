---
title: Manage smart groups
description: Managing Smart Groups created over your alert instances
ms.topic: conceptual
ms.subservice: alerts
ms.date: 09/24/2018

---

# Manage smart groups

[Smart groups](./alerts-smartgroups-overview.md?toc=%2fazure%2fazure-monitor%2ftoc.json) use machine learning algorithms to group together alerts on the basis of co-occurrence or similarity, so that the user can now manage smart groups instead of having to manage each alert individually. This article will walk you through how to access and use smart groups in Azure Monitor.

1. To see the Smart Groups created for your alert instances you can either:

     1. Click on **Smart Groups** from the **Alerts Summary** page.    
    ![Screenshot shows the Alert Summary page with Smart groups highlighted.](./media/alerts-managing-smart-groups/sg-alerts-summary.jpg)
    
     1. Click on Alerts by Smart Groups from the All Alerts page.   
     ![Screenshot shows the All Alerts page with Alert by Smart Group highlighted.](./media/alerts-managing-smart-groups/sg-all-alerts.jpg)

2. This takes you to the list view for all Smart Groups created over your alert instances. Instead of sifting through multiple alerts, you can now deal with the smart groups instead.   
![Screenshot shows the All Alerts page.](./media/alerts-managing-smart-groups/sg-list.jpg)

3. Clicking on any Smart Group opens up the details page, where you can see the grouping reason, along with the member alerts. This aggregation allows you to deal with a singular smart group, instead of sifting through multiple alerts.   
![Screenshot shows the Details page.](./media/alerts-managing-smart-groups/sg-details.jpg)