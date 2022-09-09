---
title: Custom AML skill in skillsets
titleSuffix: Azure Cognitive Search
description: Extend capabilities of Azure Cognitive Search skillsets with Azure Machine Learning models.

manager: nitinme
author: mattmsft
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/12/2020
---

# AML skill in an Azure Cognitive Search enrichment pipeline

> [!IMPORTANT] 
> This skill is currently in public preview. Preview functionality is provided without a service level agreement, and is not recommended for production workloads. For more information, see [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). There is currently no .NET SDK support.

The **AML** skill allows you to extend AI enrichment with a custom [Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/overview-what-is-azure-ml) (AML) model. Once an AML model is [trained and deployed](https://docs.microsoft.com/azure/machine-learning/concept-azure-machine-learning-architecture#workflow), an **AML** skill integrates it into AI enrichment.

Like built-in skills, an **AML** skill has inputs and outputs. The inputs are sent to your deployed AML service as a JSON object, which outputs a JSON payload as a response along with a success status code. The response is expected to have the outputs specified by your **AML** skill. Any other response is considered an error and no enrichments are performed.

> [!NOTE]
> The indexer will retry twice for certain standard HTTP status codes returned from the AML service. These HTTP status codes are:
> * `503 Service Unavailable`
> * `429 Too Many Requests`

## Prerequisites

* An [AML workspace](https://docs.microsoft.com/azure/machine-learning/concept-workspace)
* An [Azure Kubernetes Service AML compute target](https://docs.microsoft.com/azure/machine-learning/concept-compute-target) in this workspace with a [deployed model](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-azure-kubernetes-service)
  * The [compute target should have SSL enabled](https://docs.microsoft.com/azure/machine-learning/how-to-secure-web-service#deploy-on-aks-and-field-programmable-gate-array-fpga). Azure Cognitive Search only allows access to **https** endpoints
  * Self-signed certificates may not be used.

## @odata.type  
Microsoft.Skills.Custom.AmlSkill

## Skill parameters

Parameters are case-sensitive. Which parameters you choose to use depends on what [authentication your AML service requires, if any](#WhatSkillParametersToUse)

| Parameter name | Description |
|--------------------|-------------|
| `uri` | (Required for [no authentication or key authentication](#WhatSkillParametersToUse)) The [scoring URI of the AML service](https://docs.microsoft.com/azure/machine-learning/how-to-consume-web-service) to which the _JSON_ payload will be sent. Only the **https** URI scheme is allowed. |
| `key` | (Required for [key authentication](#WhatSkillParametersToUse)) The [key for the AML service](https://docs.microsoft.com/azure/machine-learning/how-to-consume-web-service#authentication-with-keys). |
| `resourceId` | (Required for [token authentication](#WhatSkillParametersToUse)). The Azure Resource Manager resource ID of the AML service. It should be in the format subscriptions/{guid}/resourceGroups/{resource-group-name}/Microsoft.MachineLearningServices/workspaces/{workspace-name}/services/{service_name}. |
| `region` | (Optional for [token authentication](#WhatSkillParametersToUse)). The [region](https://azure.microsoft.com/global-infrastructure/regions/) the AML service is deployed in. |
| `timeout` | (Optional) When specified, indicates the timeout for the http client making the API call. It must be formatted as an XSD "dayTimeDuration" value (a restricted subset of an [ISO 8601 duration](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) value). For example, `PT60S` for 60 seconds. If not set, a default value of 30 seconds is chosen. The timeout can be set to a maximum of 230 seconds and a minimum of 1 second. |
| `degreeOfParallelism` | (Optional) When specified, indicates the number of calls the indexer will make in parallel to the endpoint you have provided. You can decrease this value if your endpoint is failing under too high of a request load, or raise it if your endpoint is able to accept more requests and you would like an increase in the performance of the indexer.  If not set, a default value of 5 is used. The degreeOfParallelism can be set to a maximum of 10 and a minimum of 1.

<a name="WhatSkillParametersToUse"></a>

## What skill parameters to use

Which AML skill parameters are required depends on what authentication your AML service uses, if any. AML services provide three authentication options:

* [Key-Based Authentication](https://docs.microsoft.com/azure/machine-learning/concept-enterprise-security#authentication-for-web-service-deployment). A static key is provided to authenticate scoring requests from AML skills
  * Use the _uri_ and _key_ parameters
* [Token-Based Authentication](https://docs.microsoft.com/azure/machine-learning/concept-enterprise-security#authentication). The AML service is [deployed using token based authentication](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-azure-kubernetes-service#authentication-with-tokens). The Azure Cognitive Search service's [managed identity](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) is granted the [Reader Role](https://docs.microsoft.com/azure/machine-learning/how-to-assign-roles) in the AML service's workspace. The AML skill then uses the Azure Cognitive Search service's managed identity to authenticate against the AML service, with no static keys required.
  * Use the _resourceId_ parameter.
  * If the Azure Cognitive Search service is in a different region from the AML workspace, use the _region_ parameter to set the region the AML service was deployed in
* No Authentication. No authentication is required to use the AML service
  * Use the _uri_ parameter

## Skill inputs

There are no "predefined" inputs for this skill. You can choose one or more fields that would be already available at the time of this skill's execution as inputs and the _JSON_ payload sent to the AML service will have different fields.

## Skill outputs

There are no "predefined" outputs for this skill. Depending on the response your AML service will return, add output fields so that they can be picked up from the _JSON_ response.

## Sample definition

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.AmlSkill",
    "description": "A sample model that detects the language of sentence",
    "uri": "https://contoso.count-things.com/score",
    "context": "/document",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "detected_language_code"
      }
    ]
  }
```

## Sample input JSON structure

This _JSON_ structure represents the payload that will be sent to your AML service. The top-level fields of the structure will correspond to the "names" specified in the `inputs` section of the skill definition. The value of those fields will be from the `source` of those fields (which could be from a field in the document, or potentially from another skill)

```json
{
  "text": "Este es un contrato en Inglés"
}
```

## Sample output JSON structure

The output corresponds to the response returned from your AML service. The AML service should only return a _JSON_ payload (verified by looking at the `Content-Type` response header) and should be an object where the fields are enrichments matching the "names" in the `output` and whose value is considered the enrichment.

```json
{
    "detected_language_code": "es"
}
```

## Inline shaping sample definition

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.AmlSkill",
    "description": "A sample model that detects the language of sentence",
    "uri": "https://contoso.count-things.com/score",
    "context": "/document",
    "inputs": [
      {
        "name": "shapedText",
        "sourceContext": "/document",
        "inputs": [
            {
              "name": "content",
              "source": "/document/content"
            }
        ]
      }
    ],
    "outputs": [
      {
        "name": "detected_language_code"
      }
    ]
  }
```

## Inline shaping input JSON structure

```json
{
  "shapedText": { "content": "Este es un contrato en Inglés" }
}
```

## Inline shaping sample output JSON structure

```json
{
    "detected_language_code": "es"
}
```

## Error cases
In addition to your AML being unavailable or sending out non-successful status codes, the following are considered erroneous cases:

* If the AML service returns a success status code but the response indicates that it is not `application/json`, then the response is considered invalid and no enrichments will be performed.
* If the AML service returns invalid json

For cases when the AML service is unavailable or returns an HTTP error, a friendly error with any available details about the HTTP error will be added to the indexer execution history.

## See also

+ [How to define a skillset](cognitive-search-defining-skillset.md)
+ [AML Service troubleshooting](https://docs.microsoft.com/azure/machine-learning/how-to-troubleshoot-deployment)
