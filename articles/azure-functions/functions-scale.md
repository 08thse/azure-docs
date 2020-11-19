---
title: Azure Functions scale and hosting 
description: Learn how to choose between Azure Functions Consumption plan and Premium plan.
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.topic: conceptual
ms.date: 08/17/2020

ms.custom: H1Hack27Feb2017

---
# Azure Functions hosting options

When you create a function app in Azure, you must choose a hosting plan for your app. There are three basic hosting plans available for Azure Functions: [Consumption plan](consumption-plan.md), [Premium plan](function-premium-plan.md), and [Dedicated (App Service) plan](dedicated-plan.md). All hosting plans are generally available (GA) on both Linux and Windows virtual machines.

The hosting plan you choose dictates the following behaviors:

* How your function app is scaled.
* The resources available to each function app instance.
* Support for advanced functionality, such as Azure Virtual Network connectivity.

This article provides a detailed comparison between the various hosting plans, along with Kubernetes-based hosting.

## Overview of plans

The following is a summary of the benefits of the three main hosting plans for Functions:

| | |
| --- | --- |  
|**[Consumption plan](consumption-plan.md)**| Scale automatically and only pay for compute resources when your functions are running. On the Consumption plan, instances of the Functions host are dynamically added and removed based on the number of incoming events.<br/> ✔ Default hosting plan.<br/>✔ Pay only when your functions are running.<br/>✔ Scale out automatically, even during periods of high load.|  
|**[Premium plan](function-premium-plan.md)**|While automatically scaling based on demand, use pre-warmed workers to run applications with no delay after being idle, run on more powerful instances, and connect to virtual networks. Consider the Azure Functions Premium plan in the following situations: <br/>✔ Your function apps run continuously, or nearly continuously.<br/>✔ You have a high number of small executions and a high execution bill, but low GB seconds in the Consumption plan.<br/>✔ You need more CPU or memory options than what is provided by the Consumption plan.<br/>✔ Your code needs to run longer than the maximum execution time allowed on the Consumption plan.<br/>✔ You require features that aren't available on the Consumption plan, such as virtual network connectivity.|  
|**[Dedicated plan](dedicated-plan.md)** |Run your functions within an App Service plan at regular [App Service plan rates](https://azure.microsoft.com/pricing/details/app-service/windows/). Best for long-running scenarios where [Durable Functions](durable/durable-functions-overview.md) can't be used. Consider an App Service plan in the following situations:<br/>✔ You have existing, underutilized VMs that are already running other App Service instances.<br/>✔ You want to provide a custom image on which to run your functions. <br/>✔ Predictive scaling and costs are required.|  

The comparison tables in this article also include the following hosting options, which provide the highest amount of control and isolation in which to run your function apps.  

| | |
| --- | --- |  
|**[ASE](dedicated-plan.md)** | App Service Environment (ASE) is an App Service feature that provides a fully isolated and dedicated environment for securely running App Service apps at high scale. ASEs are appropriate for application workloads that require: <br/>✔ Very high scale.<br/>✔ Full compute isolation and secure network access.<br/>✔ High memory usage.|  
| **[Kubernetes](functions-kubernetes-keda.md)** | Kubernetes provides a fully isolated and dedicated environment running on top of the Kubernetes platform.  Kubernetes is appropriate for application workloads that require: <br/>✔ Custom hardware requirements.<br/>✔ Isolation and secure network access.<br/>✔ Ability to run in hybrid or multi-cloud environment.<br/>✔ Run alongside existing Kubernetes applications and services.|  

The remaining tables in this article compare the plans on various features and behaviors. For a cost comparison between dynamic hosting plans (Consumption and Premium), see the [Azure Functions pricing page](https://azure.microsoft.com/pricing/details/functions/). For pricing of the various Dedicated plan options, see the [App Service pricing page](https://azure.microsoft.com/pricing/details/app-service/windows/). 

## Operating system/runtime

The following table shows supported operating system and language runtime support for the hosting plans.

| | Linux<sup>1</sup><br/>Code-only | Windows<sup>2</sup><br/>Code-only | Linux<sup>1,3</sup><br/>Docker container |
| --- | --- | --- | --- |
| **[Consumption plan](consumption-plan.md)** | .NET Core<br/>Node.js<br/>Java<br/>Python | .NET Core<br/>Node.js<br/>Java<br/>PowerShell Core | No support  |
| **[Premium plan](function-premium-plan.md)** | .NET Core<br/>Node.js<br/>Java<br/>Python|.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python  | 
| **[Dedicated plan](dedicated-plan.md)**<sup>4</sup> | .NET Core<br/>Node.js<br/>Java<br/>Python|.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python |
| **[ASE](dedicated-plan.md)**<sup>4</sup> | .NET Core<br/>Node.js<br/>Java<br/>Python |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core  |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python | 
| **[Kubernetes](functions-kubernetes-keda.md)** | n/a | n/a |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python |

<sup>1</sup>Linux is the only supported operating system for the Python runtime stack.  
<sup>2</sup>Windows is the only supported operating system for the PowerShell runtime stack.   
<sup>3</sup>Linux is the only supported operating system for Docker containers.
<sup>4</sup> For specific limits for the various App Service plan options, see the [App Service plan limits](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).


[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]

## Scale

The following table compares the scaling behaviors of the various hosting plans.

| | Scale out | Max # instances |
| --- | --- | --- |
| **[Consumption plan](consumption-plan.md)** | Event driven. Scale out automatically, even during periods of high load. Azure Functions infrastructure scales CPU and memory resources by adding additional instances of the Functions host, based on the number of incoming trigger events. | 200 |
| **[Premium plan](function-premium-plan.md)** | Event driven. Scale out automatically, even during periods of high load. Azure Functions infrastructure scales CPU and memory resources by adding additional instances of the Functions host, based on the number of events that its functions are triggered on. |100|
| **[Dedicated plan](dedicated-plan.md)**<sup>1</sup> | Manual/autoscale |10-20|
| **[ASE](dedicated-plan.md)**<sup>1</sup> | Manual/autoscale |100 |
| **[Kubernetes](functions-kubernetes-keda.md)**  | Event-driven autoscale for Kubernetes clusters using [KEDA](https://keda.sh). | Varies&nbsp;by&nbsp;cluster.&nbsp;&nbsp;|

<sup>1</sup> For specific limits for the various App Service plan options, see the [App Service plan limits](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).

## Cold start behavior

|    |    | 
| -- | -- |
| **[Consumption&nbsp;plan](consumption-plan.md)** | Apps may scale to zero when idle, meaning some requests may have additional latency at startup.  The consumption plan does have some optimizations to help decrease cold start time, including pulling from pre-warmed placeholder functions that already have the function host and language processes running. |
| **[Premium plan](function-premium-plan.md)** | Perpetually warm instances to avoid any cold start. |
| **[Dedicated plan](dedicated-plan.md)**<sup>1</sup> | When running in a Dedicated plan, the Functions host can run continuously, which means that cold start isn’t really an issue. |
| **[ASE](dedicated-plan.md)**<sup>1</sup> | When running in a Dedicated plan, the Functions host can run continuously, which means that cold start isn’t really an issue. |
| **[Kubernetes](functions-kubernetes-keda.md)**  | Depends on KEDA configuration. Apps can be configured to always run and never have cold start, or configured to scale to zero, resulting in cold start on new events. 

<sup>1</sup> For specific limits for the various App Service plan options, see the [App Service plan limits](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).

## Service limits

[!INCLUDE [functions-limits](../../includes/functions-limits.md)]

## Networking features

[!INCLUDE [functions-networking-features](../../includes/functions-networking-features.md)]

## Billing

| | | 
| --- | --- |
| **[Consumption plan](consumption-plan.md)** | Pay only for the time your functions run. Billing is based on number of executions, execution time, and memory used. |
| **[Premium plan](function-premium-plan.md)** | Premium plan is based on the number of core seconds and memory used across needed and pre-warmed instances. At least one instance per plan must be kept warm at all times. This plan provides more predictable pricing. |
| **[Dedicated plan](dedicated-plan.md)**<sup>1</sup> | You pay the same for function apps in an App Service Plan as you would for other App Service resources, like web apps.|
| **[ASE](dedicated-plan.md)**<sup>1</sup> | There's a flat monthly rate for an ASE that pays for the infrastructure and doesn't change with the size of the ASE. There's also a cost per App Service plan vCPU. All apps hosted in an ASE are in the Isolated pricing SKU. |
| **[Kubernetes](functions-kubernetes-keda.md)**| You pay only the costs of your Kubernetes cluster; no additional billing for Functions. Your function app runs as an application workload on top of your cluster, just like a regular app. |

<sup>1</sup> For specific limits for the various App Service plan options, see the [Service limits](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).

## Next steps

+ [Deployment technologies in Azure Functions](functions-deployment-technologies.md) 
+ [Azure Functions developer guide](functions-reference.md)
