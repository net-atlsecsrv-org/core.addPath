---
title: How to create a support ticket 
description: How to create a support ticket in Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
---

# How to create a support ticket for SQL Data Warehouse
If you are having any issues with your SQL Data Warehouse, create a support ticket so the engineering support team can assist you.

## Create a support ticket
1. Open the [Azure portal][Azure portal].
2. On the Home screen, click the **Help + support** tab.
   
    ![Help + support](./media/sql-data-warehouse-get-started-create-support-ticket/MainPage.PNG)
3. On the Help + Support blade, click **New support request** and fill out the **Basics** blade.

   Select your [Azure support plan][Azure support plan].
   
   * **Billing, quota, and subscription management** support are available at all support levels.
   * **Break-fix** support is provided through [Developer][Developer], [Standard][Standard], [Professional Direct][Professional Direct], or [Premier][Premier] support. Break-fix issues are problems experienced by customers while using Azure where there is a reasonable expectation that Microsoft caused the problem.
   * **Developer mentoring** and **advisory services** are available at the [Professional Direct][Professional Direct] and [Premier][Premier] support levels. 
     
     If you have a Premier support plan, you can also report SQL Data Warehouse related issues on the [Microsoft Premier online portal][Microsoft Premier online portal].  See [Azure support plans][Azure support plan] to learn more about the various support plans, including scope, response times, pricing, etc.  For frequently asked questions about Azure support, see [Azure support FAQs][Azure support FAQs].  
        
     ![Basics blade](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_1.PNG)
     ![Basics blade1](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_2.PNG)
4. Fill out the **Problem** blade.
    ![Problem_blade](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_3.PNG)
   
   > [!NOTE]
   > By default, each SQL server (for example, myserver.database.windows.net) has a **DTU Quota** of 45,000. This quota is simply a safety limit. You can increase your quota by creating a support ticket and selecting *Quota* as the request type. To calculate your DTU needs, multiply 7.5 by the total [DWU][DWU] needed. For example, you would like to host two DW6000s on one SQL server, then you should request a DTU quota of 90,000.  You can view your current DTU consumption from the SQL server blade in the portal. Both paused and unpaused databases count toward the DTU quota. 
   > 
   > 
   
5. Fill out your **contact information**.
   ![Contact_information](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_4.PNG)

    
6. Click **Create** to submit the support request.

## Monitor a support ticket
After you have submitted the support request, the Azure support team will contact you. To check your request status and details, click **All support requests** on the dashboard.

![Check status](./media/sql-data-warehouse-get-started-create-support-ticket/Monitor_ticket.PNG)

## Other resources
Additionally, you can connect with the SQL Data Warehouse community on [Stack Overflow][Stack Overflow] or on the [Azure SQL Data Warehouse MSDN forum][Azure SQL Data Warehouse MSDN forum].

<!--Image references--> 

<!--Article references--> 
[DWU]: ./sql-data-warehouse-overview-what-is.md

<!--MSDN references--> 

<!--Other web references--> 
[Azure portal]: https://portal.azure.com/
[Azure support plan]: https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/  
[Developer]: https://azure.microsoft.com/support/plans/developer/  
[Standard]: https://azure.microsoft.com/support/plans/standard/  
[Professional Direct]: https://azure.microsoft.com/support/plans/prodirect/  
[Premier]: https://azure.microsoft.com/support/plans/premier/  
[Azure support FAQs]: https://azure.microsoft.com/support/faq/
[Microsoft Premier online portal]: https://premier.microsoft.com/
[Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw/
[Azure SQL Data Warehouse MSDN forum]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/

