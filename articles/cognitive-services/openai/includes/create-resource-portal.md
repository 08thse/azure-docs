---
title: 'How-to create an Azure OpenAI resource using the Azure portal'
titleSuffix: Azure OpenAI
description: Walkthrough on how to get started with Azure OpenAI by creating your first resource and deploying a model through the Azure portal.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: openai
ms.topic: include
ms.date: 06/30/2022
keywords: 
---

Use this article to get started with Azure OpenAI with step-by-step instructions to create a resource and deploy a model. While the steps to kick-off resource creation and model deployment can be completed in a few minutes, the actual deployment process itself can take more than hour. It is recommended to create your resource, start your deployment, and then check back in on your deployment later rather than actively waiting for the deployment to complete.

## Prerequisites

- An Azure subscription - [Create one for free](https://azure.microsoft.com/free/cognitive-services)
- Access granted to the service in the desired Azure subscription. This service is currently invite only. You can fill out a new use case request here: <https://aka.ms/oai/access>.

## Create a resource

Resources in Azure can be created several different ways:

- Within the Azure Portal
- Using the REST APIs, Azure ClI, PowerShell or client libraries
- Via ARM templates

This guide walks you through the Azure Portal creation experience.

1. Navigate to the create page: [Azure OpenAI Service Create Page](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_openai_tip#create/Microsoft.CognitiveServicesOpenAI)<!--TODO:Change once new link is live>
1. On the **Create** page provide the following information:

    |Field| Description   |
    |--|--|
    | **Subscription** | Select the Azure subscription used in your OpenAI onboarding application|
    | **Resource group** | The Azure resource group that will contain your OpenAI resource. You can create a new group or add it to a pre-existing group. |
    | **Region** | The location of your instance. Different locations may introduce latency, but have no impact on the runtime availability of your resource. Please choose **South Central US**|
    | **Name** | A descriptive name for your cognitive services resource. For example, *MyOpenAIResource*. |
    | **Pricing Tier** | Only 1 pricing tier is available for the service currently |

    :::image type="content" source="../media/create-resource/create.png" alt-text="Screenshot of the resource creation blade for an OpenAI Resource in the Azure portal with options selected that match the preceding table of information." lightbox="../media/create-resource/create.png":::

## Deploy a model

Before you can generate text or inference you need to deploy a model. This is done by selecting **create new deployment** on the **deployments** page. From here you can select from one of our many available models. For getting started we recommend `text-davinci-002` for users in South Central and `text-davinci-001` for users in West Europe (text-davinci-002 is not available in this region). You can do this in the Azure OpenAI Studio.

1. Go to the [Azure OpenAI Studio](https://oai.azure.com)

1. Login with the resource you want to use

1. Select the **Go to Deployments** button under **Manage deployments in your resource** to navigate to the **Deployments** page

    :::image type="content" source="../media/create-resource/deployment.png" alt-text="Screenshot of the resource creation blade for an OpenAI Resource in the Azure portal with options selected that match the preceding table of information." lightbox="../media/create-resource/deployment.png":::

1. Create a new deployment called `text-davinci-002` and choose the `text-davinci-002` model from the drop down.

  > [!NOTE]
  > Text-davinci-002 is only available in South Central US. If you are in a different region, please choose `text-davinci-001`.