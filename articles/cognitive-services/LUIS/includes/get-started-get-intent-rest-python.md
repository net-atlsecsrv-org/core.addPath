---
title: Get intent with REST call in Python
titleSuffix: Azure Cognitive Services
services: cognitive-services

manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 06/03/2020

---

[Reference documentation](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c08) | [Sample](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/python-predict-with-rest/predict.py)

## Prerequisites

* [Python 3.6](https://www.python.org/downloads/) or later.
* [Visual Studio Code](https://code.visualstudio.com/)

## Create Pizza app

[!INCLUDE [Create pizza app](get-started-get-intent-create-pizza-app.md)]

## Get intent from the prediction endpoint

Use Python to query the [prediction endpoint](https://aka.ms/luis-apim-v3-prediction) and get a prediction result.

1. Copy this code snippet into a file called `predict.py`:

    [!code-python[Code snippet](~/cognitive-services-quickstart-code/python/LUIS/python-predict-with-rest/predict.py)]

1. Replace the values starting with `YOUR-` with your own values.

    |Information|Purpose|
    |--|--|
    |`YOUR-APP-ID`|Your app ID. Located on the LUIS portal, Application Settings page for your app.
    |`YOUR-PREDICTION-KEY`|Your 32 character prediction key. Located on the LUIS portal, Azure Resources page for your app.
    |`YOUR-PREDICTION-ENDPOINT`| Your prediction URL endpoint. Located on the LUIS portal, Azure Resources page for your app.<br>For example, `https://westus.api.cognitive.microsoft.com/`.|

1. Install the `requests` dependency. The `requests` library is used to make HTTP requests:

    ```console
    pip install requests
    ```

1. Run your script with this console command:

    ```console
    python predict.py
    ```

1. Review the prediction response, which is returned as JSON:

    ```console
    {'query': 'I want two large pepperoni pizzas on thin crust please', 'prediction': {'topIntent': 'ModifyOrder', 'intents': {'ModifyOrder': {'score': 1.0}, 'None': {'score': 8.55e-09}, 'Greetings': {'score': 1.82222226e-09}, 'CancelOrder': {'score': 1.47272727e-09}, 'Confirmation': {'score': 9.8125e-10}}, 'entities': {'Order': [{'FullPizzaWithModifiers': [{'PizzaType': ['pepperoni pizzas'], 'Size': [['Large']], 'Quantity': [2], 'Crust': [['Thin']], '$instance': {'PizzaType': [{'type': 'PizzaType', 'text': 'pepperoni pizzas', 'startIndex': 17, 'length': 16, 'score': 0.9978157, 'modelTypeId': 1, 'modelType': 'Entity Extractor', 'recognitionSources': ['model']}], 'Size': [{'type': 'SizeList', 'text': 'large', 'startIndex': 11, 'length': 5, 'score': 0.9984481, 'modelTypeId': 1, 'modelType': 'Entity Extractor', 'recognitionSources': ['model']}], 'Quantity': [{'type': 'builtin.number', 'text': 'two', 'startIndex': 7, 'length': 3, 'score': 0.999770939, 'modelTypeId': 1, 'modelType': 'Entity Extractor', 'recognitionSources': ['model']}], 'Crust': [{'type': 'CrustList', 'text': 'thin crust', 'startIndex': 37, 'length': 10, 'score': 0.933985531, 'modelTypeId': 1, 'modelType': 'Entity Extractor', 'recognitionSources': ['model']}]}}], '$instance': {'FullPizzaWithModifiers': [{'type': 'FullPizzaWithModifiers', 'text': 'two large pepperoni pizzas on thin crust', 'startIndex': 7, 'length': 40, 'score': 0.90681237, 'modelTypeId': 1, 'modelType': 'Entity Extractor', 'recognitionSources': ['model']}]}}], 'ToppingList': [['Pepperoni']], '$instance': {'Order': [{'type': 'Order', 'text': 'two large pepperoni pizzas on thin crust', 'startIndex': 7, 'length': 40, 'score': 0.9047088, 'modelTypeId': 1, 'modelType': 'Entity Extractor', 'recognitionSources': ['model']}], 'ToppingList': [{'type': 'ToppingList', 'text': 'pepperoni', 'startIndex': 17, 'length': 9, 'modelTypeId': 5, 'modelType': 'List Entity Extractor', 'recognitionSources': ['model']}]}}}}
    ```

    JSON response formatted for readability:

    ```JSON
    {
      'query': 'I want two large pepperoni pizzas on thin crust please',
      'prediction': {
        'topIntent': 'ModifyOrder',
        'intents': {
          'ModifyOrder': {
            'score': 1.0
          },
          'None': {
            'score': 8.55e-9
          },
          'Greetings': {
            'score': 1.82222226e-9
          },
          'CancelOrder': {
            'score': 1.47272727e-9
          },
          'Confirmation': {
            'score': 9.8125e-10
          }
        },
        'entities': {
          'Order': [
            {
              'FullPizzaWithModifiers': [
                {
                  'PizzaType': [
                    'pepperoni pizzas'
                  ],
                  'Size': [
                    [
                      'Large'
                    ]
                  ],
                  'Quantity': [
                    2
                  ],
                  'Crust': [
                    [
                      'Thin'
                    ]
                  ],
                  '$instance': {
                    'PizzaType': [
                      {
                        'type': 'PizzaType',
                        'text': 'pepperoni pizzas',
                        'startIndex': 17,
                        'length': 16,
                        'score': 0.9978157,
                        'modelTypeId': 1,
                        'modelType': 'Entity Extractor',
                        'recognitionSources': [
                          'model'
                        ]
                      }
                    ],
                    'Size': [
                      {
                        'type': 'SizeList',
                        'text': 'large',
                        'startIndex': 11,
                        'length': 5,
                        'score': 0.9984481,
                        'modelTypeId': 1,
                        'modelType': 'Entity Extractor',
                        'recognitionSources': [
                          'model'
                        ]
                      }
                    ],
                    'Quantity': [
                      {
                        'type': 'builtin.number',
                        'text': 'two',
                        'startIndex': 7,
                        'length': 3,
                        'score': 0.999770939,
                        'modelTypeId': 1,
                        'modelType': 'Entity Extractor',
                        'recognitionSources': [
                          'model'
                        ]
                      }
                    ],
                    'Crust': [
                      {
                        'type': 'CrustList',
                        'text': 'thin crust',
                        'startIndex': 37,
                        'length': 10,
                        'score': 0.933985531,
                        'modelTypeId': 1,
                        'modelType': 'Entity Extractor',
                        'recognitionSources': [
                          'model'
                        ]
                      }
                    ]
                  }
                }
              ],
              '$instance': {
                'FullPizzaWithModifiers': [
                  {
                    'type': 'FullPizzaWithModifiers',
                    'text': 'two large pepperoni pizzas on thin crust',
                    'startIndex': 7,
                    'length': 40,
                    'score': 0.90681237,
                    'modelTypeId': 1,
                    'modelType': 'Entity Extractor',
                    'recognitionSources': [
                      'model'
                    ]
                  }
                ]
              }
            }
          ],
          'ToppingList': [
            [
              'Pepperoni'
            ]
          ],
          '$instance': {
            'Order': [
              {
                'type': 'Order',
                'text': 'two large pepperoni pizzas on thin crust',
                'startIndex': 7,
                'length': 40,
                'score': 0.9047088,
                'modelTypeId': 1,
                'modelType': 'Entity Extractor',
                'recognitionSources': [
                  'model'
                ]
              }
            ],
            'ToppingList': [
              {
                'type': 'ToppingList',
                'text': 'pepperoni',
                'startIndex': 17,
                'length': 9,
                'modelTypeId': 5,
                'modelType': 'List Entity Extractor',
                'recognitionSources': [
                  'model'
                ]
              }
            ]
          }
        }
      }
    }
    ```

## Clean up resources

When you are finished with this quickstart, delete the file from the file system.

## Next steps

> [!div class="nextstepaction"]
> [Add utterances and train](../get-started-get-model-rest-apis.md)