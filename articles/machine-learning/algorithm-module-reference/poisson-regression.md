---
title: "Poisson Regression: Module reference"
titleSuffix: Azure Machine Learning
description: Learn how to use the Poisson Regression module to create a Poisson regression model.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference

author: likebupt
ms.author: keli19
ms.date: 07/13/2020
---

# Poisson Regression

This article describes a module in Azure Machine Learning designer (preview).

Use this module to create a Poisson regression model in a pipeline. Poisson regression is intended for predicting numeric values, typically counts. Therefore, you should use this module to create your regression model only if the values you are trying to predict fit the following conditions:

- The response variable has a [Poisson distribution](https://en.wikipedia.org/wiki/Poisson_distribution).  

- Counts cannot be negative. The method will fail outright if you attempt to use it with negative labels.

- A Poisson distribution is a discrete distribution; therefore, it is not meaningful to use this method with non-whole numbers.

> [!TIP]
> If your target isn’t a count, Poisson regression is probably not an appropriate method. Try [other regression modules in the designer](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/module-reference#machine-learning-algorithms). 

After you have set up the regression method, you must train the model using a dataset containing examples of the value you want to predict. The trained model can then be used to make predictions.

## More about Poisson regression

Poisson regression is a special type of regression analysis that is typically used to model counts. For example, Poisson regression would be useful in these scenarios:

- Modeling the number of colds associated with airplane flights

- Estimating the number of emergency service calls during an event

- Projecting the number of customer inquiries subsequent to a promotion

- Creating contingency tables

Because the response variable has a Poisson distribution, the model makes different assumptions about the data and its probability distribution than, say, least-squares regression. Therefore, Poisson models should be interpreted differently from other regression models.

## How to configure Poisson Regression

1. Add the **Poisson Regression** module to your pipeline in Designer (preview). You can find this module under **Machine Learning Algorithms**, in the **Regression** category.

2. Add a dataset that contains training data of the correct type. 

    We recommend that you use [Normalize Data](normalize-data.md) to normalize the input dataset before using it to train the regressor.

3. In the right pane of the **Poisson Regression** module, specify how you want the model to be trained, by setting the **Create trainer mode** option.  
  
    - **Single Parameter**: If you know how you want to configure the model, provide a specific set of values as arguments.
  
    - **Parameter Range**: If you are not sure of the best parameters, do a parameter sweep using the [Tune Model Hyperparameters](tune-model-hyperparameters.md) module. The trainer iterates over multiple values you specify to find the optimal configuration.
  
4. **Optimization tolerance**: Type a value that defines the tolerance interval during optimization. The lower the value, the slower and more accurate the fitting.

5. **L1 regularization weight** and **L2 regularization weight**: Type values to use for L1 and L2 regularization. *Regularization* adds constraints to the algorithm regarding aspects of the model that are independent of the training data. Regularization is commonly used to avoid overfitting. 

    - L1 regularization is useful if the goal is to have a model that is as sparse as possible.

        L1 regularization is done by subtracting the L1 weight of the weight vector from the loss expression that the learner is trying to minimize. The L1 norm is a good approximation to the L0 norm, which is the number of non-zero coordinates.

    - L2 regularization prevents any single coordinate in the weight vector from growing too much in magnitude. L2 regularization is useful if the goal is to have a model with small overall weights.

    In this module, you can apply a combination of L1 and L2 regularizations. By combining L1 and L2 regularization, you can impose a penalty on the magnitude of the parameter values. The learner tries to minimize the penalty, in a tradeoff with minimizing the loss.

    For a good discussion of L1 and L2 regularization, see [L1 and L2 Regularization for Machine Learning](https://msdn.microsoft.com/magazine/dn904675.aspx).

6. **Memory size for L-BFGS**: Specify the amount of memory to reserve for model fitting and optimization.

     L-BFGS is a specific method for optimization, based on the Broyden–Fletcher–Goldfarb–Shanno (BFGS) algorithm. The method uses a limited amount of memory (L) to compute the next step direction.

     By changing this parameter, you can affect the number of past positions and gradients that are stored for computation of the next step.

7. Connect the training dataset and the untrained model to one of the training modules: 

    - If you set **Create trainer mode** to **Single Parameter**, use the [Train Model](train-model.md) module.

    - If you set **Create trainer mode** to **Parameter Range**, use the [Tune Model Hyperparameters](tune-model-hyperparameters.md) module.

    > [!WARNING]
    > 
    > - If you pass a parameter range to [Train Model](train-model.md), it uses only the first value in the parameter range list.
    > 
    > - If you pass a single set of parameter values to the [Tune Model Hyperparameters](tune-model-hyperparameters.md) module, when it expects a range of settings for each parameter, it ignores the values and uses the default values for the learner.
    > 
    > - If you select the **Parameter Range** option and enter a single value for any parameter, that single value you specified is used throughout the sweep, even if other parameters change across a range of values.

8.  Submit the pipeline.

## Results

After training is complete:

+ To save a snapshot of the trained model, select the training module, then switch to **Outputs+logs** tab in the right panel. Click on the icon **Register dataset**.  You can find the saved model as a module in the module tree. 

## Next steps

See the [set of modules available](module-reference.md) to Azure Machine Learning. 
