---
title: Using Redis Output Azure Functions for Azure Cache for Redis (preview)
description: Learn how to use Redis an output Azure Functions 
author: flang-msft
zone_pivot_groups: programming-languages-set-functions-lang-workers

ms.author: franlanglois
ms.service: azure-functions
ms.custom: devx-track-extended-java, devx-track-js, devx-track-python
ms.topic: reference
ms.date: 01/18/2024
---

# Azure Cache for Redis output binding for Azure Functions

The Azure Cache for Redis output bindings lets you change the keys in a cache based on a set of available trigger on the cache.

For information on setup and configuration details, see the [overview](functions-bindings-cache.md).

## Example

::: zone pivot="programming-language-csharp"

The following example shows a pub/sub trigger on the set event with an output binding to an Azure Cache for Redis instance. The set event triggers the cache to delete the key that was recently set.

[!INCLUDE [functions-bindings-csharp-intro](../../includes/functions-bindings-csharp-intro.md)]

# [In-process](#tab/in-process)

```c#
using Microsoft.Extensions.Logging;

namespace Microsoft.Azure.WebJobs.Extensions.Redis.Samples.RedisOutputBinding
{
    internal class SetDeleter
    {
        [FunctionName(nameof(SetDeleter))]
        public static void Run(
            [RedisPubSubTrigger(Common.connectionStringSetting, "__keyevent@0__:set")] string key,
            [Redis(Common.connectionStringSetting, "DEL")] out string[] arguments,
            ILogger logger)
        {
            logger.LogInformation($"Deleting recently SET key '{key}'");
            arguments = new string[] { key };
        }
    }
}
```

# [Isolated process](#tab/isolated-process)

<!--add a link to the extension-specific code example in this repo: https://github.com/Azure/azure-functions-dotnet-worker/blob/main/samples/Extensions/ as in the following example:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/EventGrid/EventGridFunction.cs" range="35-49":::
-->

---

::: zone-end
::: zone pivot="programming-language-java"

<!--Content and samples from the Java tab in ##Examples go here.-->

Not available in preview.

::: zone-end  
::: zone pivot="programming-language-javascript"  

<!--Content and samples from the JavaScript tab in ##Examples go here.-->

Not available in preview.

::: zone-end  
::: zone pivot="programming-language-powershell"  

Not available in preview.

<!--Content and samples from the PowerShell tab in ##Examples go here.-->

::: zone-end  
::: zone pivot="programming-language-python"  

Not available in preview.

<!--Content and samples from the Python tab in ##Examples go here.-->

::: zone-end  
::: zone pivot="programming-language-csharp"
## Attributes

Both [in-process](functions-dotnet-class-library.md) and [isolated process](dotnet-isolated-process-guide.md) C# libraries use the <!--attribute API here--> attribute to define the function. C# script instead uses a function.json configuration file.

<!-- If the attribute's constructor takes parameters, you'll need to include a table like this, where the values are from the original table in the Configuration section:

The attribute's constructor takes the following parameters:

|Parameter | Description|
|---------|----------------------|
|**Parameter1** |Description 1|
|**Parameter2** | Description 2|

-->

# [In-process](#tab/in-process)

| Attribute property | Description                                                                                                                                                 |
|------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ConnectionString`     | The name of the setting in the `appsettings` that contains the cache connection string. For example: `<cacheName>.redis.cache.windows.net:6380,password...` |
| `Command`     | The redis-cli command to be executed on the cache with all arguments separated by spaces. For example:  `GET key`, `HGET key field`. |

> [!NOTE]
> All commands are supported for this binding.

### Function Return type

string[]: Arguments for the redis command.

# [Isolated process](#tab/isolated-process)

Not available in preview.

<!-- C# attribute information for the trigger goes here with an intro sentence. Use a code link like the following to show the method definition: 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/EventGrid/EventGridFunction.cs" range="13-16":::
-->

---

::: zone-end  
::: zone pivot="programming-language-java"  

## Annotations

Not available in preview.
<!-- Equivalent values for the annotation parameters in Java.-->
::: zone-end  
::: zone pivot="programming-language-javascript,programming-language-powershell,programming-language-python"  

## Configuration

Not available in preview.

The following table explains the binding configuration properties that you set in the *function.json* file. 
<!-- this get more complex when you support the Python v2 model. -->

<!-- suggestion 

|function.json property |Description|
|---------|---------|
| **type** | Required - must be set to `eventGridTrigger`. |
| **direction** | Required - must be set to `in`. |
| **name** | Required - the variable name used in function code for the parameter that receives the event data. |
-->

::: zone-end  

See the [Example section](#example) for complete examples.

## Usage

::: zone pivot="programming-language-csharp"  
The parameter type supported by the XXX trigger depends on the Functions runtime version and the C# modality used.

# [In-process](#tab/in-process)

<!--Any usage information specific to in-process, including types. -->

# [Isolated process](#tab/isolated-process)

Not available in preview.

<!--Any usage information specific to isolated worker process, including types. -->

---

::: zone-end  
<!--Any of the below pivots can be combined if the usage info is identical.-->
::: zone pivot="programming-language-java"
<!--Any usage information from the Java tab in ## Usage. -->
::: zone-end  
::: zone pivot="programming-language-javascript,programming-language-powershell"  
<!--Any usage information from the JavaScript tab in ## Usage. -->
::: zone-end  
::: zone pivot="programming-language-powershell"  
<!--Any usage information from the PowerShell tab in ## Usage. -->
::: zone-end   
::: zone pivot="programming-language-python"  
<!--Any usage information from the Python tab in ## Usage. -->
::: zone-end  

<!---## Extra sections

Put any sections with content that doesn't fit into the above section headings down here.  
-->


## Next steps

<!--At least one next step link.-->