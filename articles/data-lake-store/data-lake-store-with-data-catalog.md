---
title: Integrate Data Lake Storage Gen1 with Azure Data Catalog
description: Learn how to register data from Azure Data Lake Storage Gen1 in Azure Data Catalog to make data discoverable in your organization.

author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley

---
# Register data from Azure Data Lake Storage Gen1 in Azure Data Catalog
In this article, you will learn how to integrate Azure Data Lake Storage Gen1 with Azure Data Catalog to make your data discoverable within an organization by integrating it with Data Catalog. For more information on cataloging data, see [Azure Data Catalog](../data-catalog/overview.md). To understand scenarios in which you can use Data Catalog, see [Azure Data Catalog common scenarios](../data-catalog/data-catalog-common-scenarios.md).

## Prerequisites
Before you begin this tutorial, you must have the following:

* **An Azure subscription**. See [Get Azure free trial](https://azure.microsoft.com/pricing/free-trial/).
* **Enable your Azure subscription** for Data Lake Storage Gen1. See [instructions](data-lake-store-get-started-portal.md).
* **A Data Lake Storage Gen1 account**. Follow the instructions at [Get started with Azure Data Lake Storage Gen1 using the Azure portal](data-lake-store-get-started-portal.md). For this tutorial, create a Data Lake Storage Gen1 account called **datacatalogstore**.

    Once you have created the account, upload a sample data set to it. For this tutorial, let us upload all the .csv files under the **AmbulanceData** folder in the [Azure Data Lake Git Repository](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/). You can use various clients, such as [Azure Storage Explorer](https://storageexplorer.com/), to upload data to a blob container.
* **Azure Data Catalog**. Your organization must already have an Azure Data Catalog created for your organization. Only one catalog is allowed for each organization.

## Register Data Lake Storage Gen1 as a source for Data Catalog

> [!VIDEO https://channel9.msdn.com/Series/AzureDataLake/ADCwithADL/player]

1. Go to `https://azure.microsoft.com/services/data-catalog`, and click **Get started**.
1. Log into the Azure Data Catalog portal, and click **Publish data**.

    ![Register a data source](./media/data-lake-store-with-data-catalog/register-data-source.png "Register a data source")
1. On the next page, click **Launch Application**. This will download the application manifest file on your computer. Double-click the manifest file to start the application.
1. On the Welcome page, click **Sign in**, and enter your credentials.

    ![Welcome screen](./media/data-lake-store-with-data-catalog/welcome.screen.png "Welcome screen")
1. On the Select a Data Source page, select **Azure Data Lake Store**, and then click **Next**.

    ![Select data source](./media/data-lake-store-with-data-catalog/select-source.png "Select data source")
1. On the next page, provide the Data Lake Storage Gen1 account name that you want to register in Data Catalog. Leave the other options as default and then click **Connect**.

    ![Connect to data source](./media/data-lake-store-with-data-catalog/connect-to-source.png "Connect to data source")
1. The next page can be divided into the following segments.

    a. The **Server Hierarchy** box represents the Data Lake Storage Gen1 account folder structure. **$Root** represents the Data Lake Storage Gen1 account root, and **AmbulanceData** represents the folder created in the root of the Data Lake Storage Gen1 account.

    b. The **Available objects** box lists the files and folders under the **AmbulanceData** folder.

    c. The **Objects to be registered** box lists the files and folders that you want to register in Azure Data Catalog.

    ![Screenshot of the Microsoft Azure Data Catalog - Store Account dialog box.](./media/data-lake-store-with-data-catalog/view-data-structure.png "View data structure")
1. For this tutorial, you should register all the files in the directory. For that, click the (![move objects](./media/data-lake-store-with-data-catalog/move-objects.png "Move objects")) button to move all the files to **Objects to be registered** box.

    Because the data will be registered in an organization-wide data catalog, it is a recommended approach to add some metadata that you can later use to quickly locate the data. For example, you can add an e-mail address for the data owner (for example, one who is uploading the data) or add a tag to identify the data. The screen capture below shows a tag that you add to the data.

    ![Screenshot of the Microsoft Azure Data Catalog - Store Account dialog box with the tag that was added to the data called out.](./media/data-lake-store-with-data-catalog/view-selected-data-structure.png "View data structure")

    Click **Register**.
1. The following screen capture denotes that the data is successfully registered in the Data Catalog.

    ![Registration complete](./media/data-lake-store-with-data-catalog/registration-complete.png "View data structure")
1. Click **View Portal** to go back to the Data Catalog portal and verify that you can now access the registered data from the portal. To search the data, you can use the tag you used while registering the data.

     ![Search data in catalog](./media/data-lake-store-with-data-catalog/search-data-in-catalog.png "Search data in catalog")
1. You can now perform operations like adding annotations and documentation to the data. For more information, see the following links.

    * [Annotate data sources in Data Catalog](../data-catalog/data-catalog-how-to-annotate.md)
    * [Document data sources in Data Catalog](../data-catalog/data-catalog-how-to-documentation.md)

## See also
* [Annotate data sources in Data Catalog](../data-catalog/data-catalog-how-to-annotate.md)
* [Document data sources in Data Catalog](../data-catalog/data-catalog-how-to-documentation.md)
* [Integrate Data Lake Storage Gen1 with other Azure services](data-lake-store-integrate-with-other-services.md)