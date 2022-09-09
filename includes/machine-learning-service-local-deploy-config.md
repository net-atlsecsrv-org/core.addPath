---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 07/26/2019
ms.author: larryfr
---

The entries in the `deploymentconfig.json` document map to the parameters for [LocalWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration?view=azure-ml-py). The following table describes the mapping between the entities in the JSON document and the parameters for the method:

| JSON entity | Method parameter | Description |
| ----- | ----- | ----- |
| `computeType` | NA | The compute target. For local, the value must be `local`. |
| `port` | `port` | The local port on which to expose the service's HTTP endpoint. |

The following JSON is an example deployment configuration for use with the CLI:

```json
{
    "computeType": "local",
    "port": 32267
}
```
