---
title: Model interpretability in automated machine learning
titleSuffix: Azure Machine Learning
description: Learn how to get explanations for how your automated ML model determines feature importance and makes predictions when using the Azure Machine Learning SDK.
services: machine-learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
---

# Model interpretability in automated machine learning

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In this article, you learn how to enable the interpretability features for automated machine learning (ML) in Azure Machine Learning. Automated ML helps you understand both raw and engineered feature importance. In order to use model interpretability, set `model_explainability=True` in the `AutoMLConfig` object.  

In this article, you learn how to:

- Perform interpretability during training for best model or any model.
- Enable visualizations to help you see patterns in data and explanations.
- Implement interpretability during inference or scoring.

## Prerequisites

- Interpretability features. Run `pip install azureml-interpret azureml-contrib-interpret` to get the necessary packages.
- Knowledge of building automated ML experiments. For more information on how to use the Azure Machine Learning SDK, complete this [regression model tutorial](tutorial-auto-train-models.md) or see how to [configure automated ML experiments](how-to-configure-auto-train.md).

## Interpretability during training for the best model

Retrieve the explanation from the `best_run`, which includes explanations for engineered features and raw features.

### Download engineered feature importance from artifact store

You can use `ExplanationClient` to download the engineered feature explanations from the artifact store of the `best_run`. To get the explanation for the raw features set `raw=True`.

```python
from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient

client = ExplanationClient.from_run(best_run)
engineered_explanations = client.download_model_explanation(raw=False)
print(engineered_explanations.get_feature_importance_dict())
```

## Interpretability during training for any model 

When you compute model explanations and visualize them, you're not limited to an existing model explanation for an automated ML model. You can also get an explanation for your model with different test data. The steps in this section show you how to compute and visualize engineered feature importance and raw feature importance based on your test data.

### Retrieve any other AutoML model from training

```python
automl_run, fitted_model = local_run.get_output(metric='r2_score')
```

### Set up the model explanations

Use `automl_setup_model_explanations` to get the engineered and raw feature explanations. The `fitted_model` can generate the following items:

- Featured data from trained or test samples
- Engineered and raw feature name lists
- Findable classes in your labeled column in classification scenarios

The `automl_explainer_setup_obj` contains all the structures from above list.

```python
from azureml.train.automl.automl_explain_utilities import AutoMLExplainerSetupClass, automl_setup_model_explanations

automl_explainer_setup_obj = automl_setup_model_explanations(fitted_model, X=X_train, 
                                                             X_test=X_test, y=y_train, 
                                                             task='classification')
```

### Initialize the Mimic Explainer for feature importance

To generate an explanation for AutoML models, use the `MimicWrapper` class. You can initialize the MimicWrapper with these parameters:

- The explainer setup object
- Your workspace
- A LightGBM model, which acts as a surrogate to the `fitted_model` automated ML model

The MimicWrapper also takes the `automl_run` object where the raw and engineered explanations will be uploaded.

```python
from azureml.interpret.mimic.models.lightgbm_model import LGBMExplainableModel
from azureml.interpret.mimic_wrapper import MimicWrapper

explainer = MimicWrapper(ws, automl_explainer_setup_obj.automl_estimator, LGBMExplainableModel, 
                         init_dataset=automl_explainer_setup_obj.X_transform, run=automl_run,
                         features=automl_explainer_setup_obj.engineered_feature_names, 
                         feature_maps=[automl_explainer_setup_obj.feature_map],
                         classes=automl_explainer_setup_obj.classes)
```

### Use MimicExplainer for computing and visualizing engineered feature importance

You can call the `explain()` method in MimicWrapper with the transformed test samples to get the feature importance for the generated engineered features. You can also use `ExplanationDashboard` to view the dashboard visualization of the feature importance values of the generated engineered features by automated ML featurizers.

```python
from azureml.contrib.interpret.visualize import ExplanationDashboard
engineered_explanations = explainer.explain(['local', 'global'],              
                                            eval_dataset=automl_explainer_setup_obj.X_test_transform)

print(engineered_explanations.get_feature_importance_dict())
ExplanationDashboard(engineered_explanations, automl_explainer_setup_obj.automl_estimator, automl_explainer_setup_obj.X_test_transform)
```

### Use Mimic Explainer for computing and visualizing raw feature importance

You can call the `explain()` method in MimicWrapper again with the transformed test samples and setting `get_raw=True` to get the feature importance for the raw features. You can also use `ExplanationDashboard` to view the dashboard visualization of the feature importance values of the raw features.

```python
from azureml.contrib.interpret.visualize import ExplanationDashboard

raw_explanations = explainer.explain(['local', 'global'], get_raw=True, 
                                     raw_feature_names=automl_explainer_setup_obj.raw_feature_names,
                                     eval_dataset=automl_explainer_setup_obj.X_test_transform)

print(raw_explanations.get_feature_importance_dict())
ExplanationDashboard(raw_explanations, automl_explainer_setup_obj.automl_pipeline, automl_explainer_setup_obj.X_test_raw)
```

### Interpretability during inference

In this section, you learn how to operationalize an automated ML model with the explainer that was used to compute the explanations in the previous section.

### Register the model and the scoring explainer

Use the `TreeScoringExplainer` to create the scoring explainer that'll compute the raw and engineered feature importance values at inference time. You initialize the scoring explainer with the `feature_map` that was computed previously. The scoring explainer uses the `feature_map` to return the raw feature importance.

Save the scoring explainer, and then register the model and the scoring explainer with the Model Management Service. Run the following code:

```python
from azureml.interpret.scoring.scoring_explainer import TreeScoringExplainer, save

# Initialize the ScoringExplainer
scoring_explainer = TreeScoringExplainer(explainer.explainer, feature_maps=[automl_explainer_setup_obj.feature_map])

# Pickle scoring explainer locally
save(scoring_explainer, exist_ok=True)

# Register trained automl model present in the 'outputs' folder in the artifacts
original_model = automl_run.register_model(model_name='automl_model', 
                                           model_path='outputs/model.pkl')

# Register scoring explainer
automl_run.upload_file('scoring_explainer.pkl', 'scoring_explainer.pkl')
scoring_explainer_model = automl_run.register_model(model_name='scoring_explainer', model_path='scoring_explainer.pkl')
```

### Create the conda dependencies for setting up the service

Next, create the necessary environment dependencies in the container for the deployed model. Please note that azureml-defaults with version >= 1.0.45 must be listed as a pip dependency, because it contains the functionality needed to host the model as a web service.

```python
from azureml.core.conda_dependencies import CondaDependencies

azureml_pip_packages = [
    'azureml-interpret', 'azureml-train-automl', 'azureml-defaults'
]

myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas', 'numpy', 'py-xgboost<=0.80'],
                                 pip_packages=azureml_pip_packages,
                                 pin_sdk_version=True)

with open("myenv.yml","w") as f:
    f.write(myenv.serialize_to_string())

with open("myenv.yml","r") as f:
    print(f.read())

```

### Deploy the service

Deploy the service using the conda file and the scoring file from the previous steps.

```python
from azureml.core.webservice import Webservice
from azureml.core.webservice import AciWebservice
from azureml.core.model import Model, InferenceConfig
from azureml.core.environment import Environment

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                               memory_gb=1,
                                               tags={"data": "Bank Marketing",  
                                                     "method" : "local_explanation"},
                                               description='Get local explanations for Bank marketing test data')
myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score_local_explain.py", environment=myenv)

# Use configs and models generated above
service = Model.deploy(ws,
                       'model-scoring',
                       [scoring_explainer_model, original_model],
                       inference_config,
                       aciconfig)
service.wait_for_deployment(show_output=True)
```

### Inference with test data

Inference with some test data to see the predicted value from automated ML model. View the engineered feature importance for the predicted value and raw feature importance for the predicted value.

```python
if service.state == 'Healthy':
    # Serialize the first row of the test data into json
    X_test_json = X_test[:1].to_json(orient='records')
    print(X_test_json)
    # Call the service to get the predictions and the engineered and raw explanations
    output = service.run(X_test_json)
    # Print the predicted value
    print(output['predictions'])
    # Print the engineered feature importances for the predicted value
    print(output['engineered_local_importance_values'])
    # Print the raw feature importances for the predicted value
    print(output['raw_local_importance_values'])
```

### Visualize to discover patterns in data and explanations at training time

You can visualize the feature importance chart in your workspace in [Azure Machine Learning studio](https://ml.azure.com). After your automated ML run is complete, select **View model details** to view a specific run. Select the **Explanations** tab to see the explanation visualization dashboard.

[![Machine Learning Interpretability Architecture](./media/how-to-machine-learning-interpretability-automl/automl-explainability.png)](./media/how-to-machine-learning-interpretability-automl/automl-explainability.png#lightbox)

## Next steps

For more information about how you can enable model explanations and feature importance in areas of the Azure Machine Learning SDK other than automated machine learning, see the [concept article on interpretability](how-to-machine-learning-interpretability.md).
