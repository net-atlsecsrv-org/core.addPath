---
title: Debug and troubleshoot ParallelRunStep
titleSuffix: Azure Machine Learning
description: Debug and troubleshoot ParallelRunStep in machine learning pipelines in the Azure Machine Learning SDK for Python. Learn common pitfalls for developing with pipelines, and tips to help you debug your scripts before and during remote execution.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: troubleshooting
ms.reviewer: jmartens, larryfr, vaidyas, laobri, tracych
ms.author: trmccorm
author: tmccrmck
ms.date: 07/16/2020
---

# Debug and troubleshoot ParallelRunStep
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In this article, you learn how to debug and troubleshoot the [ParallelRunStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallel_run_step.parallelrunstep?view=azure-ml-py) class from the [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

## Testing scripts locally

See the [Testing scripts locally section](how-to-debug-pipelines.md#testing-scripts-locally) for machine learning pipelines. Your ParallelRunStep runs as a step in ML pipelines so the same answer applies to both.

## Debugging scripts from remote context

The transition from debugging a scoring script locally to debugging a scoring script in an actual pipeline can be a difficult leap. For information on finding your logs in the portal, the [machine learning pipelines section on debugging scripts from a remote context](how-to-debug-pipelines.md#debugging-scripts-from-remote-context). The information in that section also applies to a ParallelRunStep.

For example, the log file `70_driver_log.txt` contains information from the controller that launches the ParallelRunStep code.

Because of the distributed nature of ParallelRunStep jobs, there are logs from several different sources. However, two consolidated files are created that provide high-level information:

- `~/logs/overview.txt`: This file provides a high-level info about the number of mini-batches (also known as tasks) created so far and number of mini-batches processed so far. At this end, it shows the result of the job. If the job failed, it will show the error message and where to start the troubleshooting.

- `~/logs/sys/master.txt`: This file provides the principal node (also known as the orchestrator) view of the running job. Includes task creation, progress monitoring, the run result.

Logs generated from entry script using EntryScript helper and print statements will be found in following files:

- `~/logs/user/<ip_address>/<node_name>.log.txt`: These files are the logs written from entry_script using EntryScript helper. Also contains print statement (stdout) from entry_script.

For a concise understanding of errors in your script there is:

- `~/logs/user/error.txt`: This file will try to summarize the errors in your script.

For more information on errors in your script, there is:

- `~/logs/user/error/`: Contains all errors thrown and full stack traces organized by node.

When you need a full understanding of how each node executed the score script, look at the individual process logs for each node. The process logs can be found in the `sys/node` folder, grouped by worker nodes:

- `~/logs/sys/node/<node_name>.txt`: This file provides detailed info about each mini-batch as it's picked up or completed by a worker. For each mini-batch, this file includes:

    - The IP address and the PID of the worker process. 
    - The total number of items, successfully processed items count, and failed item count.
    - The start time, duration, process time and run method time.

You can also find information on the resource usage of the processes for each worker. This information is in CSV format and is located at `~/logs/sys/perf/overview.csv`. Information about each process is available under `~logs/sys/processes.csv`.

### How do I log from my user script from a remote context?
ParallelRunStep may run multiple processes on one node based on process_count_per_node. In order to organize logs from each process on node and combine print and log statement, we recommend using ParallelRunStep logger as shown below. You get a logger from EntryScript and make the logs show up in **logs/user** folder in the portal.

**A sample entry script using the logger:**
```python
from azureml_user.parallel_run import EntryScript

def init():
    """ Initialize the node."""
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug("This will show up in files under logs/user on the Azure portal.")


def run(mini_batch):
    """ Accept and return the list back."""
    # This class is in singleton pattern and will return same instance as the one in init()
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug(f"{__file__}: {mini_batch}.")
    ...

    return mini_batch
```

### How could I pass a side input such as, a file or file(s) containing a lookup table, to all my workers?

User can pass reference data to script using side_inputs parameter of ParalleRunStep. All datasets provided as side_inputs will be mounted on each worker node. User can get the location of mount by passing argument.

Construct a [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) containing the reference data and register it with your workspace. Pass it to the `side_inputs` parameter of your `ParallelRunStep`. Additionally, you can add its path in the `arguments` section to easily access its mounted path:

```python
label_config = label_ds.as_named_input("labels_input")
batch_score_step = ParallelRunStep(
    name=parallel_step_name,
    inputs=[input_images.as_named_input("input_images")],
    output=output_dir,
    arguments=["--labels_dir", label_config],
    side_inputs=[label_config],
    parallel_run_config=parallel_run_config,
)
```

After that you can access it in your inference script (for example, in your init() method) as follows:

```python
parser = argparse.ArgumentParser()
parser.add_argument('--labels_dir', dest="labels_dir", required=True)
args, _ = parser.parse_known_args()

labels_path = args.labels_dir
```

## Next steps

* See the SDK reference for help with the [azureml-pipeline-steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) package. View reference [documentation](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunstep?view=azure-ml-py) for ParallelRunStep class.

* Follow the [advanced tutorial](tutorial-pipeline-batch-scoring-classification.md) on using pipelines with ParallelRunStep. The tutorial shows how to pass another file as a side input. 
