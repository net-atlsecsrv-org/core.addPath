---
title:  "Two-Class Decision Forest: Module Reference"
titleSuffix: Azure Machine Learning
description: Learn how to use the Two-Class Decision Forest module in Azure Machine Learning to create a machine learning model based on the decision forests algorithm.  
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference

author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
---
# Two-Class Decision Forest module

This article describes a module in Azure Machine Learning designer (preview).

Use this module to create a machine learning model based on the decision forests algorithm.  

Decision forests are fast, supervised ensemble models. This module is a good choice if you want to predict a target with a maximum of two outcomes. 

## Understanding decision forests

This decision forest algorithm is an ensemble learning method intended for classification tasks. Ensemble methods are based on the general principle that rather than relying on a single model, you can get better results and a more generalized model by creating multiple related models and combining them in some way. Generally, ensemble models provide better coverage and accuracy than single decision trees. 

There are many ways to create individual models and combine them in an ensemble. This particular implementation of a decision forest works by building multiple decision trees and then **voting** on the most popular output class. Voting is one of the better-known methods for generating results in an ensemble model. 

+ Many individual classification trees are created, using the entire dataset, but different (usually randomized) starting points. This differs from the random forest approach, in which the individual decision trees might only use some randomized portion of the data or features.
+ Each tree in the decision forest tree outputs a non-normalized frequency histogram of labels. 
+ The aggregation process sums these histograms and normalizes the result to get the “probabilities” for each label. 
+ The trees that have high prediction confidence will have a greater weight in the final decision of the ensemble.

Decision trees in general have many advantages for classification tasks:
  
- They can capture non-linear decision boundaries.
- You can train and predict on lots of data, as they are efficient in computation and memory usage.
- Feature selection is integrated in the training and classification processes.  
- Trees can accommodate noisy data and many features.  
- They are non-parametric models, meaning they can handle data with varied distributions. 

However, simple decision trees can overfit on data, and are less generalizable than tree ensembles.

For more information, see [Decision Forests](https://go.microsoft.com/fwlink/?LinkId=403677).  

## How to configure
  
1.  Add the **Two-Class Decision Forest** module to your pipeline in Azure Machine Learning, and open the **Properties** pane of the module. 

    You can find the module under **Machine Learning**. Expand **Initialize**, and then **Classification**.  
  
2.  For **Resampling method**, choose the method used to create the individual trees.  You can choose from **Bagging** or **Replicate**.  
  
    -   **Bagging**: Bagging is also called *bootstrap aggregating*. In this method, each tree is grown on a new sample, created by randomly sampling the original dataset with replacement until you have a dataset the size of the original.  
  
         The outputs of the models are combined by *voting*, which is a form of aggregation. Each tree in a classification decision forest outputs an unnormalized frequency histogram of labels. The aggregation is to sum these histograms and normalize to get the “probabilities” for each label. In this manner, the trees that have high prediction confidence will have a greater weight in the final decision of the ensemble.  
  
         For more information, see the Wikipedia entry for Bootstrap aggregating.  
  
    -   **Replicate**: In replication, each tree is trained on exactly the same input data. The determination of which split predicate is used for each tree node remains random and the trees will be diverse.   
  
3.  Specify how you want the model to be trained, by setting the **Create trainer mode** option.  
  
    -   **Single Parameter**: If you know how you want to configure the model, you can provide a specific set of values as arguments.
  
4.  For **Number of decision trees**, type the maximum number of decision trees that can be created in the ensemble. By creating more decision trees, you can potentially get better coverage, but training time increases.  
  
    > [!NOTE]
    >  This value also controls the number of trees displayed when visualizing the trained model. If you want to see or print a single tree, you can set the value to 1. However, only one tree can be produced (the tree with the initial set of parameters) and no further iterations are performed.
  
5.  For **Maximum depth of the decision trees**, type a number to limit the maximum depth of any decision tree. Increasing the depth of the tree might increase precision, at the risk of some overfitting and increased training time.
  
6.  For **Number of random splits per node**, type the number of splits to use when building each node of the tree. A *split* means that features in each level of the tree (node) are randomly divided.
  
7.  For **Minimum number of samples per leaf node**, indicate the minimum number of cases that are required to create any terminal node (leaf) in a tree.
  
     By increasing this value, you increase the threshold for creating new rules. For example, with the default value of 1, even a single case can cause a new rule to be created. If you increase the value to 5, the training data would have to contain at least five cases that meet the same conditions.  
  
8.  Select the **Allow unknown values for categorical features** option to create a group for unknown values in the training or validation sets. The model might be less precise for known values, but it can provide better predictions for new (unknown) values. 

     If you deselect this option, the model can accept only the values that are contained in the training data.
  
9. Attach a labeled dataset, and one of the [training modules](module-reference.md):  
  
    -   If you set **Create trainer mode** to **Single Parameter**, use the [Train Model](./train-model.md) module.  
  
    
## Results

After training is complete:

+ To see the tree that was created on each iteration, right-click the output of the [Train Model](./train-model.md) module, and select **Visualize**.
  
    Click each tree to drill down into the splits and see the rules for each node.

+ To save a snapshot of the model, right-click the **Trained Model** output, and select **Save Model**. The saved model is not updated on successive runs of the pipeline.

+ To use the model for scoring, add the **Score Model** module to a pipeline.

## Next steps

See the [set of modules available](module-reference.md) to Azure Machine Learning. 