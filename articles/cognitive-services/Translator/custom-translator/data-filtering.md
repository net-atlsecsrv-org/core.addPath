---
title: Data Filtering - Custom Translator
titleSuffix: Azure Cognitive Services
description: When you submit documents to be used for training a custom system, the documents undergo a series of processing and filtering steps to prepare for training.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: v-pawal
ms.topic: conceptual
#Customer intent: As a Custom Translator, I want to understand how data is filtered before training a model.
---

# Data filtering

When you submit documents to be used for training a custom system, the documents undergo a series of processing and filtering steps to prepare for training. These steps are explained here. The knowledge of the filtering may help you understand the sentence count displayed in custom translator as well as the steps you may take yourself to prepare the documents for training with Custom Translator.

## Sentence alignment
If your document isn't in XLIFF, TMX, or ALIGN format, Custom Translator aligns the sentences of your source and target documents to each other, sentence by sentence. Translator doesn't perform document alignment – it follows your naming of the documents to find the matching document of the other language. Within the document, Custom Translator tries to find the corresponding sentence in the other language. It uses document markup like embedded HTML tags to help with the alignment.  

If you see a large discrepancy between the number of sentences in the source and target side documents, your document may not have been parallel in the first place, or for other reasons couldn't be aligned. The document pairs with a large difference (>10%) of sentences on each side warrant a second look to make sure they're indeed parallel. Custom Translator shows a warning next to the document if the sentence count differs suspiciously.  


## Deduplication
Custom Translator removes the sentences that are present in test and tuning documents from training data. The removal happens dynamically inside of the training run, not in the data processing step. Custom Translator reports the sentence count to you in the project overview before such removal.  

## Length filter
* Remove sentences with only one word on either side.
* Remove sentences with more than 100 words on either side.  Chinese, Japanese, Korean are exempt.
* Remove sentences with fewer than 3 characters. Chinese, Japanese, Korean are exempt.
* Remove sentences with more than 2000 characters for Chinese, Japanese, Korean.
* Remove sentences with less than 1% alpha characters.
* Remove dictionary entries containing more than 50 words.

## White space
* Replace any sequence of white-space characters including tabs and CR/LF sequences with a single space character.
* Remove leading or trailing space in the sentence

## Sentence end punctuation
Replace multiple sentence end punctuation characters with a single instance.  

## Japanese character normalization
Convert full width letters and digits to half-width characters.

## Unescaped XML tags
Filtering transforms unescaped tags into escaped tags:
* `&lt;` becomes `&amp;lt;`
* `&gt;` becomes `&amp;gt;`
* `&amp;` becomes `&amp;amp;`

## Invalid characters
Custom Translator removes sentences that contain Unicode character U+FFFD. The character U+FFFD indicates a failed encoding conversion.

## Next steps

- [Train a model](how-to-train-model.md) in Custom Translator.
