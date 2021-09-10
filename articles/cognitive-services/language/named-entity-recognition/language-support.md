---
title: Named Entity Recognition (NER) and PII detection language support 
titleSuffix: Azure Cognitive Services
description: This article explains which natural languages are supported by the NER and PII detection features of Azure Cognitive Service for language.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 09/10/2021
ms.author: aahi
---

# Named Entity Recognition (NER) and Personally Identifiable Information (PII) detection language support 

Use this article to learn which natural languages are supported by the NER and PII features of Language Services.

> [!NOTE]
> Languages are added as new [model versions](../concepts/specify-model-version.md) are released for this feature. The current model version for NER is `2021-06-01`.

## NER language support

> [!NOTE]
> Only "Person", "Location" and "Organization" entities are returned for languages marked with *.

| Language               | Language code | v3.x support | Starting with v3 model version: |       Notes        |
|:-----------------------|:-------------:|:----------:|:-------------------------------:|:------------------:|
| Arabic                 |     `ar`      |      ✓*    |               2019-10-01        |                    |
| Chinese-Simplified     |   `zh-hans`   |     ✓      |               2021-01-15        | `zh` also accepted |
| Chinese-Traditional   |   `zh-hant`   |     ✓*      |               2019-10-01        |                    |
| Czech                 |     `cs`      |     ✓*      |               2019-10-01        |                    |
| Danish                |     `da`      |     ✓*      |               2019-10-01        |                    |
| Dutch                 |     `nl`      |     ✓*      |               2019-10-01        |                    |
| English                |     `en`      |     ✓      |               2019-10-01        |                    |
| Finnish               |     `fi`      |     ✓*      |               2019-10-01        |                    |
| French                 |     `fr`      |     ✓      |               2021-01-15        |                    |
| German                 |     `de`      |     ✓      |               2021-01-15        |                    |
| Hebrew                |     `he`      |     ✓*      |               2019-10-01        |                    |
| Hungarian             |     `hu`      |     ✓*      |               2019-10-01        |                    |
| Italian               |     `it`      |     ✓       |               2021-01-15        |                    |
| Japanese              |     `ja`      |     ✓       |               2021-01-15        |                    |
| Korean                |     `ko`      |     ✓       |               2021-01-15        |                    |
| Norwegian  (Bokmål)   |     `no`      |     ✓*      |               2019-10-01        | `nb` also accepted |
| Polish                |     `pl`      |     ✓*      |               2019-10-01        |                    |
| Portuguese (Brazil)   |    `pt-BR`    |     ✓       |               2021-01-15        |                    |
| Portuguese (Portugal) |    `pt-PT`    |     ✓       |               2021-01-15        | `pt` also accepted |
| Russian              |     `ru`      |     ✓*       |               2019-10-01        |                    |
| Spanish               |     `es`      |     ✓       |               2020-04-01        |                    |
| Swedish               |     `sv`      |     ✓*      |               2019-10-01        |                    |
| Turkish               |     `tr`      |     ✓*      |               2019-10-01        |                    |

## PII language support

> [!NOTE]
> Languages are added as new [model versions](concepts/model-versioning.md) are released for specific Text Analytics features. The current model version for PII is `2021-01-15`.

| Language               | Language code | v3.x support | Starting with v3 model version: |       Notes        |
|:-----------------------|:-------------:|:----------:|:-------------------------------:|:------------------:|
| Chinese-Simplified     |   `zh-hans`   |     ✓      |               2021-01-15        | `zh` also accepted |
| English                |     `en`      |     ✓      |               2020-07-01        |                    |
| French                 |     `fr`      |     ✓      |               2021-01-15        |                    |
| German                 |     `de`      |     ✓      |               2021-01-15        |                    |
| Italian               |     `it`      |     ✓       |               2021-01-15        |                    |
| Japanese              |     `ja`      |     ✓       |               2021-01-15        |                    |
| Korean                |     `ko`      |     ✓       |               2021-01-15        |                    |
| Portuguese (Brazil)   |    `pt-BR`    |     ✓       |               2021-01-15        |                    |
| Portuguese (Portugal) |    `pt-PT`    |     ✓       |               2021-01-15        | `pt` also accepted |
| Spanish               |     `es`      |     ✓       |               2020-04-01        |                    |

## Next steps

[NER and PII feature overview](overview.md)