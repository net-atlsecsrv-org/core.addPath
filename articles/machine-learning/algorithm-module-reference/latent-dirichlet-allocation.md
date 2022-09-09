---
title:  "Latent Dirichlet Allocation"
titleSuffix: Azure Machine Learning
description: Learn how to use the Latent Dirichlet Allocation module to group otherwise unclassified text into a number of categories.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference

author: likebupt
ms.author: keli19
ms.date: 03/11/2020
---
# Latent Dirichlet Allocation

This article describes how to use the **Latent Dirichlet Allocation** module in Azure Machine Learning designer (preview), to group otherwise unclassified text into a number of categories. 

Latent Dirichlet Allocation (LDA) is often used in natural language processing (NLP) to find texts that are similar. Another common term is *topic modeling*.

This module takes a column of text, and generates these outputs:

+ The source text, together with a score for each category

+ A feature matrix, containing extracted terms and coefficients for each category

+ A transformation, which you can save and reapply to new text used as input

This module uses the scikit-learn library. For more information about scikit-learn, see the [GitHub repository, which includes tutorials and an explanation of the algorithm.

### More about Latent Dirichlet Allocation (LDA)

Generally speaking, LDA is not a method for classification per se, but uses a generative approach. What this means is that you don't need to provide known class labels and then infer the patterns.  Instead, the algorithm generates a probabilistic model that is used to identify groups of topics. You can use the probabilistic model to classify either existing training cases, or new cases that you provide to the model as input.

A generative model can be preferable because it avoids making any strong assumptions about the relationship between the text and categories, and uses only the distribution of words to mathematically model topics.

+ The theory is discussed in this paper, available as a PDF download: [Latent Dirichlet Allocation: Blei, Ng, and Jordan](https://ai.stanford.edu/~ang/papers/nips01-lda.pdf)

+ The implementation in this module is based on the [scikit-learn library](https://github.com/scikit-learn/scikit-learn/blob/master/sklearn/decomposition/_lda.py) for LDA.

For more information, see the [Technical notes](#technical-notes) section.

## How to configure Latent Dirichlet Allocation

This module requires a dataset that contains a column of text, either raw or preprocessed.

1. Add the **Latent Dirichlet Allocation** module to your pipeline.

2. As input for the module, provide a dataset containing one or more text columns.

3. For **Target columns**, choose one or more columns containing text to analyze.

    You can choose multiple columns but they must be of the string data type.

    In general, because LDA creates a large feature matrix from the text, you'll typically analyze a single text column.

4. For  **Number of topics to model**, type an integer between 1 and 1000 that indicates how many categories or topics you want to derive from the input text.

    By default, 5 topics are created.

5. For **N-grams**, specify the maximum length of N-grams generated during hashing.

    The default is 2, meaning that both bigrams and unigrams are generated.

6. Select the **Normalize** option to converting output values to probabilities. Therefore, rather than representing the transformed values as integers, values in the output and feature dataset would be transformed as follows:

    + Values in the dataset will be represented as a probability where `P(topic|document)`.

    + Values in the feature topic matrix will be represented as a probability where `P(word|topic)`.

    > [!NOTE] 
    > In Azure Machine Learning designer (preview), because the library that we based, scikit-learn, no longer support unnormalized *doc_topic_distr* output from version 0.19, therefore, in this module, **Normalize** parameter can only be applied to **Feature Topic matrix** output, **Transformed dataset** output is always normalized.

7. Select the option, **Show all options**, and then set it to TRUE if you want to view and then set additional advanced parameters.

    These parameters are specific to the scikit-learn implementation of LDA. There are some good tutorials about LDA in scikit-learn, as well as the official [scikit-learn document](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.LatentDirichletAllocation.html).

    + **Rho parameter**. Provide a prior probability for the sparsity of topic distributions. Corresponds to sklearn's `topic_word_prior` parameter. You would use the value 1 if you expect that the distribution of words is flat; i.e, all words are assumed equiprobable. If you think most words appear sparsely, you might set it to a much lower value.

    + **Alpha parameter**. Specify a prior probability for the sparsity of per-document topic weights.  Corresponds to sklearn's `doc_topic_prior` parameter.

    + **Estimated number of documents**. Type a number that represents your best estimate of the number of documents (rows) that will be processed. This lets the module allocate a hash table of sufficient size.  Corresponds to the `total_samples` parameter in scikit-learn.

    + **Size of the batch**. Type a number that indicates how many rows to include in each batch of text sent to LDA model. Corresponds to the `batch_size` parameter in scikit-learn.

    + **Initial value of iteration used in learning update schedule**. Specify the starting value that downweights learning rate for early iterations in online learning. Corresponds to the `learning_offset` parameter in scikit-learn.

    + **Power applied to the iteration during updates**. Indicate the level of power applied to the iteration count in order to control learning rate during online updates. Corresponds to the `learning_decay` parameter in scikit-learn.

    + **Number of passes over the data**. Specify the maximum number of times the algorithm will cycle over the data. Corresponds to the `max_iter` parameter in scikit-learn.

8. Select the option, **Build dictionary of ngrams** or **Build dictionary of ngrams prior to LDA**, if you want to create the n-gram list in an initial pass, before classifying text.

    If you create the initial dictionary beforehand, you can later use the dictionary when reviewing the model. Being able to map results to text rather than numerical indices is generally easier for interpretation. However, saving the dictionary will take longer and use additional storage.

9. For **Maximum size of ngram dictionary**, type the total number of rows that can be created in the n-gram dictionary.

    This option is useful for controlling the size of the dictionary. However, if the number of ngrams in the input exceeds this size, collisions may occur.

10. Submit the pipeline. The LDA module uses Bayes theorem to determine what topics might be associated with individual words. Words are not exclusively associated with any topics or groups; instead, each n-gram has a learned probability of being associated with any of the discovered classes.

## Results

The module has two outputs:

+ **Transformed dataset**: Contains the input text, and a specified number of discovered categories, together with the scores for each text example for each category.

+ **Feature topic matrix**: The leftmost column contains the extracted text feature, and there is a column for each category containing the score for that feature in that category.


### LDA transformation

This module also outputs the *LDA transformation* that applies LDA to the dataset.

You can save this transformation by register dataset under **Outputs+logs** tab in the right pane of the module and reuse it for other datasets. This might be useful if you have trained on a large corpus and want to reuse the coefficients or categories.

### Refining an LDA model or results

Typically you cannot create a single LDA model that will meet all needs, and even a model designed for one task might require many iterations to improve accuracy. We recommend that you try all these methods to improve your model:

+ Changing the model parameters
+ Using visualization to understand the results
+ Getting the feedback of subject matter experts to ascertain whether the generated topics are useful.

Qualitative measures can also be useful for assessing the results. To evaluate topic modeling results, consider:

+ Accuracy - Are similar items really similar?
+ Diversity - Can the model discriminate between similar items when required for the business problem?
+ Scalability - Does it work on a wide range of text categories or only on a narrow target domain?

The accuracy of models based on LDA can often be improved by using natural language processing to clean, summarize and simplify, or categorize text. For example, the following techniques, all supported in Azure Machine Learning, can improve classification accuracy:

+ Stop word removal

+ Case normalization

+ Lemmatization or stemming

+ Named entity recognition

For more information, see [Preprocess Text](preprocess-text.md).

In the designer, you can also use R or Python libraries for text processing: [Execute R Script](execute-r-script.md),  [Execute Python Script](execute-python-script.md)



## Technical notes

This section contains implementation details, tips, and answers to frequently asked questions.

### Implementation details

By default, the distributions of outputs for transformed dataset and feature-topic matrix are normalized as probabilities.

+ The transformed dataset is normalized as the conditional probability of topics given a document. In this case, the sum of each row equals 1.

+ The feature-topic matrix is normalized as the conditional probability of words given a topic. In this case, the sum of each column equals 1.

> [!TIP]
> Occasionally the module might return an empty topic, which is most often caused by the pseudo-random initialization of the algorithm.  If this happens, you can try changing related  parameters, such as the maximum size of the N-gram dictionary or the number of bits to use for feature hashing.

### LDA and topic modeling

Latent Dirichlet Allocation (LDA) is often used for *content-based topic modeling*, which basically means learning categories from unclassified text. In content-based topic modeling, a topic is a distribution over words.

For example, assume that you have provided a corpus of customer reviews that includes many, many products. The text of reviews that have been submitted by many customers over time would contain many terms, some of which are used in multiple topics.

A **topic** that is identified by the LDA process might represent reviews for an individual Product A, or it might represent a group of product reviews. To LDA, the topic itself is just a probability distribution over time for a set of words.

Terms are rarely exclusive to any one product, but can refer to other products, or be general terms that apply to everything ("great", "awful"). Other terms might be noise words.  However, it is important to understand that the LDA method does not purport to capture all words in the universe, or to understand how words are related, aside from probabilities of co-occurrence. It can only group words that were used in the target domain.

After the term indexes have been computed, individual rows of text are compared using a distance-based similarity measure, to determine whether two pieces of text are like each other.  For example, you might find that the product has multiple names that are strongly correlated. Or, you might find that strongly negative terms are usually associated with a particular product. You can use the similarity measure both to identify related terms and to create recommendations.

###  Module parameters

|Name|Type|Range|Optional|Default|Description|  
|----------|----------|-----------|--------------|-------------|-----------------|  
|Target column(s)|Column Selection||Required|StringFeature|Target column name or index|  
|Number of topics to model|Integer|[1;1000]|Required|5|Model the document distribution against N topics|  
|N-grams|Integer|[1;10]|Required|2|Order of N-grams generated during hashing|  
|Normalize|Boolean|True or False|Required|true|Normalize output to probabilities.  The transformed dataset will be P(topic&#124;document) and the feature topic matrix will be P(word&#124;topic)|  
|Show all options|Boolean|True or False|Required|False|Presents additional parameters specific to scikit-learn online LDA|  
|Rho parameter|Float|[0.00001;1.0]|Applies when the **Show all options** checkbox  is selected|0.01|Topic word prior distribution|  
|Alpha parameter|Float|[0.00001;1.0]|Applies when the **Show all options** checkbox  is selected|0.01|Document topic prior distribution|  
|Estimated number of documents|Integer|[1;int.MaxValue]|Applies when the **Show all options** checkbox  is selected|1000|Estimated number of documents (Corresponds to total_samples parameter)|  
|Size of the batch|Integer|[1;1024]|Applies when the **Show all options** checkbox  is selected|32|Size of the batch|  
|Initial value of iteration used in learning rate update schedule|Integer|[0;int.MaxValue]|Applies when the **Show all options** checkbox is selected|0|Initial value that downweights learning rate for early iterations. Corresponds to the learning_offset parameter|  
|Power applied to the iteration during updates|Float|[0.0;1.0]|Applies when the **Show all options** checkbox is selected|0.5|Power applied to the iteration count in order to control learning rate. Corresponds to the learning_decay parameter |  
|Number of training iterations|Integer|[1;1024]|Applies when the **Show all options** checkbox  is selected|25|Number of training iterations|  
|Build dictionary of ngrams|Boolean|True or False|Applies when the **Show all options** checkbox is *not* selected|True|Builds a dictionary of ngrams prior to computing LDA. Useful for model inspection and interpretation|  
|Maximum size of ngram dictionary|Integer|[1;int.MaxValue]|Applies when the option **Build dictionary of ngrams** is True|20000|Maximum size of the ngrams dictionary. If number of tokens in the input exceed this size, collisions may occur|  
|Number of bits to use for feature hashing|Integer|[1;31]|Applies when the **Show all options** checkbox is *not* selected and **Build dictionary of ngrams** is False|12|Number of bits to use for feature hashing| 
|Build dictionary of ngrams prior to LDA|Boolean|True or False|Applies when the **Show all options** checkbox  is selected|True|Builds a dictionary of ngrams prior to LDA. Useful for model inspection and interpretation|  
|Maximum number of ngrams in dictionary|Integer|[1;int.MaxValue]|Applies when the **Show all options** checkbox is selected and the option **Build dictionary of ngrams** is True|20000|Maximum size of the dictionary. If number of tokens in the input exceed this size, collisions may occur|  
|Number of hash bits|Integer|[1;31]|Applies when the **Show all options** checkbox is selected and the option **Build dictionary of ngrams** is False|12|Number of bits to use during feature hashing|   


## Next steps

See the [set of modules available](module-reference.md) to Azure Machine Learning.   
For a list of errors specific to the modules, see [Exceptions and error codes for the designer](designer-error-codes.md).
