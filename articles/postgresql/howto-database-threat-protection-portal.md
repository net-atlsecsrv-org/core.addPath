---
title: Use Advanced Threat Protection - Azure Database for PostgreSQL - Single Server
description: Threat Protection detects anomalous database activities indicating potential security threats to the database. 
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
---
# Advanced Threat Protection for Azure Database for PostgreSQL - Single Server

Advanced Threat Protection for Azure Database for PostgreSQL detects anomalous activities indicating unusual and potentially harmful attempts to access or exploit databases.

Advanced Threat Protection is part of the Advanced Data Security offering, which is a unified package for advanced security capabilities. Advanced Threat Protection can be accessed and managed via the [Azure portal](https://portal.azure.com) and is currently in preview.

> [!NOTE]
> The Advanced Threat Protection feature is **not** available in the following Azure government and sovereign cloud regions: US Gov Texas, US Gov Arizona, US Gov Iowa, US, Gov Virginia, US DoD East, US DoD Central, Germany Central, Germany North, China East, China East 2. Please visit [products available by region](https://azure.microsoft.com/global-infrastructure/services/) for general product availability.
>

> [!NOTE]
> This feature is available in all regions of Azure where Azure Database for PostgreSQL is deployed for General Purpose and Memory Optimized servers.

## Set up threat detection
1. Launch the Azure portal at [https://portal.azure.com](https://portal.azure.com).
2. Navigate to the configuration page of the Azure Database for PostgreSQL server you want to protect. In the security settings, select **Advanced Threat Protection (Preview)**.
3. On the **Advanced Threat Protection (Preview)** configuration page:

   - Enable Advanced Threat Protection on the server.
   - In **Advanced Threat Protection Settings**, in the **Send alerts to** text box, provide the list of emails to receive security alerts upon detection of anomalous database activities.
  
   ![Set up threat detection](./media/howto-database-threat-protection-portal/set-up-threat-protection.png)

## Explore anomalous database activities

You receive an email notification upon detection of anomalous database activities. The email provides information on the suspicious security event including the nature of the anomalous activities, database name, server name, application name, and the event time. In addition, the email provides information on possible causes and recommended actions to investigate and mitigate the potential threat to the database.
    
1. Click the **View recent alerts** link in the email to launch the Azure portal and show the Azure Security Center alerts page, which provides an overview of active threats detected on the SQL database.
    
    ![Anomalous activity report](./media/howto-database-threat-protection-portal/anomalous-activity-report.png)

    View active threats:

    ![Active threats](./media/howto-database-threat-protection-portal/active-threats.png)

2. Click a specific alert to get additional details and actions for investigating this threat and remediating future threats.
    
    ![Specific alert](./media/howto-database-threat-protection-portal/specific-alert.png)

## Explore threat detection alerts

Advanced Threat Protection integrates its alerts with [Azure Security Center](https://azure.microsoft.com/services/security-center/). 

Click **Security alerts** under **THREAT PROTECTION** to launch the Azure Security Center alerts page and get an overview of active SQL threats detected on the database.

  ![Threat protection asc](./media/howto-database-threat-protection-portal/threat-detection-alert-asc.png)

## Next steps

* Learn more about [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
* For more information on pricing, see the [Azure Database for PostgreSQL Pricing page](https://azure.microsoft.com/pricing/details/postgresql/)  
