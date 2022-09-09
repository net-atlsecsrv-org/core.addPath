---
title: How to perform a batch test - LUIS
titleSuffix: Azure Cognitive Services
description: Use Language Understanding (LUIS) batch testing sets to find utterances with incorrect intents and entities.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.author: diberry
---

# Batch testing with a set of example utterances

 Batch testing is a comprehensive test on your current trained model to measure its performance in LUIS. The data sets used for batch testing should not include example utterances in the intents or utterances received from the prediction runtime endpoint.

<a name="batch-testing"></a>

## Import a dataset file for batch testing

1. Select **Test** in the top bar, and then select **Batch testing panel**.

    ![Batch Testing Link](./media/luis-how-to-batch-test/batch-testing-link.png)

2. Select **Import dataset**. The **Import new dataset** dialog box appears. Select **Choose File** and locate a JSON file with the correct [JSON format](luis-concept-batch-test.md#batch-file-format) that contains *no more than 1,000* utterances to test.

    Import errors are reported in a red notification bar at the top of the browser. When an import has errors, no dataset is created. For more information, see [Common errors](luis-concept-batch-test.md#common-errors-importing-a-batch).

3. In the **Dataset Name** field, enter a name for your dataset file. The dataset file includes an **array of utterances** including the *labeled intent* and *entities*. Review the [example batch file](luis-concept-batch-test.md#batch-file-format) for syntax.

4. Select **Done**. The dataset file is added.

## Run, rename, export, or delete dataset

To run, rename, export, or delete the dataset, use the ellipsis (***...***) button at the end of the dataset row.

> [!div class="mx-imgBorder"]
> ![Screenshot of batch tests list with options](./media/luis-how-to-batch-test/batch-testing-options.png)

## Run a batch test on your trained app

To run the test, select the dataset name then select **Run** from the contextual toolbar. When the test completes, this row displays the test result of the dataset.

The downloadable dataset is the same file that was uploaded for batch testing.

|State|Meaning|
|--|--|
|![Successful test green circle icon](./media/luis-how-to-batch-test/batch-test-result-green.png)|All utterances are successful.|
|![Failing test red x icon](./media/luis-how-to-batch-test/batch-test-result-red.png)|At least one utterance intent did not match the prediction.|
|![Ready to test icon](./media/luis-how-to-batch-test/batch-test-result-blue.png)|Test is ready to run.|

<a name="access-batch-test-result-details-in-a-visualized-view"></a>

## View batch test results

To review the batch test results, select **See results**.

<a name="filter-chart-results-by-intent-or-entity"></a>

## Filter chart results

To filter the chart by a specific intent or entity, select the intent or entity in the right-side filtering panel. The data points and their distribution update in the graph according to your selection.

![Visualized Batch Test Result](./media/luis-how-to-batch-test/filter-by-entity.png)

## View single-point utterance data

In the chart, hover over a data point to see the certainty score of its prediction. Select a data point to retrieve its corresponding utterance in the utterances list at the bottom of the page.

![Selected utterance](./media/luis-how-to-batch-test/selected-utterance.png)


<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>

## View section data

In the four-section chart, select the section name, such as **False Positive** at the top-right of the chart. Below the chart, all utterances in that section display below the chart in a list.

![Selected utterances by section](./media/luis-how-to-batch-test/selected-utterances-by-section.png)

In this preceding image, the utterance `switch on` is labeled with the TurnAllOn intent, but received the prediction of None intent. This is an indication that the TurnAllOn intent needs more example utterances in order to make the expected prediction.

The two sections of the chart in red indicate utterances that did not match the expected prediction. These indicate utterances which LUIS needs more training.

The two sections of the chart in green did match the expected prediction.

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## Next steps

If testing indicates that your LUIS app doesn't recognize the correct intents and entities, you can work to improve your LUIS app's performance by labeling more utterances or adding features.

* [Label suggested utterances with LUIS](luis-how-to-review-endpoint-utterances.md)
* [Use features to improve your LUIS app's performance](luis-how-to-add-features.md)
* [Understand batch testing with this tutorial](luis-tutorial-batch-testing.md)
* [Learn batch testing concepts](luis-concept-batch-test.md).
