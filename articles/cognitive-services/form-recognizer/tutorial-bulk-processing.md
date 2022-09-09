---
title: "Tutorial: Extract form data in bulk using Azure Data Factory - Form Recognizer"
titleSuffix: Azure Cognitive Services
description: Set up Azure Data Factory activities to trigger the training and running of Form Recognizer models to digitize a large backlog of documents.

author: PatrickFarley
manager: nitinme

ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 01/04/2021
ms.author: pafarley
---

# Tutorial: Extract form data in bulk using Azure Data Factory

In this tutorial, we'll look at how to use Azure services to ingest a large batch of forms into digital media. This tutorial will show how to automate the data ingestion from an Azure Data Lake of documents into an Azure SQL database. You'll be able to quickly train models and process new documents with a few clicks.

## Business need

Most organizations are now aware of how valuable the data they have in different formats (pdf, images, videos) is. They're looking for the best practices and most cost-effective ways to digitize those assets.

Additionally, our customers often have different types of forms coming from their many clients and customers. Unlike the [quickstarts](./quickstarts/client-library.md), this tutorial shows you how to automatically train a model with new and different types of forms using a metadata-driven approach. If you don't have an existing model for the given form type, the system will create one for you and give you the model ID. 

By extracting the data from forms and combining it with existing operational systems and data warehouses, businesses can get insights and deliver value to their customers and business users.

With Azure Form Recognizer, we help organizations harness their data, automate processes (invoice payments, tax processing, and so on), save money and time, and enjoy better data accuracy.

In this tutorial, you learn how to:

> [!div class="checklist"]
> * Set up Azure Data Lake to store your forms
> * Use an Azure database to create a parametrization table
> * Use Azure Key Vault to store sensitive credentials
> * Train your Form Recognizer model in a Databricks notebook
> * Extract your form data using a Databricks notebook
> * Automate form training and extraction with Azure Data Factory

## Prerequisites

* Azure subscription - [Create one for free](https://azure.microsoft.com/free/cognitive-services/)
* Once you have your Azure subscription, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="Create a Form Recognizer resource"  target="_blank">create a Form Recognizer resource <span class="docon docon-navigate-external x-hidden-focus"></span></a> in the Azure portal to get your key and endpoint. After it deploys, select **Go to resource**.
    * You'll need the key and endpoint from the resource you create to connect your application to the Form Recognizer API. You'll paste your key and endpoint into the code below later in the quickstart.
    * You can use the free pricing tier (`F0`) to try the service, and upgrade later to a paid tier for production.
* A set of at least five forms of the same type. Ideally, this workflow is meant to support large sets of documents. See [Build a training data set](./build-training-data-set.md) for tips and options for putting together your training data set. For this tutorial, you can use the files under the **Train** folder of the [sample data set](https://go.microsoft.com/fwlink/?linkid=2128080).

## Project architecture 

This project stands up a set of Azure Data Factory pipelines to trigger python notebooks that train, analyze, and extract data from documents in an Azure Data Lake storage account.

The Form Recognizer REST AP requires some parameters as input. For security reasons, some of these parameters will be stored in an Azure Key Vault, while other less sensitive parameters, like the storage blob folder name, will be stored in a parameterization table in an Azure SQL database.

For type of form to be analyzed, data engineers or data scientists will populate a row of the parameter table. Then they use Azure Data Factory to iterate over the list of detected form types and pass the relevant parameters to a Databricks notebook to train or retrain the Form Recognizer models. An Azure function could also be used here.

The Azure Databricks notebook then uses the trained models to extract form data, and it exports that date to an Azure SQL database.

:::image type="content" source="./media/tutorial-bulk-processing/architecture.png" alt-text="project architecture":::


## Set up Azure Data Lake

Your backlog of forms might be in your on-premise environment or in a (s)FTP server. This tutorial uses forms in an Azure Data Lake Gen 2 storage account. You can transfer your files there using Azure Data Factory, Azure Storage Explorer, or AzCopy. The training and scoring datasets can be in different containers, but the training datasets for all form types must be in the same container (though they can be in different folders).

To create a new Data Lake, follow the instructions in [Create a storage account to use with Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/create-data-lake-storage-account).

## Create a parameterization table

Next, we'll create a metadata table in an Azure SQL Database. This table will contain the non-sensitive data required by the Form Recognizer REST API. Whenever there is a new type of form in our dataset, we'll insert a new record in this table and trigger the training and scoring pipeline (to be implemented later).

The following fields will be used in the table:

* **form_description**: This field is not required as part of the training. It provides a description of the type of forms we are training the model for (for example, "client A forms," "Hotel B forms").
* **training_container_name**: This field is the storage account container name where we have stored the training dataset. It can be the same container as **scoring_container_name**.
* **training_blob_root_folder**: The folder within the storage account where we'll store the files for the training of the model.
* **scoring_container_name**: This field is the storage account container name where we've stored the files we want to extract the key value pairs from. It can be the same container as **training_container_name**.
* **scoring_input_blob_folder**: The folder in the storage account where we'll store the files to extract data from.
* **model_id**: The ID of model we want to retrain. For the first run, the value must be set to -1, which will cause training notebook to create a new custom model to train. The training notebook will return the newly created model ID to the Azure Data Factory instance and, using a stored procedure activity, we'll update this value in the Azure SQL database.

  Whenever you want to ingest a new form type, you'll need to manually reset the model ID to -1 before training the model.

* **file_type**: The supported form types are `application/pdf`, `image/jpeg`, `image/png`, and `image/tif`.

  If you have forms of different file types, you'll need to change this value and **model_id** when training a new form type.
* **form_batch_group_id**: Over time, you might have multiple form types you train against the same model. The **form_batch_group_id** will allow you to specify all the form types that have been training using a specific model.

### Create the table

[Create an Azure SQL Database](https://ms.portal.azure.com/#create/Microsoft.SQLDatabase), and then run the following SQL script in the [query editor](https://docs.microsoft.com/azure/azure-sql/database/connect-query-portal) to create the needed table.

```sql
CREATE TABLE dbo.ParamFormRecogniser(
    form_description varchar(50) NULL,
  training_container_name varchar(50) NOT NULL,
    training_blob_root_folder varchar(50) NULL,
    scoring_container_name varchar(50) NOT NULL,
    scoring_input_blob_folder varchar(50) NOT NULL,
    scoring_output_blob_folder varchar(50) NOT NULL,
    model_id varchar(50) NULL,
    file_type varchar(50) NULL
) ON PRIMARY
GO
```

Run the following script to create the procedure for automatically updating **model_id** once it is trained.

```SQL
CREATE PROCEDURE [dbo].[update_model_id] ( @form_batch_group_id  varchar(50),@model_id varchar(50))
AS
BEGIN 
    UPDATE [dbo].[ParamFormRecogniser]   
        SET [model_id] = @model_id  
    WHERE form_batch_group_id =@form_batch_group_id
END
```

## Use Azure Key Vault to store sensitive credentials

For security reasons, we don't want to store certain sensitive information in the parameterization table in the Azure SQL database. We'll store sensitive parameters as Azure Key Vault secrets.

### Create an Azure Key Vault

[Create a Key Vault resource](https://ms.portal.azure.com/#create/Microsoft.KeyVault). Then navigate to the Key Vault resource after it's created and, in the **settings** section, select **secrets** to add the parameters.

A new window will appear, select **Generate/import**. Enter the name of the parameter and its value and click create. Do this for the following parameters:

* **CognitiveServiceEndpoint**: The endpoint URL of your Form Recognizer API.
* **CognitiveServiceSubscriptionKey**: The access key for your Form Recognizer service. 
* **StorageAccountName**: The storage account where the training dataset and forms we want to extract key-value pairs from are stored. If these are in different accounts, enter each of their account names as separate secrets. Remember that the training datasets must be in the same container for all form types, but they can be in different folders.
* **StorageAccountSasKey**: the shared access signature (SAS) of the storage account. To retrieve the SAS URL, go to your storage resource and select the **Storage Explorer** tab. Navigate to your container, right-click, and select **Get shared access signature**. It's important to get the SAS for your container, not for the storage account itself. Make sure the **Read** and **List** permissions are checked, and click **Create**. Then copy the value in the **URL** section. It should have the form: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.

## Train your Form Recognizer model in a Databricks notebook

You'll use Azure Databricks to store and run the Python code that interacts with the Form Recognizer service.

### Create a notebook in Databricks

[Create an Azure Databricks resource](https://ms.portal.azure.com/#create/Microsoft.Databricks) in the Azure portal. Navigate to the resource after it has been created and launch the workspace.

### Create a secret scope backed by Azure Key Vault

To reference the secrets in the Azure Key Vault we created above, you'll need to create a secret scope in Databricks. Follow the steps under [Create an Azure Key Vault-backed secret scope](https://docs.microsoft.com/azure/databricks/security/secrets/secret-scopes#--create-an-azure-key-vault-backed-secret-scope).

### Create a Databricks cluster

A cluster is a collection of Databricks computation resources. To create a cluster:

1. In the sidebar, click the **Clusters** button.
1. On the **Clusters** page, click **Create Cluster**.
1. On the **Create Cluster** page, specify a cluster name and select **7.2 (Scala 2.12, Spark 3.0.0)** in the Databricks Runtime Version drop-down.
1. Click **Create Cluster**.

### Write a settings notebook

Now you're ready to add Python notebooks. First, create a notebook called **Settings**; this notebook will assign the values in your parameterization table to variables in the script. The values will later be passed in as parameters by Azure Data Factory. We'll also assign values from the secrets in the Key Vault to variables. 

1. To create the **Settings** notebook, click on the **workspace** button, in the new tab, click on the dropdown list and select **create** and then **notebook**.
1. In the pop-up window, enter the name you want to give to the notebook and select **Python** as default language. Select your Databricks cluster, and select **Create**.
1. In the first notebook cell, we retrieve the parameters passed by Azure Data Factory.

    ```python 
    dbutils.widgets.text("form_batch_group_id", "","")
    dbutils.widgets.get("form_batch_group_id")
    form_batch_group_id = getArgument("form_batch_group_id")
    
    dbutils.widgets.text("model_id", "","")
    dbutils.widgets.get("model_id")
    model_id = getArgument("model_id")
    
    dbutils.widgets.text("training_container_name", "","")
    dbutils.widgets.get("training_container_name")
    training_container_name = getArgument("training_container_name")
    
    dbutils.widgets.text("training_blob_root_folder", "","")
    dbutils.widgets.get("training_blob_root_folder")
    training_blob_root_folder=  getArgument("training_blob_root_folder")
    
    dbutils.widgets.text("scoring_container_name", "","")
    dbutils.widgets.get("scoring_container_name")
    scoring_container_name=  getArgument("scoring_container_name")
    
    dbutils.widgets.text("scoring_input_blob_folder", "","")
    dbutils.widgets.get("scoring_input_blob_folder")
    scoring_input_blob_folder=  getArgument("scoring_input_blob_folder")
    
    
    dbutils.widgets.text("file_type", "","")
    dbutils.widgets.get("file_type")
    file_type = getArgument("file_type")
    
    dbutils.widgets.text("file_to_score_name", "","")
    dbutils.widgets.get("file_to_score_name")
    file_to_score_name=  getArgument("file_to_score_name")
    ```

1. In the second cell, we retrieve secrets from Key Vault and assign them to variables.

    ```python 
    cognitive_service_subscription_key = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveserviceSubscriptionKey")
    cognitive_service_endpoint = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveServiceEndpoint")
    
    training_storage_account_name = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountName")
    storage_account_sas_key= dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountSasKey")  
    
    ScoredFile = file_to_score_name+ "_output.json"
    training_storage_account_url="https://"+training_storage_account_name+".blob.core.windows.net/"+training_container_name+storage_account_sas_key 
    ```

### Write a training notebook

Now that we've completed the **Settings** notebook, we can create a notebook to train the model. As mentioned above, we will use files stored in a folder in an Azure Data Lake Gen 2 storage account (**training_blob_root_folder**). The folder name has been passed in as a variable. Each set of form types will be in the same folder and, as we loop over the parameter table, we'll train the model using all of the form types. 

1. Create a new notebook called **TrainFormRecognizer**. 
1. In the first cell, execute the Settings notebook:

    ```python
    %run "./Settings"
    ```

1. In the next cell, assign variables from the **Settings** file, and dynamically train the model for each form type, applying the code in the [REST quickstart](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-train-extract.md#get-training-results%20).

    ```python
    import json
    import time
    from requests import get, post
    
    post_url = cognitive_service_endpoint + r"/formrecognizer/v2.0/custom/models"
    source = training_storage_account_url
    prefix= training_blob_root_folder
    
    includeSubFolders=True
    useLabelFile=False
    headers = {
        # Request headers
        'Content-Type': file_type,
        'Ocp-Apim-Subscription-Key': cognitive_service_subscription_key,
    }
    body =     {
        "source": source
        ,"sourceFilter": {
            "prefix": prefix,
            "includeSubFolders": includeSubFolders
       },
    }
    if model_id=="-1": # if you don't already have a model you want to retrain. In this case, we create a model and use it to extract the key-value pairs
      try:
          resp = post(url = post_url, json = body, headers = headers)
          if resp.status_code != 201:
              print("POST model failed (%s):\n%s" % (resp.status_code, json.dumps(resp.json())))
              quit()
          print("POST model succeeded:\n%s" % resp.headers)
          get_url = resp.headers["location"]
          model_id=get_url[get_url.index('models/')+len('models/'):]     
          
      except Exception as e:
          print("POST model failed:\n%s" % str(e))
          quit()
    else :# if you already have a model you want to retrain, we reuse it and (re)train with the new form types.  
      try:
        get_url =post_url+r"/"+model_id
          
      except Exception as e:
          print("POST model failed:\n%s" % str(e))
          quit()
    ```

1. The final step in the training process is to get the training result in a json format.

    ```python
    n_tries = 10
    n_try = 0
    wait_sec = 5
    max_wait_sec = 5
    while n_try < n_tries:
        try:
            resp = get(url = get_url, headers = headers)
            resp_json = resp.json()
            print (resp.status_code)
            if resp.status_code != 200:
                print("GET model failed (%s):\n%s" % (resp.status_code, json.dumps(resp_json)))
                n_try += 1
                quit()
            model_status = resp_json["modelInfo"]["status"]
            print (model_status)
            if model_status == "ready":
                print("Training succeeded:\n%s" % json.dumps(resp_json))
                n_try += 1
                quit()
            if model_status == "invalid":
                print("Training failed. Model is invalid:\n%s" % json.dumps(resp_json))
                n_try += 1
                quit()
            # Training still running. Wait and retry.
            time.sleep(wait_sec)
            n_try += 1
            wait_sec = min(2*wait_sec, max_wait_sec)     
            print (n_try)
        except Exception as e:
            msg = "GET model failed:\n%s" % str(e)
            print(msg)
            quit()
    print("Train operation did not complete within the allocated time.")
    ```

## Extract form data using a notebook

### Mount the Azure Data Lake storage

The next step is to score the different forms we have using the trained model. We'll mount the Azure Data Lake storage account in Databricks and refer to the mount during the ingesting process.

Just like in the training stage, we'll use Azure Data Factory to invoke the extraction of the key-value pairs from the forms. We'll loop over the forms in the folders specified in the parameter table.

1. Let's create the notebook to mount the storage account in Databricks. We'll call it **MountDataLake**. 
1. You'll need to call the **Settings** notebook first:

    ```python
    %run "./Settings"
    ```

1. In the second cell, we'll define variables for the sensitive parameters, which we'll retrieve from our Key Vault secrets.

    ```python
    cognitive_service_subscription_key = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveserviceSubscriptionKey")
    cognitive_service_endpoint = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveServiceEndpoint")
    
    scoring_storage_account_name = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountName")
    scoring_storage_account_sas_key= dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountSasKey")  
    
    scoring_mount_point = "/mnt/"+scoring_container_name 
    scoring_source_str = "wasbs://{container}@{storage_acct}.blob.core.windows.net/".format(container=scoring_container_name, storage_acct=scoring_storage_account_name) 
    scoring_conf_key = "fs.azure.sas.{container}.{storage_acct}.blob.core.windows.net".format(container=scoring_container_name, storage_acct=scoring_storage_account_name)
    
    ```

1. Next, we'll try to unmount the storage account in case it was previously mounted.

    ```python
    try:
      dbutils.fs.unmount(scoring_mount_point) # Use this to unmount as needed
    except:
      print("{} already unmounted".format(scoring_mount_point))
    
    ```

1. Finally, we'll mount the storage account.


    ```python
    try: 
      dbutils.fs.mount( 
        source = scoring_source_str, 
        mount_point = scoring_mount_point, 
        extra_configs = {scoring_conf_key: scoring_storage_account_sas_key} 
      ) 
    except Exception as e: 
      print("ERROR: {} already mounted. Run previous cells to unmount first".format(scoring_mount_point))
    
    ```

    > [!NOTE]
    > We only mounted the training storage account&mdash;in this case, the training files and the files we want to extract key-value pairs from are in the same storage account. If your scoring and training storage accounts are different, you will need to mount both storage accounts here. 

### Write the scoring notebook

Now we can create a scoring notebook. Similarly to the training notebook, we'll use files stored in folders in the Azure Data Lake storage account we just mounted. The folder name is passed as a variable. We'll loop over all the forms in the specified folder and extract the key-value pairs from them. 

1. Create a new notebook and call it **ScoreFormRecognizer**. 
1. Execute the **Settings** and the **MountDataLake** notebooks.

    ```python
    %run "./Settings"
    %run "./MountDataLake"
    ```

1. Then add the following code, which calls the [Analyze](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm) API.

    ```python
    ########### Python Form Recognizer Async Analyze #############
    import json
    import time
    from requests import get, post 
    
    
    #prefix= TrainingBlobFolder
    post_url = cognitive_service_endpoint + "/formrecognizer/v2.0/custom/models/%s/analyze" % model_id
    source = r"/dbfs/mnt/"+scoring_container_name+"/"+scoring_input_blob_folder+"/"+file_to_score_name
    output = r"/dbfs/mnt/"+scoring_container_name+"/scoringforms/ExtractionResult/"+os.path.splitext(os.path.basename(source))[0]+"_output.json"
    
    params = {
        "includeTextDetails": True
    }
    
    headers = {
        # Request headers
        'Content-Type': file_type,
        'Ocp-Apim-Subscription-Key': cognitive_service_subscription_key,
    }
    
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % json.dumps(resp.json()))
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit() 
    ```

1. In the next cell, we'll get the results of the key-value pair extraction. This cell will output the result. Because we want the result in JSON format to process further into our Azure SQL Database or Cosmos DB, we'll write the result to a .json file. The output file name will be the name of the scored file, concatenated with "_output.json". The file will be stored in the same folder as the source file.

    ```python
    n_tries = 10
    n_try = 0
    wait_sec = 5
    max_wait_sec = 5
    while n_try < n_tries:
       try:
           resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": cognitive_service_subscription_key})
           resp_json = resp.json()
           if resp.status_code != 200:
               print("GET analyze results failed:\n%s" % json.dumps(resp_json))
               n_try += 1
               quit()
           status = resp_json["status"]
           if status == "succeeded":
               print("Analysis succeeded:\n%s" % json.dumps(resp_json))
               n_try += 1
               quit()
           if status == "failed":
               print("Analysis failed:\n%s" % json.dumps(resp_json))
               n_try += 1
               quit()
           # Analysis still running. Wait and retry.
           time.sleep(wait_sec)
           n_try += 1
           wait_sec = min(2*wait_sec, max_wait_sec)     
       except Exception as e:
           msg = "GET analyze results failed:\n%s" % str(e)
           print(msg)
           n_try += 1
           print("Analyze operation did not complete within the allocated time.")
           quit()
    
    ```
1. Do the file writing procedure in a new cell:

    ```python
    import requests
    file = open(output, "w")
    file.write(str(resp_json))
    file.close()
    ```

## Automate training and scoring with Azure Data Factory

The only remaining step is to set up the Azure Data Factory (ADF) service to automate the training and scoring processes. First, follow the steps under [Create a data factory](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory). After you create the ADF resource, you'll need to create three pipelines: one for training and two for scoring (explained below).

### Training pipeline

The first activity in the training pipeline is a Lookup to read and return the values in the parameterization table in the Azure SQL database. As all the training datasets will be in the same storage account and container (but potentially different folders), we'll keep the default value **First row only** attribute in the lookup activity settings. For each type of form to train the model against, we'll train the model using all the files in **training_blob_root_folder**.

:::image type="content" source="./media/tutorial-bulk-processing/training-pipeline.png" alt-text="training pipeline in data factory":::

The stored procedure takes two parameters: **model_id** and the **form_batch_group_id**. The code to return the model ID from the Databricks notebook is `dbutils.notebook.exit(model_id)`, and the code to read the code in stored procedure activity in data factory is `@activity('GetParametersFromMetadaTable').output.firstRow.form_batch_group_id`.

### Scoring pipelines

To extract the key-value pairs, we'll scan all the folders in the parameterization table and, for each folder, we'll extract the key-value pairs of all the files in it. As of today, ADF does not support nested ForEach loops. So instead, we'll create two pipelines. The first pipeline will do the lookup from the parameterization table and pass the folders list as a parameter to the second pipeline.

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-1a.png" alt-text="first scoring pipeline in data factory":::

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-1b.png" alt-text="first scoring pipeline in data factory, details":::

The second pipeline will use a GetMeta activity to get the list of the files in the folder and pass it as a parameter to the scoring Databricks notebook.

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-2a.png" alt-text="second scoring pipeline in data factory":::

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-2b.png" alt-text="second scoring pipeline in data factory, details":::

### Specify a degree of parallelism

In both the training and scoring pipelines, you can specify the degree of parallelism to process multiple forms simultaneously.

To set the degree of parallelism in the ADF pipeline:

* Select the Foreach activity.
* Uncheck the **Sequential** box.
* Set the degree of parallelism in the **Batch count** text box. We recommend a maximum batch count of 15 for the scoring.

:::image type="content" source="./media/tutorial-bulk-processing/parallelism.png" alt-text="parallelism configuration for scoring activity in ADF":::

## How to use

You now have an automated pipeline to digitize your backlog of forms and run some analytics on top of it. When you add new forms of a familiar type to an existing storage folder, simply re-run the scoring pipelines and they will update all of your output files, including output files for the new forms. 

If you add new forms of a new type, you'll also need to upload a training dataset to the appropriate container. Then, add a new row in the parameterization table, entering the locations of the new documents and their training dataset. Enter a value of -1 for **model_ID** to indicate that a new model must be trained for these forms. Then run the training pipeline in ADF. It will read from the table, train a model, and overwrite the model ID in the table. Then you can call the scoring pipelines to start writing the output files.

## Next steps

In this tutorial, you set up Azure Data Factory pipelines to trigger the training and running of Form Recognizer models to digitize a large backlog of files. Next, explore the Form Recognizer API to see what else you can do with it.

* [Form Recognizer REST API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeBusinessCardAsync)
