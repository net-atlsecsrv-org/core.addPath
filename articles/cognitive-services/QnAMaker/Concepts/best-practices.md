---
title: Best practices - QnA Maker
titlesuffix: Azure Cognitive Services 
description: Use these best practices to improve your knowledge base and provide better results to your application/chat bot's end users.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 05/10/2019
ms.author: tulasim
ms.custom: seodec18
---

# Best practices of a QnA Maker knowledge base
The [knowledge base development lifecycle](../Concepts/development-lifecycle-knowledge-base.md) guides you on how to manage your KB from beginning to end. Use these best practices to improve your knowledge base and provide better results to your application/chat bot's end users.

## Extraction
The QnA Maker service is continually improving the algorithms that extract QnAs from content and expanding the list of supported file and HTML formats. Follow the [guidelines](../Concepts/data-sources-supported.md) for data extraction based on your document type. 

In general, FAQ pages should be stand-alone and not combined with other information. Product manuals should have clear headings and preferably an index page. 

## Creating good questions and answers

### Good questions

The best questions are simple. Consider the key word or phrase for each question then create a simple question for that key word or phrase. 

Add as many alternate questions as you need but keep the alterations simple. Adding more words or phrasings that are not part of the main goal of the question does not help QnA Maker find a match. 

### Good answers

The best answers are simple answers but not too simple such as yes and no answers. If your answer should link to other sources or provide a rich experience with media and links, use [tagging](../how-to/metadata-generateanswer-usage.md) to distinguish which type of answer you expect, then submit that tag with the query to get the correct answer version.

## Chit-Chat
Add chit-chat to your bot, to make your bot more conversational and engaging, with low effort. You can easily add chit-chat data sets from pre-defined personalities when creating your KB, and change them at any time. Learn how to [add chit-chat to your KB](../How-To/chit-chat-knowledge-base.md). 

### Choosing a personality
Chit-chat is supported for several predefined personalities: 

|Personality |QnA Maker Dataset file |
|---------|-----|
|Professional |[qna_chitchat_professional.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_professional.tsv) |
|Friendly |[qna_chitchat_friendly.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_friendly.tsv) |
|Witty |[qna_chitchat_witty.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_witty.tsv) |
|Caring |[qna_chitchat_caring.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_caring.tsv) |
|Enthusiastic |[qna_chitchat_enthusiastic.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_enthusiastic.tsv) |

The responses range from formal to informal and irreverent. You should select the personality that is closest aligned with the tone you want for your bot. You can view the [datasets](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets), and choose one that serves as a base for your bot, and then customize the responses. 

### Edit bot-specific questions
There are some bot-specific questions that are part of the chit-chat data set, and have been filled in with generic answers. Change these answers to best reflect your bot details. 

We recommend making the following chit-chat QnAs more specific:

* Who are you?
* What can you do?
* How old are you?
* Who created you?
* Hello
   

## Ranking/Scoring
Make sure you are making the best use of the ranking features QnA Maker supports. Doing so will improve the likelihood that a given user query is answered with an appropriate response.

### Choosing a threshold
The default confidence score that is used as a threshold is 50, however you can change it for your KB based on your needs. Since every KB is different, you should test and choose the threshold that is best suited for your KB. Read more about the [confidence score](../Concepts/confidence-score.md). 

### Add alternate questions
[Alternate questions](../How-To/edit-knowledge-base.md) improve the likelihood of a match with a user query. Alternate questions are useful when there are multiple ways in which the same question may be asked. This can include changes in the sentence structure and word-style.

|Original query|Alternate queries|Change| 
|--|--|--|
|Is parking available?|Do you have car park?|sentence structure|
 |Hi|Yo<br>Hey there!|word-style or slang|

<a name="use-metadata-filters"></a>

### Use metadata tags to filter questions and answers

[Metadata](../How-To/edit-knowledge-base.md) adds the ability to narrow down the results of a user query based on metadata tags. The knowledge base answer can differ based on the metadata tag, even if the query is the same. For example, *"where is parking located"* can have a different answer if the location of the restaurant branch is different - that is, the metadata is *Location: Seattle* versus *Location: Redmond*.

### Use synonyms
While there is some support for synonyms in the English language, use case-insensitive [word alterations](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace) to add synonyms to keywords that take different form. Synonyms should be added at the QnA Maker service-level and shared by all knowledge bases in the service.

|Original word|Synonyms|
|--|--|
|buy|purchase<br>netbanking<br>net banking|

### Use distinct words to differentiate questions
QnA Maker's ranking algorithm, that matches a user query with a question in the knowledge base, works best if each question addresses a different need. Repetition of the same word set between questions reduces the likelihood that the right answer is chosen for a given user query with those words. 

For example, you might have two separate QnAs with the following questions:

|QnAs|
|--|
|where is the parking *location*|
|where is the ATM *location*|

Since these two QnAs are phrased with very similar words, this similarity could cause very similar scores for many user queries that are phrased like  *"where is the `<x>` location"*. Instead, try to clearly differentiate with queries like  *"where is the parking lot"* and *"where is the ATM"*, by avoiding words like "location" that could be in many questions in your KB. 

## Collaborate
QnA Maker allows users to [collaborate](../How-to/collaborate-knowledge-base.md) on a knowledge base. Users need access to the Azure QnA Maker resource group in order to access the knowledge bases. Some organizations may want to outsource the knowledge base editing and maintenance, and still be able to protect access to their Azure resources. This editor-approver model is done by setting up two identical [QnA Maker services](../How-to/set-up-qnamaker-service-azure.md) in different subscriptions and selecting one for the edit-testing cycle. Once testing is finished, the knowledge base contents are transferred with an [import-export](../Tutorials/migrate-knowledge-base.md) process to the QnA Maker service of the approver that will finally publish the knowledge base and update the endpoint.

## Active learning

[Active learning](../How-to/improve-knowledge-base.md) does the best job of suggesting alternative questions when it has a wide range of quality and quantity of user-based queries. It is important to allow client-applications' user queries to participate in the active learning feedback loop without censorship. Once questions are suggested in the QnA Maker portal, you can **[filter by suggestions](../How-To/improve-knowledge-base.md#add-active-learning-suggestion-to-knowledge-base)** then review and accept or reject those suggestions. 

## Next steps

> [!div class="nextstepaction"]
> [Edit a knowledge base](../How-to/edit-knowledge-base.md)
