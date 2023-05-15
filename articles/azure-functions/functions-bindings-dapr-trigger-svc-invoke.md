---
title: Dapr Service Invocation trigger for Azure Functions
description: Learn how to run an Azure Function as Dapr service invocation data changes.
ms.topic: reference
ms.date: 05/15/2023
ms.devlang: csharp, java, javascript, powershell, python
ms.custom: "devx-track-csharp, devx-track-python"
zone_pivot_groups: programming-languages-set-functions-lang-workers
---

# Dapr Service Invocation trigger for Azure Functions

::: zone pivot="programming-language-csharp, programming-language-javascript, programming-language-python"

Azure Functions can be triggered using the following Dapr events.

There are no templates for triggers in Dapr in the functions tooling today. Start your project with another trigger type (e.g. Storage Queues) and then modify the function.json or attributes.

::: zone-end

::: zone pivot="programming-language-csharp, programming-language-javascript, programming-language-python"

## Example

::: zone-end

::: zone pivot="programming-language-csharp"

A C# function can be created using one of the following C# modes:

- [In-process class library](./functions-dotnet-class-library.md): compiled C# function that runs in the same process as the Functions runtime. 
- [Isolated worker process class library](./dotnet-isolated-process-guide.md): compiled C# function that runs in a worker process that is isolated from the runtime. Isolated worker process is required to support C# functions running on non-LTS versions .NET and the .NET Framework.     
    

# [In-process](#tab/in-process)

```csharp
[FunctionName("RetrieveOrder")]
public static void Run(
    [DaprServiceInvocationTrigger] object args,
    [DaprState("%StateStoreName%", Key = "order")] string data,
    ILogger log)
{
    log.LogInformation("C# function processed a RetrieveOrder request from the Dapr Runtime.");
    // print the fetched state value
    log.LogInformation(data);
}
```

# [Isolated process](#tab/isolated-process)

More samples for the Dapr service invocation trigger are available in the [GitHub repository](todo).

<!--
:::code language="csharp" source="https://www.github.com/azure/azure-functions-dapr-extension/samples/dotnet-isolated-azurefunction/Trigger/TransferEventBetweenTopics.cs" range="8-30"::: 
 
-->

---

::: zone-end 

::: zone pivot="programming-language-javascript"

The following examples show Dapr triggers in a _function.json_ file and JavaScript code that uses those bindings. 

Here's the _function.json_ file for `daprServiceInvocationTrigger`:

```json
{
  "bindings": [
    {
      "type": "daprServiceInvocationTrigger",
      "name": "payload"
    }
  ]
}
```

Here's the JavaScript code for the Dapr Service Invocation trigger:

```javascript
module.exports = async function (context) {
    context.log("Node function processed a RetrieveOrder request from the Dapr Runtime.");

    // print the fetched state value
    context.log(context.bindings.data);
};
```
::: zone-end

::: zone pivot="programming-language-python"

The following example shows a Dapr Service Invocation trigger, which uses the [v1 Python programming model](functions-reference-python.md).

Here's the _function.json_ file for `daprServiceInvocationTrigger`:

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "type": "daprServiceInvocationTrigger",
      "name": "payload",
      "direction": "in"
    }
  ]
}
```

Here's the Python code:

```python
import logging
import json
import azure.functions as func

def main(payload, data: str) -> None:
    logging.info('Python function processed a RetrieveOrder request from the Dapr Runtime.')
    logging.info(data)
```

---

::: zone-end

::: zone pivot="programming-language-csharp"

## Attributes
Both in-process and isolated process C# libraries use the <!--attribute API here--> attribute to define the function.

# [In-process](#tab/in-process)

In [C# class libraries], use the [DaprServiceInvocationTrigger] to trigger a Dapr input binding, which supports the following properties.

| Parameter | Description | 
| --------- | ----------- | 
| **MethodName** | _Optional._ The name of the method the Dapr caller should use. If not specified, the name of the function is used as the method name. | 

# [Isolated process](#tab/isolated-process)

C# attribute information for the trigger goes here with an intro sentence. 
TODO: table has in-proc parameters - need out-of-proc

| Parameter | Description | 
| --------- | ----------- | 
| **MethodName** | _Optional._ The name of the method the Dapr caller should use. If not specified, the name of the function is used as the method name. | 

---

::: zone-end

::: zone pivot="programming-language-javascript, programming-language-python"

## Configuration
The following table explains the binding configuration properties that you set in the function.json file.

|function.json property | Description|
|---------|----------------------|
|**type** | Must be set to `daprServiceInvocationTrigger`.|
|**name** | The name of the variable that represents the Dapr data in function code. |
|**direction** | Must be set to `in`.  |

::: zone-end

::: zone pivot="programming-language-csharp"

See the [Example section](#example) for complete examples.

## Usage
TODO: Need usage content. 

Included text: The parameter type supported by the Dapr Service Invocation trigger depends on the Functions runtime version, the extension package version, and the C# modality used.

# [In-process](#tab/in-process)

<!--Any usage information from the C# tab in ## Usage. -->
 
# [Isolated process](#tab/isolated-process)

<!--If available, call out any usage information from the linked example in the worker repo. -->

---

::: zone-end

<!--Any of the below pivots can be combined if the usage info is identical.-->
::: zone pivot="programming-language-javascript"

See the [Example section](#example) for complete examples.

## Usage
TODO: Need usage content. 

Included text: The parameter type supported by the Dapr Service Invocation trigger depends on the Functions runtime version, the extension package version, and the C# modality used.

::: zone-end

::: zone pivot="programming-language-python"

See the [Example section](#example) for complete examples.

## Usage
TODO: Need usage content. 

Included text: The parameter type supported by the Dapr Service Invocation trigger depends on the Functions runtime version, the extension package version, and the C# modality used.

::: zone-end

<!---## Extra sections Put any sections with content that doesn't fit into the above section headings down here. This will likely get moved to another article after the refactor. -->

::: zone pivot="programming-language-java,programming-language-powershell"

> [!NOTE]
> Currently, Dapr triggers and bindings are only supported in C#, JavaScript, and Python. 

::: zone-end

## Next steps
- Triggers 
  - [Dapr input binding](./functions-bindings-dapr-trigger-input.md)
  - [Dapr topic](./functions-bindings-dapr-trigger-topic.md)
- Input
  - [Dapr state](./functions-bindings-dapr-input-state.md)
  - [Dapr secret](./functions-bindings-dapr-input-secret.md)
- Dapr output bindings
  - [Dapr state](./functions-bindings-dapr-output-state.md)
  - [Dapr invoke](./functions-bindings-dapr-output-invoke.md)
  - [Dapr publish](./functions-bindings-dapr-output-publish.md)
  - [Dapr output](./functions-bindings-dapr-output.md)