---
title: "Visual interface example #5: Classification to predict churn + appetency + up-selling"
titleSuffix: Azure Machine Learning
description: This visual interface sample pipeline shows binary classifier prediction of churn, a common task for customer relationship management (CRM).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 09/23/2019
---

# Sample 5 - Classification: Predict churn, appetency, and up-selling 

Learn how to build a complex machine learning pipeline without writing a single line of code using the visual interface.

This pipeline trains three, **two-class boosted decision tree** classifiers to predict common tasks for customer relationship management (CRM) systems: churn, appetency, and up-selling. The data values and labels are split across multiple data sources and scrambled to anonymize customer information, however, we can still use the visual interface to combine data sets and train a model using the obscured values.

Because you're trying to answer the question "Which one?" this is called a classification problem, but you can apply the same logic shown in this sample to tackle any type of machine learning problem whether it be regression, classification, clustering, and so on.

Here's the completed graph for this pipeline:

![Pipeline graph](./media/how-to-ui-sample-classification-predict-churn/pipeline-graph.png)

## Prerequisites

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Select the **Open** button for the Sample 5 pipeline.

    ![Open the pipeline](media/how-to-ui-sample-classification-predict-churn/open-sample5.png)

## Data

The data for this pipeline is from KDD Cup 2009. It has 50,000 rows and 230 feature columns. The task is to predict churn, appetency, and up-selling for customers who use these features. For more information about the data and the task, see the [KDD website](https://www.kdd.org/kdd-cup/view/kdd-cup-2009).

## Pipeline summary

This visual interface sample pipeline shows binary classifier prediction of churn, appetency, and up-selling, a common task for customer relationship management (CRM).

First, some simple data processing.

- The raw dataset has many missing values. Use the **Clean Missing Data** module to replace the missing values with 0.

    ![Clean the dataset](./media/how-to-ui-sample-classification-predict-churn/cleaned-dataset.png)

- The features and the corresponding churn, appetency, and up-selling labels are in different datasets. Use the **Add Columns** module to append the label columns to the feature columns. The first column, **Col1**, is the label column. The rest of the columns, **Var1**, **Var2**, and so on, are the feature columns.

    ![Add the column dataset](./media/how-to-ui-sample-classification-predict-churn/added-column1.png)

- Use the **Split Data** module to split the dataset into train and test sets.

- Then use the Boosted Decision Tree binary classifier with the default parameters to build the prediction models. Build one model per task, that is, one model each to predict up-selling, appetency, and churn.

## Results

Visualize the output of the **Evaluate Model** module to see the performance of the model on the test set. For the up-selling task, the ROC curve shows that the model does better than a random model. The area under the curve (AUC) is 0.857. At threshold 0.5, the precision is 0.7, the recall is 0.463, and the F1 score is 0.545.

![Evaluate the results](./media/how-to-ui-sample-classification-predict-churn/evaluate-result.png)

 You can move the **Threshold** slider and see the metrics change for the binary classification task.

## Clean up resources

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## Next steps

Explore the other samples available for the visual interface:

- [Sample 1 - Regression: Predict an automobile's price](how-to-ui-sample-regression-predict-automobile-price-basic.md)
- [Sample 2 - Regression: Compare algorithms for automobile price prediction](how-to-ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Sample 3 - Classification: Predict credit risk](how-to-ui-sample-classification-predict-credit-risk-basic.md)
- [Sample 4 - Classification: Predict credit risk (cost sensitive)](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Sample 6 - Classification: Predict flight delays](how-to-ui-sample-classification-predict-flight-delay.md)
- [Sample 7 - Text Classification: Books reviews](how-to-ui-sample-text-classification.md)
