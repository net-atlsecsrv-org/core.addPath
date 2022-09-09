---
title: "ResNet"
titleSuffix: Azure Machine Learning
description: Learn how to create an image classification model in Azure Machine Learning designer using the ResNet algorithm.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference

author: likebupt
ms.author: keli19
ms.date: 09/26/2020
---

# ResNet

This article describes how to use the **ResNet** module in Azure Machine Learning designer, to create an image classification model using the ResNet algorithm..  

This classification algorithm is a supervised learning method, and requires a labeled dataset. 
> [!NOTE]
> This module does not support labeled dataset generated from *Data Labeling* in the studio, but only support labeled image directory generated from [Convert to Image Directory](convert-to-image-directory.md) module. 

You can train the model by providing a model and a labeled image directory as inputs to [Train Pytorch Model](train-pytorch-model.md). The trained model can then be used to predict values for the new input examples using [Score Image Model](score-image-model.md).

### More about ResNet

Refer to [this paper](https://pytorch.org/docs/stable/torchvision/models.html?highlight=resnext101_32x8d#torchvision.models.resnext101_32x8d) for more details about ResNet.

## How to configure ResNet

1.  Add the **ResNet** module to your pipeline in the designer.  

2.  For **Model name**, specify name of a certain ResNet structure and you can select from supported resnet: 'resnet18', 'resnet34', 'resnet50', 'resnet101', 'resnet152', 'resnet152', 'resnext50\_32x4d', 'resnext101\_32x8d', 'wide_resnet50\_2', 'wide_resnet101\_2'.

3.  For **Pretrained**, specify whether to use a model pre-trained on ImageNet. If selected, you can fine-tune model based on selected pre-trained model; if deselected, you can train from scratch.

4.  Connect the output of **DenseNet** module, training and validation image dataset module to the [Train Pytorch Model](train-pytorch-model.md). 

5. Submit the pipeline.

## Results

After pipeline run is completed, to use the model for scoring, connect the [Train Pytorch Model](train-pytorch-model.md) to [Score Image Model](score-image-model.md), to predict values for new input examples.

## Technical notes  

###  Module parameters  

| Name       | Range | Type    | Default           | Description                              |
| ---------- | ----- | ------- | ----------------- | ---------------------------------------- |
| Model name | Any   | Mode    | resnext101\_32x8d | Name of a certain ResNet structure       |
| Pretrained | Any   | Boolean | True              | Whether to use a model pre-trained on ImageNet |
|            |       |         |                   |                                          |

###  Output  

| Name            | Type                    | Description                              |
| --------------- | ----------------------- | ---------------------------------------- |
| Untrained model | UntrainedModelDirectory | An untrained ResNet model that can be connected to Train Pytorch Model. |

## Next steps

See the [set of modules available](module-reference.md) to Azure Machine Learning. 