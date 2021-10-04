---
title: What is the health feature in Azure Cognitive Service for language?
titleSuffix: Azure Cognitive Services
description: An overview of the health feature in Azure Cognitive Services, which helps you extract medical information from unstructured text, like clinical documents.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 09/30/2021
ms.author: aahi
---

# What is the health feature in Azure Cognitive Service for language?

The health feature is one of the features offered by [Azure Cognitive Services for language](../overview.md), a collection of machine learning and AI algorithms in the cloud for developing intelligent applications that involve written language. 

The health feature extracts and labels relevant medical information from unstructured texts such as doctor's notes, discharge summaries, clinical documents, and electronic health records.

This documentation contains the following types of articles:

* [**Quickstarts**](quickstart.md) are getting-started instructions to guide you through making requests to the service.
* [**How-to guides**](how-to/call-api.md) contain instructions for using the service in more specific or customized ways.
* The [**conceptual articles**](concepts/health-entity-categories.md) provide in-depth explanations of the service's functionality and features.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Introducing-Text-Analytics-for-Health/player]

## Features

[!INCLUDE [health features](includes/features.md)]

[!INCLUDE [Typical workflow for pre-configured language features](../includes/overview-typical-workflow.md)]

## Deploy on premises using Docker containers

Use the available Docker container to [deploy this feature on-premises](how-to/use-containers.md). These docker containers enable you to bring the service closer to your data for compliance, security, or other operational reasons.

## Responsible AI 

An AI system includes not only the technology, but also the people who will use it, the people who will be affected by it, and the environment in which it is deployed. Read the [transparency note for the health feature](/legal/cognitive-services/language-service/transparency-note-health?context=/azure/cognitive-services/language/context/context) to learn about responsible AI use and deployment in your systems. You can also see the following articles for more information:

[!INCLUDE [Responsible AI links](../includes/overview-responsible-ai-links.md)]

## Next steps

There are two ways to get started using the entity linking feature:
* [Language Studio](../language-studio.md), which is a web-based platform that enables you to try several Azure Cognitive Service for language features without needing to write code.
* The [quickstart article](quickstart.md) for instructions on making requests to the service using the REST API and client library SDK.  
