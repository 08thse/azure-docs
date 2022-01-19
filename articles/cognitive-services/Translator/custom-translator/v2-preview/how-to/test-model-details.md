---
title: Test custom model BLEU score and model translation
titleSuffix: Azure Cognitive Services
description: How to test custom model BLEU score and model translation
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 01/13/2022
ms.author: lajanuar
ms.topic: conceptual
#Customer intent: As a Custom Translator user, I want to understand the test results, so that I can publish the custom model or use standard.
---
# Test custom model BLEU score and model translation | Preview

> [!IMPORTANT]
> Custom Translator v2.0 is currently in public preview. Some features may not be supported or have constrained capabilities.

In order to make informed decision whether to use our standard model or your custom model, you should evaluate the delta between your custom model `BLEU score` and our standard model `Baseline BLEU`. Usually, an average of 3–5 BLEU points - or more - indicates the custom model has higher translation quality.

## Test model translation

1. Select **Test model** blade.
2. Select model **Name**,for example, "en-de with sample data".
3. Human evaluate translation from **New model** (custom model), and **Baseline model** (our pre-trained baseline used for customization) against **Reference** (target translation from the test set)

![Model test results](../media/quickstart/model-test-details.png)

## Next steps

- Learn [how to publish model](publish-model.md).
- Learn [how to translate documents with custom models](translate-with-custom-model.md).
