---
title: How to perform Named Entity Recognition 
titleSuffix: Azure Cognitive Services
description: This article will show you how to extract named entities from text and detect identifying information.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/10/2021
ms.author: aahi
---


# How to use Named Entity Recognition(NER) and detect Personally Identifying Information (PII)

The NER and PII feature can evaluate unstructured text, extract named entities from text in several pre-defined categories, and detect identifying information.

## Named Entity Recognition (NER)

Named Entity Recognition (NER) is the ability to identify different entities in text and categorize them into pre-defined classes or types such as: person, location, event, product, and organization.  

## Personally Identifiable Information (PII)

The PII feature is part of NER and it can identify and redact sensitive entities in text that are associated with an individual person such as: phone number, email address, mailing address, passport number.

| Feature                                                         | NER | PII |
|-----------------------------------------------------------------|--------|----------|
| Methods for single, and batch requests                          | X      | X        |
| Expanded entity recognition across several categories           | X      | X        |
| Recognition of personal (`PII`) and health (`PHI`) information entities        |        | X        |
| Redaction of `PII`        |        | X        |

## Determine how to process the data (optional)

### Specify the NER model

By default, this feature will use the latest available AI model on your text. You can also configure your API requests to use a previous model version, if you determine one performs better on your data. The model you specify will be used to perform NER and PII operations.

| Feature | Supported Versions | latest version |
|--|--|--|
| Entity Recognition (general) | `2019-10-01`, `2020-02-01`, `2020-04-01`,`2021-01-15`,`2021-06-01`  | `2021-06-01`   |
| Entity Recognition (PII)     | `2019-10-01`, `2020-02-01`, `2020-04-01`,`2020-07-01`, `2021-01-15`  | `2021-01-15`   |

### Input languages

When you submit documents to be processed, you can specify which of [the supported languages](language-support.md) they're written in. if you don't specify a language, key phrase extraction will default to English. The API may return offsets in the response to support different [multilingual and emoji encodings](../../concepts/multilingual-emoji-support.md). 

## Submitting data

Analysis is performed upon receipt of the request. For information on the size and number of requests you can send per minute and second, see the data limits section below.

Using the NER and PII features synchronously is stateless. No data is stored in your account, and results are returned immediately in the response.

When using these features asynchronously, the API results are available for 24 hours from the time the request was ingested, and is indicated in the response. After this time period, the results are purged and are no longer available for retrieval.

The API will attempt to detect the [defined entity categories](concepts/named-entity-categories.md) for a given document language. If you want to specify which entities will be detected and returned, use the optional `piiCategories` parameter with the appropriate entity categories. This parameter can also let you detect entities that aren't enabled by default for your document language. The following URL example would detect a French driver's license number that might occur in English text, along with the default English entities.

> [!TIP]
> If you don't include `default` when specifying entity categories, The API will only return the entity categories you specify.

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.5/entities/recognition/pii?piiCategories=default,FRDriversLicenseNumber`

## Getting NER and PII results

When you get results from language detection, you can stream the results to an application or save the output to a file on the local system. The API response will include [recognized entities](concepts/named-entity-categories.md), including their categories and sub-categories, and confidence scores. Entities that represent personal information (PII) will be redacted in the returned text.

## Data limits

> [!NOTE]
> * If you need to analyze larger documents than the limit allows, you can break the text into smaller chunks of text before sending them to the API. 
> * A document is a single string of text characters.  

| Limit | Value |
|------------------------|---------------|
| Maximum size of a single document (synchronous) | 5,120 characters as measured by [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements).  |
| Maximum number of characters per request (asynchronous)  | 125K characters across all submitted documents, as measured by [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements).  |
| Maximum size of entire request | 1 MB. |
| Max documents per request | 5 |

If a document exceeds the character limit, the API will behave differently depending on the feature you're using:

* Asynchronous:
  * The API will reject the entire request and return a `400 bad request` error if any document within it exceeds the maximum size.
* Synchronous:  
  * The API won't process a document that exceeds the maximum size, and will return an invalid document error for it. If an API request has multiple documents, the API will continue processing them if they are within the character limit.

Exceeding the maximum number of documents you can send in a single request will generate an HTTP 400 error code.

### Rate limits

Your rate limit will vary with your [pricing tier](https://aka.ms/unifiedLanguagePricing).

| Tier          | Requests per second | Requests per minute |
|---------------|---------------------|---------------------|
| S / Multi-service | 1000                | 1000                |
| S0 / F0         | 100                 | 300                 |

## Next steps

[Named Entity Recognition overview](overview.md)