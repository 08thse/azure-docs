---
title: Azure Container Apps hosting of Azure Functions 
description: Learn about how you can use Azure Container Apps to host containerized function apps in Azure Functions.
ms.date: 05/04/2023
ms.topic: conceptual
# Customer intent: As a cloud developer, I want to learn more about hosting my function apps in Linux containers by using Azure Container Apps.
---

# Azure Container Apps hosting of Azure Functions 

Azure Functions provides integrated suppport for developing, deploying, and managing containerized function apps on [Azure Container Apps](../container-apps/overview.md). 

[!INCLUDE [functions-container-apps-preview](../../includes/functions-container-apps-preview.md)]

Integration with Container Apps lets you leverage the existing functions programming model to write function code in your preferred programming language or framework supported by Azure Functions. You still get the Functions triggers and bindings, as well as event-driven scaling. Container Apps leverages the power of the underlying Azure Kubernetes Service (AKS) while removing the complexity of having to work with Kubernetes APIs.

This integration also means that you can leverage existing Functions client tools and the portal to create containers, deploy function app containers to Container Apps, and configure continuous deployment. Network and observability configurations are defined at the Container App environment level and apply to all microservices running in a Container Apps environment, including your function app. You also get the other cloud-native capabilities of Container Apps, including KEDA, Dapr, Envoy. You can still use Application Insights to monitor your functions executions.

## Deploying Azure Functions to Container Apps

In the current preview, you must deploy your functions code in a Linux container that you create. Functions maintains a set of [lanuage-specific base images](https://mcr.microsoft.com/en-us/catalog?search=functions) that you can use to generate your containerized function apps. When you create a Functions project using [Azure Functions Core Tools](./functions-run-local.md) and include the [`--docker` option](./functions-core-tools-reference.md#func-init), Core Tools also generates a .Dockerfile that you can use to create your container from the correct base image. 

Azure Functions currently supports the following methods of deployment to Azure Container Apps:

+ Azure CLI 
+ Azure Portal
+ GitHub Actions
+ Azure Pipeline tasks
+ ARM templates
+ Bicep templates
+ Azure Functions core tools

To learn how to create and deploy a function app container to Container Apps using the Azure CLI, see [Create your first containerized functions on Azure Container Apps](functions-deploy-container-aca.md). 

[!INCLUDE [functions-linux-custom-container-note](../../includes/functions-linux-custom-container-note.md)]

## Considerations for Container Apps hosting

Keep in mind the following considerations when deploying your function app containers to Container Apps:
 
+ Container Apps support for Functions is currently in preview and is only available in the following regions:
    + Australia East
    + Central US 
    + East US 
    + East Us 2 
    + North Europe 
    + South Central US 
    + UK South 
    + West Europe 
    + West US3 
+ When running in a [Consumption + Dedicated plan structure](../container-apps/plans.md#consumption-dedicated), only the default Consumption plan is currently supported. Dedicated plans in this structure aren't yet supported for Functions.
+ While all triggers can be used, only the following triggers can dynamically scale (from zero instances) when running on Container Apps:
    + HTTP 
    + Azure Queue Storage 
    + Azure Service Bus 
    + Azure Event Hubs 
    + Kafka (without certificates)   
+ For the built-in Container Apps [policy definitions](../container-apps/policy-reference.md#policy-definitions), currently only environment-level policies apply to Azure Functions containers.
+ When using Container Apps, you don't have direct access to the lower-level Kubernetes APIs. However, you can access the AKS instance directly.

## Next steps

+ [Hosting and scale](./functions-scale.md)