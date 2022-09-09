---
title:  "Import Data: Module Reference"
titleSuffix: Azure Machine Learning
description: Learn  how to use the Import Data module in Azure Machine Learning to load data into a machine learning pipeline from existing cloud data services.  
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference

author: likebupt
ms.author: keli19
ms.date: 11/13/2020
---
# Import Data module

This article describes a module in Azure Machine Learning designer.

Use this module to load data into a machine learning pipeline from existing cloud data services. 

> [!Note]
> All functionality provided by this module can be done by **datastore** and **datasets** in the worksapce landing page. We recommend you use **datastore** and **dataset** which includes additional features like data monitoring. To learn more, see [How to Access Data](../how-to-access-data.md) and [How to Register Datasets](../how-to-create-register-datasets.md) article.
>  After you register a dataset, you can find it in the **Datasets** -> **My Datasets** category in designer interface. This module is reserved for Studio(classic) users to for a familiar experience. 
>

The **Import Data** module support read data from following sources:

- URL via HTTP
- Azure cloud storages through [**Datastores**](../how-to-access-data.md))
    - Azure Blob Container
    - Azure File Share
    - Azure Data Lake
    - Azure Data Lake Gen2
    - Azure SQL Database
    - Azure PostgreSQL    

Before using cloud storage, you need to register a datastore in your Azure Machine Learning workspace first. For more information, see [How to Access Data](../how-to-access-data.md). 

After you define the data you want and connect to the source, **[Import Data](./import-data.md)** infers the data type of each column based on the values it contains, and loads the data into your designer pipeline. The output of **Import Data** is a dataset that can be used with any designer pipeline.

If your source data changes, you can refresh the dataset and add new data by rerunning [Import Data](./import-data.md).

> [!WARNING]
> If your workspace is in a virtual network, you must configure your datastores to use the designer's data visualization features. For more information on how to use datastores and datasets in a virtual network, see [Use Azure Machine Learning studio in an Azure virtual network](../how-to-enable-studio-virtual-network.md).


## How to configure Import Data

1. Add the **Import Data** module to your pipeline. You can find this module in the **Data Input and Output** category in the designer.

1. Select the module to open the right pane.

1. Select **Data source**, and choose the data source type. It could be HTTP or datastore.

    If you choose datastore, you can select existing datastores that already registered to your Azure Machine Learning workspace or create a new datastore. Then define the path of data to import in the datastore. You can easily browse the path by click **Browse Path**
    ![Screenshot shows the Browse path link which opens the Path selection dialog box.](media/module/import-data-path.png)

    > [!NOTE]
    > **Import Data** module is for **Tabular** data only.
    > If you want to import multiple tabular data files one time, it requires following conditions, otherwise errors will occur:
    > 1. To include all data files in the folder, you need to input `folder_name/**` for **Path**.
    > 2. All data files must be encoded in unicode-8.
    > 3. All data files must have the same column numbers and column names.
    > 4. The result of importing multiple data files is concatenating all rows from multiple files in order.

1. Select the preview schema to filter the columns you want to include. You can also define advanced settings like Delimiter in Parsing options.

    ![import-data-preview](media/module/import-data.png)

1. The checkbox, **Regenerate output**, decides whether to execute the module to regenerate output at running time. 

    It's by default unselected, which means if the module has been executed with the same parameters previously, the system will reuse the output from last run to reduce run time. 

    If it is selected, the system will execute the module again to regenerate output. So select this option when underlying data in storage is updated, it can help to get the latest data.


1. Submit the pipeline.

    When Import Data loads the data into the designer, it infers the data type of each column based on the values it contains, either numerical or categorical.

    If a header is present, the header is used to name the columns of the output dataset.

    If there are no existing column headers in the data, new column names are generated using the format col1, col2,… , coln*.

## Results

When import completes, right-click the output dataset and select **Visualize** to see if the data was imported successfully.

If you want to save the data for reuse, rather than importing a new set of data each time the pipeline is run, select the **Register dataset** icon under the **Outputs+logs** tab in the right panel of the module. Choose a name for the dataset. The saved dataset preserves the data at the time of saving, the dataset is not updated when the pipeline is rerun, even if the dataset in the pipeline changes. This can be useful for taking snapshots of data.

After importing the data, it might need some additional preparations for modeling and analysis:

- Use [Edit Metadata](./edit-metadata.md) to change column names, to handle a column as a different data type, or to indicate that some columns are labels or features.

- Use [Select Columns in Dataset](./select-columns-in-dataset.md) to select a subset of columns to transform or use in modeling. The transformed or removed columns can easily be rejoined to the original dataset by using the [Add Columns](./add-columns.md) module.  

- Use [Partition and Sample](./partition-and-sample.md) to divide the dataset, perform sampling, or get the top n rows.

## Next steps

See the [set of modules available](module-reference.md) to Azure Machine Learning. 
