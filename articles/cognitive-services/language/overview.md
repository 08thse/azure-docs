---
title: What is Azure Cognitive Service for Language
titleSuffix: Azure Cognitive Services
description: Learn how to integrate AI into your applications that can extract information and understand written language.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 09/16/2021
ms.author: aahi
---

# What are Azure Language services? 

Azure Cognitive Service for language is a cloud-based service that provides Natural Language Processing (NLP) features for understanding and analyzing text. Use this service to help build intelligent applications using the web-based Language Studio, REST APIs, and client libraries.  

The service is the unification of the following Cognitive Services offerings:

* Text Analytics
* QnA Maker
* Language Understanding (LUIS)

## Available features

> [!TIP]
> See the [Language Studio quickstart](language-studio.md) to begin exploring these features using a web-based portal, and without needing to write code. 

Azure Cognitive Service for language provides the following features:

|Feature  |Description  | Deployment options| 
|---------|---------|---------|
| [Named Entity Recognition(NER) and sensitive information(PII)](named-entity-recognition/overview.md)     | This pre-configured feature identifies entities in text across several pre-defined categories, including sensitive information.        | <ul><li>[**REST API and client-library SDK**](named-entity-recognition/quickstart.md)</li></ul> |
| [Key phrase extraction](key-phrase-extraction/overview.md)     | This pre-configured feature evaluates unstructured text, and for each input document, returns a list of key phrases and main points in the text. | <ul><li>[**REST API and client-library SDK**](key-phrase-extraction/quickstart.md)</li><li>[**Docker container**](key-phrase-extraction/how-to/use-containers.md)</li></ul>  |
|[Entity linking](entity-linking/overview.md)    | This pre-configured feature disambiguates the identity of an entity found in text and provides links to the entity on Wikipedia.        |
|[Named Entity Recognition (NER)](named-entity-recognition/overview.md)      | This prebuilt feature identifies entities in text and categorizes them into pre-defined classes such as: names, locations, and organizations.        |
| [Extract information from healthcare-related text](health/overview.md)    | This pre-configured feature extracts information from unstructured medical texts, such as clinical notes and doctor's notes.  |
| [Custom NER](custom-named-entity-recognition/overview.md)    | Build an AI model to extract custom entity categories, using unstructured text that you provide. |
| [Analyze sentiment and opinions](sentiment-opinion-mining/overview.md)     | This pre-configured feature provides sentiment labels (such as "*negative*", "*neutral*" and "*positive*") for sentences and documents. This feature can additionally provide granular information about the opinions related to words that appear in the text, such as the attributes of products or services. |
|[Language detection](language-detection/overview.md)    | This pre-configured feature evaluates text, and determines the language it was written in. It returns a language identifier and a score that indicates the strength of the analysis.        |
|[Custom text classification](custom-classification/overview.md)    | Build an AI model to classify unstructured text into custom classes that you define.         |
| [Text Summarization](text-summarization/overview.md)     | This pre-configured feature extracts key sentences that collectively convey the essence of a document. |
| [Custom conversational language understanding](custom-language-understanding/overview.md)   | Build an AI model to bring the ability to understand natural language into apps, bots, and IoT devices. |
| [Question answering](custom-question-answering/overview.md)     | This pre-configured feature provides answers to questions extracted from text input, using semi-structured content such as: FAQs, manuals, and documents. |

<!--
:::row:::
    :::column:::
        **Feature**
    :::column-end:::
    :::column:::
        **Description**
    :::column-end:::
    :::column:::
        **Deployment options**
    :::column-end:::
:::row-end:::

:::row:::
    :::column:::
        [Named Entity Recognition(NER) and sensitive information(PII)](named-entity-recognition/overview.md)
    :::column-end:::
    :::column:::
        This pre-configured feature identifies and redacts Personally Identifiable Information (PII) in text across several pre-defined categories, and categorizes them. For example: names, addresses, phone numbers, and passport numbers.
    :::column-end:::
    :::column:::
        * [REST API and client-library SDK](named-entity-recognition/quickstart.md)
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [Key phrase extraction](key-phrase-extraction/overview.md)
    :::column-end:::
    :::column:::
        This pre-configured feature evaluates unstructured text, and for each input document, returns a list of key phrases and main points in the text.
    :::column-end:::
    :::column:::
        * [REST API and client-library SDK](key-phrase-extraction/quickstart.md)
        * [Docker container](key-phrase-extraction/how-to/use-containers.md)
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [Entity linking](entity-linking/overview.md) 
    :::column-end:::
    :::column:::
        This pre-configured feature disambiguates the identity of an entity found in text and provides links to the entity on Wikipedia.
    :::column-end:::
    :::column:::
        * [REST API and client-library SDK](entity-linking/quickstart.md)
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [Extract information from healthcare-related text](health/overview.md)  
    :::column-end:::
    :::column:::
        This pre-configured feature extracts information from unstructured medical texts, such as clinical notes and doctor's notes.
    :::column-end:::
    :::column:::
        * [REST API and client-library SDK](health/quickstart.md)
        * [Docker container](health/how-to/use-containers.md)
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [Custom Named Entity Recognition(NER)](custom-named-entity-recognition/overview.md) 
    :::column-end:::
    :::column:::
        Build an AI model to extract custom entity categories, using unstructured text that you provide.
    :::column-end:::
    :::column:::
        * [REST API and client-library SDK](custom-named-entity-recognition/quickstart.md)
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [Analyze sentiment and opinions](sentiment-opinion-mining/overview.md)
    :::column-end:::
    :::column:::
        This pre-configured feature provides sentiment labels (such as "*negative*", "*neutral*" and "*positive*") for sentences and documents. This feature can additionally provide granular information about the opinions related to words that appear in the text, such as the attributes of products or services.
    :::column-end:::
    :::column:::
        * [REST API and client-library SDK](sentiment-opinion-mining/quickstart.md)
        * [Docker container](sentiment-opinion-mining/how-to/use-containers.md)
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [Language detection](language-detection/overview.md)
    :::column-end:::
    :::column:::
        This pre-configured feature evaluates text, and determines the language it was written in. It returns a language identifier and a score that indicates the strength of the analysis.
    :::column-end:::
    :::column:::
        * [REST API and client-library SDK](language-detection/quickstart.md)
        * [Docker container](language-detection/how-to/use-containers.md)
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [Custom text classification](custom-classification/overview.md)  
    :::column-end:::
    :::column:::
        Build an AI model to classify unstructured text into custom classes that you define.
    :::column-end:::
    :::column:::
        * [REST API and client-library SDK](custom-classification/quickstart.md)
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [Text Summarization](text-summarization/overview.md) 
    :::column-end:::
    :::column:::
        This pre-configured feature extracts key sentences that collectively convey the essence of a document.
    :::column-end:::
    :::column:::
        * [REST API and client-library SDK](text-summarization/quickstart.md)
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [Custom conversational language understanding](custom-language-understanding/overview.md)
    :::column-end:::
    :::column:::
        Build an AI model to bring the ability to understand natural language into apps, bots, and IoT devices. 
    :::column-end:::
    :::column:::
        * [REST API and client-library SDK](custom-language-understanding/quickstart.md)
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [Question answering](custom-question-answering/overview.md) 
    :::column-end:::
    :::column:::
        This pre-configured feature provides answers to questions extracted from text input, using semi-structured content such as: FAQs, manuals, and documents.
    :::column-end:::
    :::column:::
        * [REST API and client-library SDK](custom-question-answering/quickstart/sdk.md)
    :::column-end:::
:::row-end:::
-->

## Tutorials

After you've had a chance to get started with the Language service, try our tutorials that show you how to solve various scenarios.

* Tutorial: Determine user intentions with the Language SDK, C#
* Tutorial: Extract information in Excel with the Language SDK, C#
* Tutorial: Add knowledge bases in multiple languages with Language SDK, C# 

## Additional code samples

You can find more code samples on GitHub for the following languages:

* [C#](https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/textanalytics/Azure.AI.TextAnalytics/samples)
* [Java](https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/textanalytics/azure-ai-textanalytics/src/samples)
* [JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/textanalytics/ai-text-analytics/samples)
* [Python](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/textanalytics/azure-ai-textanalytics/samples)

## Deploy on premises using Docker containers 
Use Language service containers to deploy API features on-premises. These Docker containers enable you to bring the service closer to your data for compliance, security, or other operational reasons. The Language service offers the following containers:

* [Sentiment analysis](sentiment-opinion-mining/how-to/use-containers.md)
* [Language detection](language-detection/how-to/use-containers.md)
* [Key phrase extraction](key-phrase-extraction/how-to/use-containers.md) 
* [Health feature](health/how-to/use-containers.md)


## Responsible AI 

An AI system includes not only the technology, but also the people who will use it, the people who will be affected by it, and the environment in which it is deployed. Read the following articles to learn about responsible AI use and deployment in your systems:

* [Transparency note for Language services](/legal/cognitive-services/text-analytics/transparency-note)
* [Integration and responsible use](/legal/cognitive-services/text-analytics/guidance-integration-responsible-use)
* [Data, privacy, and security](/legal/cognitive-services/text-analytics/data-privacy)
