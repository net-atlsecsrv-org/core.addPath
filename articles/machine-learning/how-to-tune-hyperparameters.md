---
title: Tune hyperparameters for your model
titleSuffix: Azure Machine Learning
description: Efficiently tune hyperparameters for deep learning and machine learning models using Azure Machine Learning.
ms.author: swatig
author: swatig007
ms.reviewer: sgilley 
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 03/30/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q1

---

# Tune hyperparameters for your model with Azure Machine Learning


Automate efficient hyperparameter tuning by using Azure Machine Learning [HyperDrive package](/python/api/azureml-train-core/azureml.train.hyperdrive?preserve-view=true&view=azure-ml-py). Learn how to complete the steps required to tune hyperparameters with the [Azure Machine Learning SDK](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py):

1. Define the parameter search space
1. Specify a primary metric to optimize  
1. Specify early termination policy for low-performing runs
1. Allocate resources
1. Launch an experiment with the defined configuration
1. Visualize the training runs
1. Select the best configuration for your model

## What are hyperparameters?

**Hyperparameters** are adjustable parameters that let you control the model training process. For example, with neural networks, you decide the number of hidden layers and the number of nodes in each layer. Model performance depends heavily on hyperparameters.

 **Hyperparameter tuning** is the process of finding the configuration of hyperparameters that results in the best performance. The process is typically computationally expensive and manual.

Azure Machine Learning lets you automate hyperparameter tuning and run experiments in parallel to efficiently optimize hyperparameters.


## Define the search space

Tune hyperparameters by exploring the range of values defined for each hyperparameter.

Hyperparameters can be discrete or continuous, and has a distribution of values described by a
[parameter expression](/python/api/azureml-train-core/azureml.train.hyperdrive.parameter_expressions?preserve-view=true&view=azure-ml-py).

### Discrete hyperparameters 

Discrete hyperparameters are specified as a `choice` among discrete values. `choice` can be:

* one or more comma-separated values
* a `range` object
* any arbitrary `list` object


```Python
    {
        "batch_size": choice(16, 32, 64, 128)
        "number_of_hidden_layers": choice(range(1,5))
    }
```

In this case, `batch_size` one of the values [16, 32, 64, 128] and `number_of_hidden_layers` takes one of the values [1, 2, 3, 4].

The following advanced discrete hyperparameters can also be specified using a distribution:

* `quniform(low, high, q)` - Returns a value like round(uniform(low, high) / q) * q
* `qloguniform(low, high, q)` - Returns a value like round(exp(uniform(low, high)) / q) * q
* `qnormal(mu, sigma, q)` - Returns a value like round(normal(mu, sigma) / q) * q
* `qlognormal(mu, sigma, q)` - Returns a value like round(exp(normal(mu, sigma)) / q) * q

### Continuous hyperparameters 

The Continuous hyperparameters are specified as a distribution over a continuous range of values:

* `uniform(low, high)` - Returns a value uniformly distributed between low and high
* `loguniform(low, high)` - Returns a value drawn according to exp(uniform(low, high)) so that the logarithm of the return value is uniformly distributed
* `normal(mu, sigma)` - Returns a real value that's normally distributed with mean mu and standard deviation sigma
* `lognormal(mu, sigma)` - Returns a value drawn according to exp(normal(mu, sigma)) so that the logarithm of the return value is normally distributed

An example of a parameter space definition:

```Python
    {    
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1)
    }
```

This code defines a search space with two parameters - `learning_rate` and `keep_probability`. `learning_rate` has a normal distribution with mean value 10 and a standard deviation of 3. `keep_probability` has a uniform distribution with a minimum value of 0.05 and a maximum value of 0.1.

### Sampling the hyperparameter space

Specify the parameter sampling method to use over the hyperparameter space. Azure Machine Learning supports the following methods:

* Random sampling
* Grid sampling
* Bayesian sampling

#### Random sampling

[Random sampling](/python/api/azureml-train-core/azureml.train.hyperdrive.randomparametersampling?preserve-view=true&view=azure-ml-py) supports discrete and continuous hyperparameters. It supports early termination of low-performance runs. Some users do an initial search with random sampling and then refine the search space to improve results.

In random sampling, hyperparameter values are randomly selected from the defined search space. 

```Python
from azureml.train.hyperdrive import RandomParameterSampling
from azureml.train.hyperdrive import normal, uniform, choice
param_sampling = RandomParameterSampling( {
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

#### Grid sampling

[Grid sampling](/python/api/azureml-train-core/azureml.train.hyperdrive.gridparametersampling?preserve-view=true&view=azure-ml-py) supports discrete hyperparameters. Use grid sampling if you can budget to exhaustively search over the search space. Supports early termination of low-performance runs.

Performs a simple grid search over all possible values. Grid sampling can only be used with `choice` hyperparameters. For example, the following space has six samples:

```Python
from azureml.train.hyperdrive import GridParameterSampling
from azureml.train.hyperdrive import choice
param_sampling = GridParameterSampling( {
        "num_hidden_layers": choice(1, 2, 3),
        "batch_size": choice(16, 32)
    }
)
```

#### Bayesian sampling

[Bayesian sampling](/python/api/azureml-train-core/azureml.train.hyperdrive.bayesianparametersampling?preserve-view=true&view=azure-ml-py) is based on the Bayesian optimization algorithm. It picks samples based on how previous samples performed, so that new samples improve the primary metric.

Bayesian sampling is recommended if you have enough budget to explore the hyperparameter space. For best results, we recommend a maximum number of runs greater than or equal to 20 times the number of hyperparameters being tuned. 

The number of concurrent runs has an impact on the effectiveness of the tuning process. A smaller number of concurrent runs may lead to better sampling convergence, since the smaller degree of parallelism increases the number of runs that benefit from previously completed runs.

Bayesian sampling only supports `choice`, `uniform`, and `quniform` distributions over the search space.

```Python
from azureml.train.hyperdrive import BayesianParameterSampling
from azureml.train.hyperdrive import uniform, choice
param_sampling = BayesianParameterSampling( {
        "learning_rate": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```



## <a name="specify-primary-metric-to-optimize"></a> Specify primary metric

Specify the [primary metric](/python/api/azureml-train-core/azureml.train.hyperdrive.primarymetricgoal?preserve-view=true&view=azure-ml-py) you want hyperparameter tuning to optimize. Each training run is evaluated for the primary metric. The early termination policy uses the primary metric to identify low-performance runs.

Specify the following attributes for your primary metric:

* `primary_metric_name`: The name of the primary metric needs to exactly match the name of the metric logged by the training script
* `primary_metric_goal`: It can be either `PrimaryMetricGoal.MAXIMIZE` or `PrimaryMetricGoal.MINIMIZE` and determines whether the primary metric will be maximized or minimized when evaluating the runs. 

```Python
primary_metric_name="accuracy",
primary_metric_goal=PrimaryMetricGoal.MAXIMIZE
```

This sample maximizes "accuracy".

### <a name="log-metrics-for-hyperparameter-tuning"></a>Log metrics for hyperparameter tuning

The training script for your model **must** log the primary metric during model training so that HyperDrive can access it for hyperparameter tuning.

Log the primary metric in your training script with the following sample snippet:

```Python
from azureml.core.run import Run
run_logger = Run.get_context()
run_logger.log("accuracy", float(val_accuracy))
```

The training script calculates the `val_accuracy` and logs it as the primary metric "accuracy". Each time the metric is logged, it's received by the hyperparameter tuning service. It's up to you to determine the frequency of reporting.

For more information on logging values in model training runs, see [Enable logging in Azure ML training runs](how-to-track-experiments.md).

## <a name="early-termination"></a> Specify early termination policy

Automatically terminate poorly performing runs with an early termination policy. Early termination improves computational efficiency.

You can configure the following parameters that control when a policy is applied:

* `evaluation_interval`: the frequency of applying the policy. Each time the training script logs the primary metric counts as one interval. An `evaluation_interval` of 1 will apply the policy every time the training script reports the primary metric. An `evaluation_interval` of 2 will apply the policy every other time. If not specified, `evaluation_interval` is set to 1 by default.
* `delay_evaluation`: delays the first policy evaluation for a specified number of intervals. This is an optional parameter that avoids premature termination of training runs by allowing all configurations to run for a minimum number of intervals. If specified, the policy applies every multiple of evaluation_interval that is greater than or equal to delay_evaluation.

Azure Machine Learning supports the following early termination policies:
* [Bandit policy](#bandit-policy)
* [Median stopping policy](#median-stopping-policy)
* [Truncation selection policy](#truncation-selection-policy)
* [No termination policy](#no-termination-policy-default)


### Bandit policy

[Bandit policy](/python/api/azureml-train-core/azureml.train.hyperdrive.banditpolicy?preserve-view=true&view=azure-ml-py#&preserve-view=truedefinition) is based on slack factor/slack amount and evaluation interval. Bandit terminates runs where the primary metric is not within the specified slack factor/slack amount compared to the best performing run.

> [!NOTE]
> Bayesian sampling does not support early termination. When using Bayesian sampling, set `early_termination_policy = None`.

Specify the following configuration parameters:

* `slack_factor` or `slack_amount`: the slack allowed with respect to the best performing training run. `slack_factor` specifies the allowable slack as a ratio. `slack_amount` specifies the allowable slack as an absolute amount, instead of a ratio.

    For example,  consider a Bandit policy applied at interval 10. Assume that the best performing run at interval 10 reported a primary metric is 0.8 with a goal to maximize the primary metric. If the policy specifies a `slack_factor` of 0.2, any training runs whose best metric at interval 10 is less than 0.66 (0.8/(1+`slack_factor`)) will be terminated.
* `evaluation_interval`: (optional) the frequency for applying the policy
* `delay_evaluation`: (optional) delays the first policy evaluation for a specified number of intervals


```Python
from azureml.train.hyperdrive import BanditPolicy
early_termination_policy = BanditPolicy(slack_factor = 0.1, evaluation_interval=1, delay_evaluation=5)
```

In this example, the early termination policy is applied at every interval when metrics are reported, starting at evaluation interval 5. Any run whose best metric is less than (1/(1+0.1) or 91% of the best performing run will be terminated.

### Median stopping policy

[Median stopping](/python/api/azureml-train-core/azureml.train.hyperdrive.medianstoppingpolicy?preserve-view=true&view=azure-ml-py) is an early termination policy based on running averages of primary metrics reported by the runs. This policy computes running averages across all training runs and terminates runs with primary metric values worse than the median of averages.

This policy takes the following configuration parameters:
* `evaluation_interval`: the frequency for applying the policy (optional parameter).
* `delay_evaluation`: delays the first policy evaluation for a specified number of intervals (optional parameter).


```Python
from azureml.train.hyperdrive import MedianStoppingPolicy
early_termination_policy = MedianStoppingPolicy(evaluation_interval=1, delay_evaluation=5)
```

In this example, the early termination policy is applied at every interval starting at evaluation interval 5. A run is terminated at interval 5 if its best primary metric is worse than the median of the running averages over intervals 1:5 across all training runs.

### Truncation selection policy

[Truncation selection](/python/api/azureml-train-core/azureml.train.hyperdrive.truncationselectionpolicy?preserve-view=true&view=azure-ml-py) cancels a percentage of lowest performing runs at each evaluation interval. Runs are compared using the primary metric. 

This policy takes the following configuration parameters:

* `truncation_percentage`: the percentage of lowest performing runs to terminate at each evaluation interval. An integer value between 1 and 99.
* `evaluation_interval`: (optional) the frequency for applying the policy
* `delay_evaluation`: (optional) delays the first policy evaluation for a specified number of intervals


```Python
from azureml.train.hyperdrive import TruncationSelectionPolicy
early_termination_policy = TruncationSelectionPolicy(evaluation_interval=1, truncation_percentage=20, delay_evaluation=5)
```

In this example, the early termination policy is applied at every interval starting at evaluation interval 5. A run terminates at interval 5 if its performance at interval 5 is in the lowest 20% of performance of all runs at interval 5.

### No termination policy (default)

If no policy is specified, the hyperparameter tuning service will let all training runs execute to completion.

```Python
policy=None
```

### Picking an early termination policy

* For a conservative policy that provides savings without terminating promising jobs, consider a Median Stopping Policy with `evaluation_interval` 1 and `delay_evaluation` 5. These are conservative settings, that can provide approximately 25%-35% savings with no loss on primary metric (based on our evaluation data).
* For more aggressive savings, use Bandit Policy with a smaller allowable slack or Truncation Selection Policy with a larger truncation percentage.

## Allocate resources

Control your resource budget by specifying the maximum number of training runs.

* `max_total_runs`: Maximum number of training runs. Must be an integer between 1 and 1000.
* `max_duration_minutes`: (optional) Maximum duration, in minutes, of the hyperparameter tuning experiment. Runs after this duration are canceled.

>[!NOTE] 
>If both `max_total_runs` and `max_duration_minutes` are specified, the hyperparameter tuning experiment terminates when the first of these two thresholds is reached.

Additionally, specify the maximum number of training runs to run concurrently during your hyperparameter tuning search.

* `max_concurrent_runs`: (optional) Maximum number of runs that can run concurrently. If not specified, all runs launch in parallel. If specified, must be an integer between 1 and 100.

>[!NOTE] 
>The number of concurrent runs is gated on the resources available in the specified compute target. Ensure that the compute target has the available resources for the desired concurrency.

```Python
max_total_runs=20,
max_concurrent_runs=4
```

This code configures the hyperparameter tuning experiment to use a maximum of 20 total runs, running four configurations at a time.

## Configure experiment

To [configure your hyperparameter tuning](/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverunconfig?preserve-view=true&view=azure-ml-py) experiment, provide the following:
* The defined hyperparameter search space
* Your early termination policy
* The primary metric
* Resource allocation settings
* ScriptRunConfig `src`

The ScriptRunConfig is the training script that will run with the sampled hyperparameters. It defines the resources per job (single or multi-node), and the compute target to use.

> [!NOTE]
>The compute target specified in `src` must have enough resources to satisfy your concurrency level. For more information on ScriptRunConfig, see [Configure training runs](how-to-set-up-training-targets.md).

Configure your hyperparameter tuning experiment:

```Python
from azureml.train.hyperdrive import HyperDriveConfig
hd_config = HyperDriveConfig(run_config=src,
                             hyperparameter_sampling=param_sampling,
                             policy=early_termination_policy,
                             primary_metric_name="accuracy",
                             primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                             max_total_runs=100,
                             max_concurrent_runs=4)
```

## Submit experiment

After you define your hyperparameter tuning configuration, [submit the experiment](/python/api/azureml-core/azureml.core.experiment%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truesubmit-config--tags-none----kwargs-):

```Python
from azureml.core.experiment import Experiment
experiment = Experiment(workspace, experiment_name)
hyperdrive_run = experiment.submit(hd_config)
```

## Warm start your hyperparameter tuning experiment (optional)

Finding the best hyperparameter values for your model can be an iterative process. You can reuse knowledge from the five previous runs to accelerate hyperparameter tuning.


Warm starting is handled differently depending on the sampling method:
- **Bayesian sampling**: Trials from the previous run are used as prior knowledge to pick new samples, and to improve the primary metric.
- **Random sampling** or **grid sampling**:  Early termination uses knowledge from previous runs to determine poorly performing runs. 

Specify the list of parent runs you want to warm start from.

```Python
from azureml.train.hyperdrive import HyperDriveRun

warmstart_parent_1 = HyperDriveRun(experiment, "warmstart_parent_run_ID_1")
warmstart_parent_2 = HyperDriveRun(experiment, "warmstart_parent_run_ID_2")
warmstart_parents_to_resume_from = [warmstart_parent_1, warmstart_parent_2]
```

If a hyperparameter tuning experiment is canceled, you can resume training runs from the last checkpoint. However, your training script must handle checkpoint logic.

The training run must use the same hyperparameter configuration and mounted the outputs folders. The training script must accept the `resume-from` argument, which contains the checkpoint or model files from which to resume the training run. You can resume individual training runs using the following snippet:

```Python
from azureml.core.run import Run

resume_child_run_1 = Run(experiment, "resume_child_run_ID_1")
resume_child_run_2 = Run(experiment, "resume_child_run_ID_2")
child_runs_to_resume = [resume_child_run_1, resume_child_run_2]
```

You can configure your hyperparameter tuning experiment to warm start from a previous experiment or resume individual training runs using the optional parameters `resume_from` and `resume_child_runs` in the config:

```Python
from azureml.train.hyperdrive import HyperDriveConfig

hd_config = HyperDriveConfig(run_config=src,
                             hyperparameter_sampling=param_sampling,
                             policy=early_termination_policy,
                             resume_from=warmstart_parents_to_resume_from,
                             resume_child_runs=child_runs_to_resume,
                             primary_metric_name="accuracy",
                             primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                             max_total_runs=100,
                             max_concurrent_runs=4)
```

## Visualize experiment

Use the [Notebook widget](/python/api/azureml-widgets/azureml.widgets.rundetails?preserve-view=true&view=azure-ml-py) to visualize the progress of your training runs. The following snippet visualizes all your hyperparameter tuning runs in one place in a Jupyter notebook:

```Python
from azureml.widgets import RunDetails
RunDetails(hyperdrive_run).show()
```

This code displays a table with details about the training runs for each of the hyperparameter configurations.

![hyperparameter tuning table](./media/how-to-tune-hyperparameters/HyperparameterTuningTable.png)

You can also visualize the performance of each of the runs as training progresses. 

![hyperparameter tuning plot](./media/how-to-tune-hyperparameters/HyperparameterTuningPlot.png)

You can visually identify the correlation between performance and values of individual hyperparameters by using a Parallel Coordinates Plot. 

[![hyperparameter tuning parallel coordinates](./media/how-to-tune-hyperparameters/HyperparameterTuningParallelCoordinates.png)](media/how-to-tune-hyperparameters/hyperparameter-tuning-parallel-coordinates-expanded.png)

You can also visualize all of your hyperparameter tuning runs in the Azure web portal. For more information on how to view an experiment in the portal, see [how to track experiments](how-to-monitor-view-training-logs.md#view-the-experiment-in-the-web-portal).

## Find the best model

Once all of the hyperparameter tuning runs have completed, identify the best performing configuration and hyperparameter values:

```Python
best_run = hyperdrive_run.get_best_run_by_primary_metric()
best_run_metrics = best_run.get_metrics()
parameter_values = best_run.get_details()['runDefinition']['Arguments']

print('Best Run Id: ', best_run.id)
print('\n Accuracy:', best_run_metrics['accuracy'])
print('\n learning rate:',parameter_values[3])
print('\n keep probability:',parameter_values[5])
print('\n batch size:',parameter_values[7])
```

## Sample notebook
Refer to train-hyperparameter-* notebooks in this folder:
* [how-to-use-azureml/ml-frameworks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## Next steps
* [Track an experiment](how-to-track-experiments.md)
* [Deploy a trained model](how-to-deploy-and-where.md)