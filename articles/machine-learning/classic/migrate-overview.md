---
title: 'ML Studio (classic): Migrate to Azure Machine Learning'
description: Migrate from Studio (classic) to Azure Machine Learning for a modernized data science platform.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to

author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
---

# Migrate to Azure Machine Learning

In this article, you learn how to migrate Studio (classic) assets to Azure Machine Learning. At this time, to migrate resources, you must manually rebuild your experiments.

Azure Machine Learning provides a modernized data science platform that combines no-code and code-first approaches. To learn more about the differences between Studio (classic) and Azure Machine Learning, see the [Assess Azure Machine Learning](#step-1-assess-azure-machine-learning) section.


## Recommended approach

To migrate to Azure Machine Learning, we recommend the following approach:

> [!div class="checklist"]
> * Step 1: Assess Azure Machine Learning
> * Step 2: Create a migration plan
> * Step 3: Rebuild experiments and web services
> * Step 4: Integrate client apps
> * Step 5: Clean up Studio (classic) assets


## Step 1: Assess Azure Machine Learning
1. Learn about [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/); it's benefits, costs, and architecture.

1. Compare the capabilities of Azure Machine Learning and Studio (classic).

    >[!NOTE]
    > The **designer** feature in Azure Machine Learning provides a similar drag-and-drop experience to Studio (classic). However, Azure Machine Learning also provides robust [code-first workflows](../concept-model-management-and-deployment.md) as an alternative. This migration series focuses on the designer, since it's most similar to the Studio (classic) experience.

    [!INCLUDE [aml-compare-classic](../../../includes/machine-learning-compare-classic-aml.md)]

3. Verify that your critical Studio (classic) modules are supported in Azure Machine Learning designer. For more information, see the [Studio (classic) and designer module-mapping](#studio-classic-and-designer-module-mapping) table below.

4. [Create an Azure Machine Learning workspace](../how-to-manage-workspace.md?tabs=azure-portal).

## Step 2: Create a migration plan

1. Identify the Studio (classic) **data sets**, **models**, and **web services** that you want to migrate.

1. Determine the impact that a migration will have on your business.

1. Create a migration plan.

## Step 3: Rebuild experiments and web services

1. [Migrate datasets to Azure Machine Learning](migrate-register-dataset.md).
1. Use the designer to [rebuild experiments](migrate-rebuild-experiment.md).
1. Use the designer to [redeploy web services](migrate-rebuild-web-service.md).

    >[!NOTE]
    > Azure Machine Learning also supports code-first workflows for [datasets](../how-to-create-register-datasets.md), [training](../how-to-set-up-training-targets.md), and [deployment](../how-to-deploy-and-where.md).

## Step 4: Integrate client apps

1. Modify client applications that invoke Studio (classic) web services to use your new [Azure Machine Learning endpoints](migrate-rebuild-integrate-with-client-app.md).

## Step 5: Cleanup Studio (classic) assets

1. [Clean up Studio (classic) assets](export-delete-personal-data-dsr.md) to avoid extra charges. You may want to retain assets for fallback until you have validated Azure Machine Learning workloads.

## Studio (classic) and designer module-mapping

Consult the following table to see you which modules to use while rebuilding Studio (classic) experiments in the designer.


> [!IMPORTANT]
> The designer implements modules through open-source Python packages rather than C# packages like Studio (classic). Because of this difference, the output of designer modules may vary slightly from their Studio (classic) counterparts.


|Category|Studio (classic) module|Replacement designer module|
|--------------|----------------|--------------------------------------|
|Data input and output|- Enter Data Manually </br> - Export Data </br> - Import Data </br> - Load Trained Model </br> - Unpack Zipped Datasets|- Enter Data Manually </br> - Export Data </br> - Import Data|
|Data Format Conversions|- Convert to CSV </br> - Convert to Dataset </br> - Convert to ARFF </br> - Convert to SVMLight </br> - Convert to TSV|- Convert to CSV </br> - Convert to Dataset|
|Data Transformation - Manipulation|- Add Columns</br> - Add Rows </br> - Apply SQL Transformation </br> - Cleaning Missing Data </br> - Convert to Indicator Values </br> - Edit Metadata </br> - Join Data </br> - Remove Duplicate Rows </br> - Select Columns in Dataset </br> - Select Columns Transform </br> - SMOTE </br> - Group Categorical Values|- Add Columns</br> - Add Rows </br> - Apply SQL Transformation </br> - Cleaning Missing Data </br> - Convert to Indicator Values </br> - Edit Metadata </br> - Join Data </br> - Remove Duplicate Rows </br> - Select Columns in Dataset </br> - Select Columns Transform </br> - SMOTE|
|Data Transformation – Scale and Reduce |- Clip Values </br> - Group Data into Bins </br> - Normalize Data </br>- Principal Component Analysis |- Clip Values </br> - Group Data into Bins </br> - Normalize Data|
|Data Transformation – Sample and Split|- Partition and Sample </br> - Split Data|- Partition and Sample </br> - Split Data|
|Data Transformation – Filter |- Apply Filter </br> - FIR Filter </br> - IIR Filter </br> - Median Filter </br> - Moving Average Filter </br> - Threshold Filter </br> - User Defined Filter||
|Data Transformation – Learning with Counts |- Build Counting Transform </br> - Export Count Table </br> - Import Count Table </br> - Merge Count Transform</br>  - Modify Count Table Parameters||
|Feature Selection |- Filter Based Feature Selection </br> - Fisher Linear Discriminant Analysis  </br> - Permutation Feature Importance |- Filter Based Feature Selection </br>  - Permutation Feature Importance|
| Model - Classification| - Multiclass Decision Forest </br> - Multiclass Decision Jungle  </br> - Multiclass Logistic Regression  </br>- Multiclass Neural Network  </br>- One-vs-All Multiclass </br>- Two-Class Averaged Perceptron </br>- Two-Class Bayes Point Machine </br>- Two-Class Boosted Decision Tree  </br> - Two-Class Decision Forest  </br> - Two-Class Decision Jungle  </br> - Two-Class Locally-Deep SVM </br> - Two-Class Logistic Regression  </br> - Two-Class Neural Network </br> - Two-Class Support Vector Machine  | - Multiclass Decision Forest </br>  - Multiclass Boost Decision Tree  </br> - Multiclass Logistic Regression </br> - Multiclass Neural Network </br> - One-vs-All Multiclass  </br> - Two-Class Averaged Perceptron  </br> - Two-Class Boosted Decision Tree  </br> - Two-Class Decision Forest </br>-  Two-Class Logistic Regression </br> - Two-Class Neural Network </br>-   Two-Class Support Vector Machine  |
| Model - Clustering| - K-means clustering| - K-means clustering|
| Model - Regression| - Bayesian Linear Regression  </br> - Boosted Decision Tree Regression  </br>- Decision Forest Regression  </br> - Fast Forest Quantile Regression  </br> - Linear Regression </br> - Neural Network Regression </br> - Ordinal Regression  Poisson Regression| - Boosted Decision Tree Regression  </br>- Decision Forest Regression  </br> - Fast Forest Quantile Regression </br> - Linear Regression  </br> - Neural Network Regression </br> - Poisson Regression|
| Model – Anomaly Detection| - One-Class SVM  </br> - PCA-Based Anomaly Detection | - PCA-Based Anomaly Detection|
| Machine Learning – Evaluate  | - Cross Validate Model  </br>- Evaluate Model  </br>- Evaluate Recommender | - Cross Validate Model  </br>- Evaluate Model </br> - Evaluate Recommender|
| Machine Learning – Train| - Sweep Clustering  </br> - Train Anomaly Detection Model </br>- Train Clustering Model  </br> - Train Matchbox Recommender  -</br> Train Model  </br>- Tune Model Hyperparameters| - Train Anomaly Detection Model  </br> - Train Clustering Model </br> -  Train Model  -</br> - Train PyTorch Model  </br>- Train SVD Recommender  </br>- Train Wide and Deep Recommender </br>- Tune Model Hyperparameters|
| Machine Learning – Score| - Apply Transformation  </br>- Assign Data to clusters  </br>- Score Matchbox Recommender </br> - Score Model|-  Apply Transformation  </br> - Assign Data to clusters </br> - Score Image Model  </br> - Score Model </br>- Score SVD Recommender </br> -Score Wide and Deep Recommender|
| OpenCV Library Modules| - Import Images </br>- Pre-trained Cascade Image Classification | |
| Python Language Modules| - Execute Python Script| - Execute Python Script  </br> - Create Python Model |
| R Language Modules  | - Execute R Script  </br> - Create R Model| - Execute R Script|
| Statistical Functions | - Apply Math Operation </br>-  Compute Elementary Statistics  </br>- Compute Linear Correlation  </br>- Evaluate Probability Function  </br>- Replace Discrete Values  </br>- Summarize Data  </br>- Test Hypothesis using t-Test| - Apply Math Operation  </br>- Summarize Data|
| Text Analytics| - Detect Languages  </br>- Extract Key Phrases from Text  </br>- Extract N-Gram Features from Text  </br>- Feature Hashing </br>- Latent Dirichlet Allocation  </br>- Named Entity Recognition </br>-  Preprocess Text  </br>- Score Vowpal Wabbit Version 7-10 Model  </br>- Score Vowpal Wabbit Version 8 Model </br>- Train Vowpal Wabbit Version 7-10 Model  </br>- Train Vowpal Wabbit Version 8 Model |-  Convert Word to Vector </br> - Extract N-Gram Features from Text </br>-  Feature Hashing  </br>- Latent Dirichlet Allocation </br>- Preprocess Text  </br>- Score Vowpal Wabbit Model </br> - Train Vowpal Wabbit Model|
| Time Series| - Time Series Anomaly Detection | |
| Web Service | - Input </br> -   Output | - Input </br>  - Output|
| Computer Vision| | - Apply Image Transformation </br> - Convert to Image Directory </br> - Init Image Transformation </br> - Split Image Directory  </br> - DenseNet Image Classification   </br>- ResNet Image Classification |

For more information on how to use individual the designer modules, see the [designer module reference](../algorithm-module-reference/module-reference.md).

### What if a designer module is missing?

Azure Machine Learning designer contains the most popular modules from Studio (classic). It also includes new modules that take advantage of the latest machine learning techniques. 

If your migration is blocked due to missing modules in the designer, contact us by [creating a support ticket](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## Example migration

The following experiment migration highlights some of the differences between Studio (classic) and Azure Machine Learning.

### Datasets

In Studio (classic), **datasets** were saved in your workspace and could only be used by Studio (classic).

![automobile-price-classic-dataset](./media/migrate-overview/studio-classic-dataset.png)

In Azure Machine Learning, **datasets** are registered to the workspace and can be used across all of Azure Machine Learning. For more information on the benefits of Azure Machine Learning datasets, see [Secure data access](../concept-data.md#reference-data-in-storage-with-datasets).

![automobile-price-aml-dataset](./media/migrate-overview/aml-dataset.png)

### Pipeline

In Studio (classic), **experiments** contained the processing logic for your work. You created experiments with drag-and-drop modules.


![automobile-price-classic-experiment](./media/migrate-overview/studio-classic-experiment.png)

In Azure Machine Learning, **pipelines** contain the processing logic for your work. You can create pipelines with either drag-and-drop modules or by writing code.

![automobile-price-aml-pipeline](./media/migrate-overview/aml-pipeline.png)

### Web service endpoint

In Studio (classic), the **REQUEST/RESPOND API** was used for real-time prediction. The **BATCH EXECUTION API** was used for batch prediction or retraining.

![automobile-price-classic-webservice](./media/migrate-overview/studio-classic-web-service.png)

In Azure Machine Learning, **real-time endpoints** are used for real-time prediction. **Pipeline endpoints** are used for  batch prediction or retraining.

![automobile-price-aml-endpoint](./media/migrate-overview/aml-endpoint.png)


## Next steps

In this article, you learned the high-level requirements for migrating to Azure Machine Learning. For detailed steps, see the other articles in the Studio (classic) migration series:

1. **Migration overview**.
1. [Migrate dataset](migrate-register-dataset.md).
1. [Rebuild a Studio (classic) training pipeline](migrate-rebuild-experiment.md).
1. [Rebuild a Studio (classic) web service](migrate-rebuild-web-service.md).
1. [Integrate an Azure Machine Learning web service with client apps](migrate-rebuild-integrate-with-client-app.md).
1. [Migrate Execute R Script](migrate-execute-r-script.md).