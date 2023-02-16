---
title: Durable Functions best practice and diagnostic tools
description: Learn about the best practices when using Durable Functions and the various tools avaiable for diagnosing problems.
author: lilyjma
ms.topic: conceptual
ms.date: 02/15/2023
ms.author: azfuncdf
---

# Durable Functions best practice and diagnostic tools

This article details some best practices that can help you avoid common problems when using Durable Functions. It also includes various tools you can use to help diagnose problems during development and testing and when running your app in production.

## Best practice

- **Use the latest version of Durable Functions extension and SDK**

    Some issues users experience with Durable Functions can be resolved by upgrading to the latest version of the extension and SDK, which contain important bug fixes and performance improvements. Having the most updated versions also ensure that the latest telemetry is collected when reproducing problems reported by users on GitHub, which would accelerate the investigation process. See [Upgrade durable functions extension version](./durable-functions-extension-upgrade.md) for instructions on getting the latest extension. Check the package manager of the language you are using to get the latest SDK. 

- **Adhere to Durable Functions [code constraints](durable-functions-code-constraints.md)**

    The [replay](./durable-functions-orchestrations#reliability.md) behavior of orchestrator code creates constraints on the type of code that you can write in an orchestrator function. An example of a constraint is that your orchestrator function must use deterministic APIs so that each time it’s replayed, it produces the same result.

    The Durable Functions Roslyn Analyzer is a live code analyzer that guides C# users to adhere to Durable Functions specific code constraints. See [Durable Functions Roslyn Analyzer](durable-functions-roslyn-analyzer.md) for instructions on how to enable it on Visual Studio and Visual Studio Code.  


- **Avoid using activity functions to perform CPU-intensive or I/O tasks**
 
    Orchestrator functions are executed on a single thread to ensure that execution can be deterministic across many [replays](durable-functions-orchestrations#reliability.md). Because of this single-threaded execution, it's important that orchestrator function threads do not perform CPU-intensive tasks, do I/O, or block for any reason. Any work that may require I/O, blocking, or multiple threads should be moved into activity functions.

- **Understand language runtime restrictions**

    Your [language runtime may impose concurrency restrictions](durable-functions-perf-and-scale#language-runtime-considerations.md) on your functions. For example, Durable Function apps written in Python and PowerShell may only allow you to run a single function at a time on your VM. This can cause problems if an orchestrator fans out to more activity functions than allowed by the language runtime. For example, if your orchestrator fans out to 10 activities, but the language runtime supports running only one function at a time, then 9 activities will be stuck waiting. Because they have been loaded into memory by the Durable Functions runtime, they cannot be load balanced to other VMs either. Therefore, it is important that your Durable Functions concurrency setting matches that of the language runtime. 

- **Make sure [task hubs have unique names](durable-functions-task-hubs#multiple-function-apps.md) when multiple apps share one storage account**

    If you have multiple function apps sharing a shared storage account, you must explicitly configure different names for each task hub in the host.json files. Otherwise, the multiple function apps will compete for messages, which could result in undefined behavior, including orchestrations getting unexpectedly "stuck" in the Pending or Running state.

    The only exception is if you deploy *copies* of the same app in multiple regions; in this case, you can use the same task hub for the copies. 

- **Handle versioning correctly**

    It is inevitable that functions will be added, removed, and changed over the lifetime of an application. Examples of [common breaking changes](durable-functions-versioning.md) include changing activity or entity function signatures and changing orchestrator logic. These changes are a problem when they affect workflows that are still in process. If deployed incorrectly, they could lead to orchestrations failing with non-deterministic error, getting stuck indefinitely, performance degradation, etc. Refer to recommended [mitigation strategies](durable-functions-versioning#mitigation-strategies.md) when dealing with breaking changes. 

- **Manage memory usage**

    *Passing in large values to and from Durable Functions APIs*

    You can run into memory issues if you have large inputs and outputs to Durable Functions APIs. 

    One common mistake Durable Functions users make is passing in large values to and from activity functions. These values will get persisted into orchestrator history when they enter or exit orchestrator functions. Over time, this history grows and loading it into memory during [replay](durable-functions-orchestrations#reliability.md) could cause out of memory exceptions. If you need to work with large objects, splitting your activity functions across more orchestration or suborchestration functions would reduce the size of the orchestration history, which could prevent out of memory exceptions. Another approach could be storing your large data in an external store like Azure Blob Storage under a given ID, then retrieve that data with the ID inside an activity function. 

    *Fine tune your concurrency settings*

    A single worker instance can execute multiple work items concurrently to increase efficiency. However, if a worker tries to process too many work items simultaneously, it could exhaust its resources like CPU load, network connections, etc. Usually, this shouldn’t be a concern because scaling and limiting work items are handled automatically for you. However, if you’re experiencing performance issues (such as orchestrators taking too long to finish, are stuck in pending, etc.) or are doing performance testing, you could [configure concurrency limits](durable-functions-perf-and-scale#configuration-of-throttles.md) in the host.json file.

    If you find that your app still cannot fulfill your performance needs even after fine tuning concurrency, you may want to consider using the Netherite storage provider instead of the default Azure Storage. Learn more about the [Netherite provider](durable-functions-storage-providers#a-namenetheriteanetherite.md). 

## Diagnostic tools

There are several tools available to help you diagnose problems.

- **Configuration for more logs**

    *Durable Functions Extension* 
    The Durable extension emits tracking events that allow you to trace the end-to-end execution of an orchestration. These tracking events can be found and queried using the [Application Insights Analytics](../../azure-monitor/logs/log-query-overview.md) tool in the Azure portal. The verbosity of tracking data emitted can be configured in the logger (Functions 1.x) or logging (Functions 2.0) section of the host.json file. See [configuration details](durable-functions-diagnostics#functions-10.md). 
        
    *Durable Task Framework*
    Starting in v2.3.0 of the Durable extension, logs emitted by the underlying Durable Task Framework (DTFx) are also available for collection. Details on how to [enable these logs](durable-functions-diagnostics#durable-task-framework-logging.md).  

- **Azure Portal**

    *Diagnostic and solve problems*
    Azure Function App Diagnostics is a useful resource on Azure Portal for monitoring and diagnosing potential issues in your application. It also provides suggestions to help resolve problems based on diagnosis. See [Azure Function App Diagnostics](function-app-diagnostics.md). 

    *Durable Functions Orchestration traces*
    Azure Portal provides orchestration trace details to help you understand the status of each orchestration instance and trace the end-to-end execution. When you look at the list of functions inside your Azure Functions app, you will see a "Monitor" column which contains links to the traces. You need to have Applications Insights enabled for your app to get this information. 

- **[Durable Functions Monitor Extension](https://github.com/microsoft/DurableFunctionsMonitor)**

    This is a Visual Studio Code extension that provides a UI for monitoring, managing, and debugging your orchestration instances. 

- **Roslyn Analyzer**

    The Durable Functions Roslyn Analyzer is a live code analyzer that guides C# users to adhere to Durable Functions specific [code constraints](durable-functions-code-constraints.md). See [Durable Functions Roslyn Analyzer](durable-functions-roslyn-analyzer.md) for instructions on how to enable it on Visual Studio and Visual Studio Code. 


## Support

If you have any questions, please open an issue in one of the GitHub repos below. Including information such as instance IDs, time ranges in UTC, app name (if possible), deployment region would help facilitate investigations. 
- [.NET](https://github.com/Azure/azure-functions-durable-extension/issues)
- [Java](https://github.com/microsoft/durabletask-java/issues)
- [Python](https://github.com/Azure/azure-functions-durable-python/issues)
- [JavaScript](https://github.com/Azure/azure-functions-durable-js/issues)
- [Durable Task Framework/.NET isolated worker](https://github.com/microsoft/durabletask-dotnet/issues)