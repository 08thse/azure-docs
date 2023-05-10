---
title: Azure Functions Overview 
description: Learn how Azure Functions can help build robust serverless apps.
ms.assetid: 01d6ca9f-ca3f-44fa-b0b9-7ffee115acd4
ms.topic: overview
ms.date: 05/27/2022
ms.custom: contperf-fy21q2, devdivchpfy22, ignite-2022
---

# Introduction to Azure Functions

Azure Functions is an event-based, serverless compute experience that accelerates application development. It allows you to write less code, maintain less infrastructure, and save on costs. Instead of worrying about deploying and maintaining servers, the cloud infrastructure provides all the up-to-date resources needed to keep your applications running.

As you build your functions, you have the following options and resources available:

- **[Integrated programming model and runtime](#integrated-programming-model-and-runtime)**: Use built-in triggers and bindings to define when a function is invoked and to what data it connects

- **[End-to-end development experience](#end-to-end-development-experience)**: Take advantage of a complete, end-to-end development experience — from building and debugging locally on major platforms like Windows, macOS, and Linux, to deploying and monitoring in the cloud

- **[Hosting options flexibility](#hosting-options-flexibility)**: Choose the hosting model that better fits your business needs without compromising development experience

- **[Fully managed and cost-effective](#fully-managed-and-cost-effective)**: Automated and flexible scaling based on your workload volume, keeping the focus on adding value instead of managing infrastructure

## Integrated programming model and runtime

You can use built-in triggers and bindings to define when a function is invoked and to what data it connects. Azure Functions features input/output bindings which provide a means of pulling data or pushing data to other services. These bindings work for both Microsoft and third-party services without the need to hard-coding integrations. Refer to [Azure Functions triggers and bindings concepts](functions-triggers-bindings.md) for more information.

The Functions [runtime](https://github.com/Azure/azure-functions-host) and the trigger and bindings extensions are fully open-source. 

![Integrated Programming Model](./media/functions-overview/integrated-programming-model.png)

## End-to-end development experience

Azure Functions offers a complete, end-to-end development experience — from building and debugging locally on major platforms like Windows, macOS, and Linux to deploying and [monitoring in the cloud](functions-monitoring.md).

You can write functions in [C#, Java, JavaScript, PowerShell, or Python](./supported-languages.md), or use a [custom handler](./functions-custom-handlers.md) to use virtually any other language. 


The functions runtime and extensions are the same regardless of where your functions run - locally on your developer environment or in the cloud. See [Azure Functions developer guide](functions-reference.md) to learn more. 

From a tools-based approach to using external pipelines, there's a [myriad of deployment options](./functions-deployment-technologies.md) available.

You can [monitoring tools](./functions-monitoring.md) and [testing strategies](./functions-test-a-function.md) to gain insights into your apps.

![End-to-end development experience](./media/functions-overview/end-to-end-development-experience.png)

## Hosting options flexibility

Pick the Functions [hosting plan](functions-scale.md) that matches your business needs and application workload. With the [Consumption](./pricing.md) plan, you only pay while your functions are running, while the [Premium](./pricing.md) and [App Service](./pricing.md) plans offer features for specialized needs. But you can deploy the same code to other hosting options like Container Apps and to your own Kubernetes cluster.

![Hosting options flexibility](./media/functions-overview/hosting-options-flexibility.png)

## Fully managed and cost-effective

When using one of the managed hosting options, the platform automatically handles all maintenance and updates and you can focus on building your application and let Azure Functions take care of the infrastructure.

Azure Functions provides "compute on-demand" through the [event driven scaled hosting options](./functions-scale.md#scale). You use the Azure Functions programming models to implement your system's logic into readily available blocks of code called "functions" which can run anytime you need to respond to critical events. As requests increase, Azure Functions meets the demand with as many resources and function instances as necessary - but only while needed. As requests fall, any extra resources and application instances drop off automatically.

Where do all the compute resources come from? Azure Functions [provides as many or as few compute resources as needed](./functions-scale.md) to meet your application's demand.

Providing compute resources on-demand is the essence of [serverless computing](https://azure.microsoft.com/solutions/serverless/) in Azure Functions.

![Fully managed and cost-effective](./media/functions-overview/fully-managed-and-cost-effective.png)

## Scenarios

We often build systems to react to a series of critical events. Whether you're building a web API, responding to database changes, processing  event data streams, or even managing message queues - Azure Functions can be used to implement them.

These scenarios allow you to build event-driven systems using modern architectural patterns. For more information, see [Azure Functions Scenarios](./functions-scenarios.md).

## Next Steps

> [!div class="nextstepaction"]
> [Get started through lessons, samples, and interactive tutorials](./functions-get-started.md)
>
> [Azure Functions Scenarios](./functions-scenarios.md)
