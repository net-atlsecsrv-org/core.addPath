---
title:  "Evaluate Model: Module Reference"
titleSuffix: Azure Machine Learning
description: Learn how to use the Evaluate Model module in Azure Machine Learning to measure the accuracy of a trained model.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference

author: likebupt
ms.author: keli19
ms.date: 02/24/2020
---
# Evaluate Model module

This article describes a module in Azure Machine Learning designer (preview).

Use this module to measure the accuracy of a trained model. You provide a dataset containing scores generated from a model, and the **Evaluate Model** module computes a set of industry-standard evaluation metrics.
  
 The metrics returned by **Evaluate Model** depend on the type of model that you are evaluating:  
  
-   **Classification Models**    
-   **Regression Models**  
-   **Clustering Models**  


> [!TIP]
> If you are new to model evaluation, we recommend the video series by Dr. Stephen Elston, as part of the [machine learning course](https://blogs.technet.microsoft.com/machinelearning/2015/09/08/new-edx-course-data-science-machine-learning-essentials/) from EdX. 


There are three ways to use the **Evaluate Model** module:

+ Generate scores over your training data, and evaluate the model based on these scores
+ Generate scores on the model, but compare those scores to scores on a reserved testing set
+ Compare scores for two different but related models, using the same set of data

## Use the training data

To evaluate a model, you must connect a dataset that contains a set of input columns and scores.  If no other data is available, you can use your original dataset.

1. Connect the **Scored dataset** output of the [Score Model](./score-model.md) to the input of **Evaluate Model**. 
2. Click **Evaluate Model** module, and run the pipeline to generate the evaluation scores.

## Use testing data

A common scenario in machine learning is to separate your original data set into training and testing datasets, using the [Split](./split-data.md) module, or the [Partition and Sample](./partition-and-sample.md) module. 

1. Connect the **Scored dataset** output of the [Score Model](score-model.md) to the input of **Evaluate Model**. 
2. Connect the output of the Split Data module that contains the testing data to the right-hand input of **Evaluate Model**.
2. Click **Evaluate Model** module, and select **Run selected** to generate the evaluation scores.

## Compare scores from two models

You can also connect a second set of scores to **Evaluate Model**.  The scores might be a shared evaluation set that has known results, or a set of results from a different model for the same data.

This feature is useful because you can easily compare results from two different models on the same data. Or, you might compare scores from two different runs over the same data with different parameters.

1. Connect the **Scored dataset** output of the [Score Model](score-model.md) to the input of **Evaluate Model**. 
2. Connect the output of the Score Model module for the second model to the right-hand input of **Evaluate Model**.
3. Submit the pipeline.

## Results

After you run **Evaluate Model**, right-click the module and select **Visualize Evaluation results** to see the results.

If you connect datasets to both inputs of **Evaluate Model**, the results will contain metrics for both set of data, or both models.
The model or data attached to the left port is presented first in the report, followed by the metrics for the dataset, or model attached on the right port.  

For example, the following image represents a comparison of results from two clustering models that were built on the same data, but with different parameters.  

![Comparing2Models](media/module/evaluate-2-models.png)  

Because this is a clustering model, the evaluation results are different than if you compared scores from two regression models, or compared two classification models. However, the overall presentation is the same. 

## Metrics

This section describes the metrics returned for the specific types of models supported for use with **Evaluate Model**:

+ [classification models](#metrics-for-classification-models)
+ [regression models](#metrics-for-regression-models)
+ [clustering models](#metrics-for-clustering-models)

### Metrics for classification models

The following metrics are reported when evaluating classification models.
  
-   **Accuracy** measures the goodness of a classification model as the proportion of true results to total cases.  
  
-   **Precision** is the proportion of true results over all positive results.  
  
-   **Recall** is the fraction of all correct results returned by the model.  
  
-   **F-score** is computed as the weighted average of precision and recall between 0 and 1, where the ideal F-score value is 1.  
  
-   **AUC** measures the area under the curve plotted with true positives on the y axis and false positives on the x axis. This metric is useful because it provides a single number that lets you compare models of different types.  
  
- **Average log loss** is a single score used to express the penalty for wrong results. It is calculated as the difference between two probability distributions – the true one, and the one in the model.  
  
- **Training log loss** is a single score that represents the advantage of the classifier over a random prediction. The log loss measures the uncertainty of your model by comparing the probabilities it outputs to the known values (ground truth) in the labels. You want to minimize log loss for the model as a whole.

### Metrics for regression models
 
The metrics returned for regression models are designed to estimate the amount of error.  A model is considered to fit the data well if the difference between observed and predicted values is small. However, looking at the pattern of the residuals (the difference between any one predicted point and its corresponding actual value) can tell you a lot about potential bias in the model.  
  
 The following metrics are reported for evaluating regression models.
  
- **Mean absolute error (MAE)** measures how close the predictions are to the actual outcomes; thus, a lower score is better.  
  
- **Root mean squared error (RMSE)** creates a single value that summarizes the error in the model. By squaring the difference, the metric disregards the difference between over-prediction and under-prediction.  
  
- **Relative absolute error (RAE)** is the relative absolute difference between expected and actual values; relative because the mean difference is divided by the arithmetic mean.  
  
- **Relative squared error (RSE)** similarly normalizes the total squared error of the predicted values by dividing by the total squared error of the actual values.  
  

  
- **Coefficient of determination**, often referred to as R<sup>2</sup>, represents the predictive power of the model as a value between 0 and 1. Zero means the model is random (explains nothing); 1 means there is a perfect fit. However, caution should be used in interpreting  R<sup>2</sup> values, as low values can be entirely normal and high values can be suspect.

###  Metrics for clustering models

Because clustering models differ significantly from classification and regression models in many respects, [Evaluate Model](evaluate-model.md) also returns a different set of statistics for clustering models.  
  
 The statistics returned for a clustering model describe how many data points were assigned to each cluster, the amount of separation between clusters, and how tightly the data points are bunched within each cluster.  
  
 The statistics for the clustering model are averaged over the entire dataset, with additional rows containing the statistics per cluster.  
  
The following metrics are reported for evaluating clustering models.
    
-   The scores in the column, **Average Distance to Other Center**, represent how close, on average, each point in the cluster is to the centroids of all other clusters.   

-   The scores in the column, **Average Distance to Cluster Center**, represent the closeness of all points in a cluster to the centroid of that cluster.  
  
-   The **Number of Points** column shows how many data points were assigned to each cluster, along with the total overall number of data points in any cluster.  
  
     If the number of data points assigned to clusters is less than the total number of data points available, it means that the data points could not be assigned to a cluster.  
  
-   The scores in the column, **Maximal Distance to Cluster Center**, represent the sum of the distances between each point and the centroid of that point’s cluster.  
  
     If this number is high, it can mean that the cluster is widely dispersed. You should review this statistic together with the **Average Distance to Cluster Center** to determine the cluster’s spread.   

-   The **Combined Evaluation** score at the bottom of the each section of results lists the averaged scores for the clusters created in that particular model.  
  

## Next steps

See the [set of modules available](module-reference.md) to Azure Machine Learning. 