---
title: How to send a Dialog Understanding job
titleSuffix: Azure Cognitive Services
description: Learn about sending a request for Dialog Understanding.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 08/16/2021
ms.author: aahi
---

# Deploy your model and send a Dialog Understanding request

After you have [trained a model](../quickstart.md#deploy-your-model) on your dataset, you're ready to deploy it. After deploying your model, you'll be able to send queries to it. 

## Deploy your model

Deploying a model is to make it host it and make it available for predictions through an endpoint. You can only have 1 deployed model per project, deploying another one will overwrite the previously deployed model.

:::image type="content" source="../media/deploy-model.png" alt-text="A screenshot showing the model deployment page in Language Studio" lightbox="../media/deploy-model.png":::

When a model is deployed, you will be able to test the model directly in the portal or by calling the API associated to it.

**(orchestration workflow projects only)**

When you're deploying an orchestration workflow project, A small window will show up for you to confirm your deployment, and configure parameters for connected services.

If you're connecting one or more LUIS applications, specify the deployment name, and whether you're using *slot* or *version* type deployment.       
* The *slot* deployment type requires you to pick between a production and staging slot.
* The *version* deployment type requires you to specify the version you have published.

No configurations are required for custom question answering and CLU connections, or for unlinked intents.

:::image type="content" source="../media/deploy-connected-services.png" alt-text="A screenshot showing the deployment screen for orchestration workflow projects." lightbox="../media/deploy-connected-services.png":::

## Send a Dialog Understanding request

Once your model is deployed, you can begin using the deployed model for predictions. Outside of the test panel, you can begin calling your deployed model via API requests to your provided custom endpoint. This endpoint request obtains the intent and entity predictions defined within the model.

You can get the full URL for your endpoint by going to the **Deploy model** page, selecting your deployed model, and clicking on "Get prediction URL".

:::image type="content" source="../media/prediction-url.png" alt-text="Screenshot showing the prediction request and URL" lightbox="../media/prediction-url.png":::

## API response for a conversations project

In a conversations project, you'll get predictions for both your intents and entities that are present within your project. 
- The intents and entities include a confidence score between 0.0 to 1.0 associated with how confident the model is about predicting a certain element in your project. 
- The top scoring intent is contained within its own parameter.
- Only predicted entities will show up in your response.
- Entities indicate:
    - The text of the entity that was extracted
    - Its start location denoted by an offset value
    - The length of the entity text denoted by a length value.

## API response for an orchestration Workflow Project

An orchestration workflow project returns with the response of the top scoring intent, and the response of the service it is connected to.
- Within the intent, the *project type* parameter lets you determine the type of response that was returned. 
- You will get the response of the connected service in the *result* parameter. 
- The *API version* parameter indicates which API version of the connected service is in the result.

Within the request, you can specify additional parameters for each connected service, in the event that the orchestrator routes to that service.
- Within the project parameters, you can optionally specify a different query to the connected service. If you don't specify an different query, the original query will be used.
- The *direct target* parameter allows you to bypass the orchestrator's routing decision and directly target a specific connected intent to force a response for it.

## Next steps

[Dialog understanding overview](../overview.md)
