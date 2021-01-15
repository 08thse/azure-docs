---
title: Get started - Translator
titleSuffix: Azure Cognitive Services
description: This article will show you how to sign up for the Azure Cognitive Services Translator and get a subscription key.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 01/15/2021
ms.author: lajanuar
---
# Create a Translator Resource

## Prerequisites

To get started, you need an active Azure subscription.
If you don't have one, you can [**create a free Azure account**](https://azure.microsoft.com/free/cognitive-services/).

## Create a Cognitive Services resource

### 1. Choose your resource type

Translator  can be accessed through two different resource types:

* **Single-service resource**—enables access to a single service API key and endpoint.  
* **Multi-service resource**—enables access to the following Cognitive Services using a single API key and endpoint:

|API Pillar|Service Name|
|---|---|
|Language | <ul><li>[Translator](../translator/translator-info-overview.md)</li><li>[Language Understanding (LUIS)](../luis/what-is-luis.md)</li><li>[Text Analytics](../text-analytics/overview.md)</li></ul>|
|Vision| <ul><li>[Computer Vision](../computer-vision/overview.md)</li><li>[Face](../face/overview.md)</li></ul>|
|Decision|<ul><li>[Content Moderator](../content-moderator/overview.md)</li></ul>|

### 2. Navigate to the Azure Portal

You can navigate directly to the _Create_ page for your service:

* Create your [Translator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) resource.

* Create your [Cognitive Services resource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne). **NOTE**: The multi-service resource is named **Cognitive Services** in the portal.

>[!TIP]
>If you prefer, you can start the *Create* process on the Azure Portal home page as follows:
> 1. Navigate to the Azure Portal.
> 1. Select ➕**Create a resource**  from the Azure services menu.
>1. In the **Search the Marketplace** search box, enter and select **Cognitive Services** ([multi-service resource](#create-a-cognitive-services-resource)) or **Translator** ([single-service resource](#create-a-cognitive-services-resource)).
> 1. Select **Create** to go to the project details page.
><br/><br/>

### 2. Complete project and instance details

> [!div class="checklist"]
> * **Subscription**. Select one of your available Azure subscriptions.
> * **Resource Group**. This Azure resource group will contain your Cognitive Services resource. You can create a new group or add it to a pre-existing group that shares the same lifecycle, permissions, and policies.
> * **Resource Region**. Enter the [Azure geographical region](https://azure.microsoft.com/global-infrastructure/geographies/) that meets your needs.
> * **Name**. Enter the name you have chosen for your resource. This will be your custom domain name in your endpoint.
> * **Pricing tier**. Select a [pricing tier](https://azure.microsoft.com/pricing/details/cognitive-services/) that meets your needs:

<ul style="padding-left:30px"><li>Each subscription has a free tier.</li><li>The free tier has the same features and functionalities as paid plans and doesn't expire.</li><li>You can have only one free subscription per account.</li></ul>

> [!div class="checklist"]
> * If you have created a multi-service resource, you will need to confirm additional usage details via the check boxes.
> * Select **Review + Create**. 
> * Review the service terms and select **Create** to deploy your resource. 
> * After your resource has successfully deployed, select **Go to resource**.

## Retrieve your authentication key and endpoint URL

All Cognitive Services API requests require an authentication key. You can pass your key through a query string parameter or by specifying it in the HTTP request header.

1. Retrieve your authentication key by selecting **Keys and Endpoint** from the left-rail menu.
1. Copy either of the listed subscription keys.

[!INCLUDE  cognitive-services-environment-variables]

## Delete a  resource or resource group

To remove a Cognitive Services or Translator resource, you can [delete the resource](/azure/azure-resource-manager/management/delete-resource-group?tabs=azure-portal#delete-resource) or [delete the resource group](/azure/azure-resource-manager/management/delete-resource-group?tabs=azure-portal#delete-resource-group). Deleting the resource group also deletes the resources contained in the group.

## Learn more

* **[Custom Translator](customization.md)**.  You can customize text translations to reflect your domain-specific terminology and style.

* **[Microsoft Translator code samples](https://github.com/MicrosoftTranslator)**.  Multi-language Translator code samples are available on GitHub.

## More resources

- [Microsoft Translator Support Forum](https://www.aka.ms/TranslatorForum)
- [Get Started with Azure (3-minute video)](https://azure.microsoft.com/get-started/?b=16.24)
