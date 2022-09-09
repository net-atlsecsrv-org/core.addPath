---
title: Data Discovery & Classification 
description: Data Discovery & Classification for Azure SQL Database and Azure Synapse Analytics
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: 
titleSuffix: Azure SQL Database and Azure Synapse
ms.devlang: 
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 04/21/2020
tags: azure-synapse
---
# Data Discovery & Classification for Azure SQL Database and Azure Synapse Analytics

Data Discovery & Classification is built into Azure SQL Database. It provides advanced capabilities for discovering, classifying, labeling, and reporting the sensitive data in your databases.

Your most sensitive data might include business, financial, healthcare, or personal information. Discovering and classifying this data can play a pivotal role in your organization's information-protection approach. It can serve as infrastructure for:

- Helping to meet standards for data privacy and requirements for regulatory compliance.
- Various security scenarios, such as monitoring (auditing) and alerting on anomalous access to sensitive data.
- Controlling access to and hardening the security of databases that contain highly sensitive data.

Data Discovery & Classification is part of the [Advanced Data Security](sql-database-advanced-data-security.md) offering, which is a unified package for advanced SQL security capabilities. You can access and manage Data Discovery & Classification via the central **SQL Advanced Data Security** section of the Azure portal.

> [!NOTE]
> This article relates to Azure SQL Database and Azure Synapse Analytics. For simplicity, we use *SQL Database* here to refer to both SQL Database and Azure Synapse. For information about SQL Server (on-premises), see [SQL Data Discovery and Classification](https://go.microsoft.com/fwlink/?linkid=866999).

## <a id="what-is-dc"></a>What is Data Discovery & Classification?

Data Discovery & Classification introduces a set of advanced services and new SQL Database capabilities. It forms a new information-protection paradigm for SQL Database, aimed at protecting the data and not just the database. The paradigm includes:

- **Discovery and recommendations:** The classification engine scans your database and identifies columns that contain potentially sensitive data. It then provides you with an easy way to review and apply recommended classification via the Azure portal.

- **Labeling:** You can apply sensitivity-classification labels persistently to columns by using new metadata attributes that have been added to the SQL database engine. This metadata can then be used for advanced, sensitivity-based auditing and protection scenarios.

- **Query result-set sensitivity:** The sensitivity of a query result set is calculated in real time for auditing purposes.

- **Visibility:** You can view the database-classification state in a detailed dashboard in the Azure portal. Also, you can download a report in Excel format to use for compliance and auditing purposes and other needs.

## <a id="discover-classify-columns"></a>Discover, classify, and label sensitive columns

This section describes the steps for:

- Discovering, classifying, and labeling columns that contain sensitive data in your database.
- Viewing the current classification state of your database and exporting reports.

The classification includes two metadata attributes:

- **Labels**: The main classification attributes, used to define the sensitivity level of the data stored in the column.  
- **Information types**: Attributes that provide more granular information about the type of data stored in the column.

### Define and customize your classification taxonomy

Data Discovery & Classification comes with a built-in set of sensitivity labels and a built-in set of information types and discovery logic. You can now customize this taxonomy and define a set and ranking of classification constructs specifically for your environment.

You define and customize of your classification taxonomy in one central place for your entire Azure organization. That location is in [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), as part of your security policy. Only someone with administrative rights on the organization's root management group can do this task.

As part of policy management for SQL information protection, you can define custom labels, rank them, and associate them with a selected set of information types. You can also add your own custom information types and configure them with string patterns. The patterns are added to the discovery logic for identifying this type of data in your databases.

Learn more about customizing and managing your policy in the [SQL Information Protection policy how-to guide](https://go.microsoft.com/fwlink/?linkid=2009845&clcid=0x409).

After the organization-wide policy has been defined, you can continue classifying individual databases by using your customized policy.

### Classify your SQL database

1. Go to the [Azure portal](https://portal.azure.com).

2. Go to **Advanced Data Security** under the **Security** heading in your Azure SQL Database pane. Select **Advanced data security**, and then select the **Data Discovery & Classification** card.

   ![Advanced Data Security pane in Azure portal](./media/sql-data-discovery-and-classification/data_classification.png)

3. On the **Data discovery & classification** page, the **Overview** tab includes a summary of the current classification state of the database. The summary includes a detailed list of all classified columns, which you can also filter to show only specific schema parts, information types, and labels. If you haven’t classified any columns yet, [skip to step 5](#step-5).

   ![Summary of current classification state](./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png)

4. To download a report in Excel format, select **Export** in the top menu of the pane.

5. <a id="step-5"></a>To begin classifying your data, select the **Classification** tab on the **Data discovery & classification** page.

    The classification engine scans your database for columns containing potentially sensitive data and provides a list of recommended column classifications.

6. View and apply classification recommendations:

   - To view the list of recommended column classifications, select the recommendations panel at the bottom of the pane.

   - To accept a recommendation for a specific column, select the check box in the left column of the relevant row. To mark all recommendations as accepted, select the leftmost check box in the recommendations table header.

       ![Review and select from list of classification recommendations](./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png)

   - To apply the selected recommendations, select **Accept selected recommendations**.

7. You can also classify columns manually, as an alternative or in addition to the recommendation-based classification:

   1. Select **Add classification** in the top menu of the pane.

   1. In the context window that opens, select the schema, table, and column that you want to classify, and the information type and sensitivity label.

   1. Select **Add classification** at the bottom of the context window.

      ![Select a column to classify](./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png)

8. To complete your classification and persistently label (tag) the database columns with the new classification metadata, select **Save** in the top menu of the window.

## <a id="audit-sensitive-data"></a>Audit access to sensitive data

An important aspect of the information-protection paradigm is the ability to monitor access to sensitive data. [Azure SQL Database Auditing](sql-database-auditing.md) has been enhanced to include a new field in the audit log called `data_sensitivity_information`. This field logs the sensitivity classifications (labels) of the data that was returned by a query. Here's an example:

![Audit log](./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png)

## <a id="permissions"></a>Permissions

These built-in roles can read the data classification of an Azure SQL database:

- Owner
- Reader
- Contributor
- SQL Security Manager
- User Access Administrator

These built-in roles can modify the data classification of an Azure SQL database:

- Owner
- Contributor
- SQL Security Manager

Learn more about role-based permissions in [RBAC for Azure resources](https://docs.microsoft.com/azure/role-based-access-control/overview).

## <a id="manage-classification"></a>Manage classifications

You can use T-SQL, a REST API, or PowerShell to manage classifications.

### Use T-SQL

You can use T-SQL to add or remove column classifications, and to retrieve all classifications for the entire database.

> [!NOTE]
> When you use T-SQL to manage labels, there's no validation that labels that you add to a column exist in the organization's information-protection policy (the set of labels that appear in the portal recommendations). So, it's up to you to validate this.

For information about using T-SQL for classifications, see the following references:

- To add or update the classification of one or more columns: [ADD SENSITIVITY CLASSIFICATION](https://docs.microsoft.com/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
- To remove the classification from one or more columns: [DROP SENSITIVITY CLASSIFICATION](https://docs.microsoft.com/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- To view all classifications on the database: [sys.sensitivity_classifications](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

### Use the Rest API

You can use the REST API to programmatically manage classifications and recommendations. The published REST API supports the following operations:

- [Create Or Update](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/createorupdate): Creates or updates the sensitivity label of the specified column.
- [Delete](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/delete): Deletes the sensitivity label of the specified column.
- [Disable Recommendation](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/disablerecommendation): Disables sensitivity recommendations on the specified column.
- [Enable Recommendation](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/enablerecommendation): Enables sensitivity recommendations on the specified column. (Recommendations are enabled by default on all columns.)
- [Get](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/get): Gets the sensitivity label of the specified column.
- [List Current By Database](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listcurrentbydatabase): Gets the current sensitivity labels of the specified database.
- [List Recommended By Database](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listrecommendedbydatabase): Gets the recommended sensitivity labels of the specified database.

### Use PowerShell cmdlets
You can use PowerShell to manage classifications and recommendations for Azure SQL Database and managed instances.

#### PowerShell cmdlets for SQL Database

- [Get-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityclassification)
- [Set-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesensitivityclassification)
- [Remove-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesensitivityclassification)
- [Get-AzSqlDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityrecommendation)
- [Enable-AzSqlDatabaSesensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqldatabasesensitivityrecommendation)
- [Disable-AzSqlDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqldatabasesensitivityrecommendation)

#### PowerShell cmdlets for managed instances

- [Get-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityclassification)
- [Set-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasesensitivityclassification)
- [Remove-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabasesensitivityclassification)
- [Get-AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityrecommendation)
- [Enable-AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqlinstancedatabasesensitivityrecommendation)
- [Disable-AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqlinstancedatabasesensitivityrecommendation)


## <a id="next-steps"></a>Next steps

- Learn more about [Advanced Data Security](sql-database-advanced-data-security.md).
- Consider configuring [Azure SQL Database Auditing](sql-database-auditing.md) for monitoring and auditing access to your classified sensitive data.
- For a presentation that includes data discovery and classification, see [Discovering, classifying, labeling & protecting SQL data | Data Exposed](https://www.youtube.com/watch?v=itVi9bkJUNc).
