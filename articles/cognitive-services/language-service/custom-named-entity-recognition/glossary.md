---
title: Definitions and terms used for Conversational Named Entity Recognition (NER)
titleSuffix: Azure Cognitive Services
description: Learn about some of the definitions and terms you may encounter when using Conversational Named Entity Recognition (NER)
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
---

# Conversational Named Entity Recognition (NER) definitions and terms

Use this article to learn about some of the definitions and terms you may encounter when using conversational NER.

## Project

A project is a work area for building your custom AI models based on your data. Your project can only be accessed by you and other people who have contributor access to the Azure resource you are using.
As a prerequisite to creating a conversational entity extraction project, you have to connect your resource to a storage account with your dataset when you [create a new project](../quickstart.md). Your project automatically includes all the `.txt` files available in your container.

Within your project you can do the following:

* **Tag your data**: The process of tagging your data so that when you train your model it learns what you want to extract.
* **Build and train your model**: The core step of your project, where your model starts learning from your tagged data. 
* **View model evaluation details**: Review your model performance to decide if there is room for improvement, or you are satisfied with the results.
* **Improve model**: When you know what went wrong with your model, and how to improve performance. 
* **Deploy model**: Make your model available for use. 
* **Test model**: Test your model on a dataset.

## Model

A model is an object that has been trained to do a certain task, in our case custom entity extraction.

* **Model training** is the process of teaching your model what to extract based on your tagged data.
* **Model evaluation** is the process that happens right after training to know how well does your model perform.
* **Model deployment** is the process of making it available for use.

## Entity

An entity is a span of text that indicates a certain type of information. The text span can consist of one or more words (or tokens). In Conversational Named Entity Recognition (NER), entities represent the information that you want to extract from the text. 

For example, in the sentence "*John borrowed 25,000 USD from Fred.*" the entities might be: 

| Entity name/type | Entity |
| -- | -- |
| Borrower Name | *John* |
| Lender Name | *Fred* |
| Loan Amount | *25,000 USD* |

## Next steps

* [Data and service limits](limits.md).
* [Recommended practices](recommended-practices.md)
* [Conversational NER overview](../overview.md).