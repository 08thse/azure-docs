---
title: Dapr Service Invocation trigger for Azure Functions
description: Learn how to run an Azure Function as Dapr service invocation data changes.
ms.topic: reference
ms.date: 08/07/2023
ms.devlang: csharp, java, javascript, powershell, python
ms.custom: "devx-track-csharp, devx-track-python"
zone_pivot_groups: programming-languages-set-functions-lang-workers
---

# Dapr Service Invocation trigger for Azure Functions

::: zone pivot="programming-language-csharp,programming-language-java,programming-language-javascript,programming-language-python,programming-language-powershell"

[!INCLUDE [preview-support](../../includes/functions-dapr-support-limitations.md)]

Azure Functions can be triggered using the following Dapr events.

There are no templates for triggers in Dapr in the functions tooling today. Start your project with another trigger type (e.g. Storage Queues) and then modify the function.json or attributes.

::: zone-end

::: zone pivot="programming-language-csharp, programming-language-javascript, programming-language-powershell, programming-language-python"

## Example

::: zone-end

::: zone pivot="programming-language-csharp"

A C# function can be created using one of the following C# modes:

[!INCLUDE [dotnet-execution](../../includes/functions-dotnet-execution-model.md)]
   

# [In-process](#tab/in-process)

```csharp
[FunctionName("CreateNewOrder")]
public static void Run(
    [DaprServiceInvocationTrigger] JObject payload,
    [DaprState("%StateStoreName%", Key = "order")] out JToken order,
    ILogger log)
{
    log.LogInformation("C# function processed a CreateNewOrder request from the Dapr Runtime.");

    // payload must be of the format { "data": { "value": "some value" } }
    order = payload["data"];
}
```

# [Isolated process](#tab/isolated-process)

:::code language="csharp" source="~/azure-functions-dapr-extension/samples/dotnet-isolated-azurefunction/OutputBinding/CreateNewOrder.cs" range="18-31"::: 
 

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

For more information about *function.json* file properties, see the [Configuration](#configuration) section.

Here's the JavaScript code:

```javascript
module.exports = async function (context) {
    context.log("Node function processed a RetrieveOrder request from the Dapr Runtime.");

    // print the fetched state value
    context.log(context.bindings.data);
};
```
::: zone-end

::: zone pivot="programming-language-powershell"

The following examples show Dapr triggers in a _function.json_ file and PowerShell code that uses those bindings. 

Here's the _function.json_ file for `daprServiceInvocationTrigger`:

```json
{
  "bindings": [
    {
      "type": "daprServiceInvocationTrigger",
      "name": "payload",
      "direction": "in"
    }
  ]
}
```

For more information about *function.json* file properties, see the [Configuration](#configuration) section.

In code:

```powershell
using namespace System
using namespace Microsoft.Azure.WebJobs
using namespace Microsoft.Extensions.Logging
using namespace Microsoft.Azure.WebJobs.Extensions.Dapr
using namespace Newtonsoft.Json.Linq

param (
    $payload
)

# C# function processed a CreateNewOrder request from the Dapr Runtime.
Write-Host "PowerShell function processed a CreateNewOrder request from the Dapr Runtime."

# Payload must be of the format { "data": { "value": "some value" } }

# Convert the object to a JSON-formatted string with ConvertTo-Json
$jsonString = $payload| ConvertTo-Json

# Associate values to output bindings by calling 'Push-OutputBinding'.
Push-OutputBinding -Name order -Value $payload["data"]
```

::: zone-end

::: zone pivot="programming-language-python"

# [Python v2](#tab/v2)

The following example shows a Dapr Service Invocation trigger, which uses the [v2 Python programming model](functions-reference-python.md). To use the `daprServiceInvocationTrigger` in your Python function app code:

```python
import logging
import json
import azure.functions as func

dapp = func.DaprFunctionApp()

@dapp.function_name(name="RetrieveOrder")
@dapp.dapr_service_invocation_trigger(arg_name="payload", method_name="RetrieveOrder")
@dapp.dapr_state_input(arg_name="data", state_store="statestore", key="order")
def main(payload, data: str) :
    # Function should be invoked with this command: dapr invoke --app-id functionapp --method RetrieveOrder  --data '{}'
    logging.info('Python function processed a RetrieveOrder request from the Dapr Runtime.')
    logging.info(data)
```

 
# [Python v1](#tab/v1)

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

For more information about *function.json* file properties, see the [Configuration](#configuration) section.

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

# [In-process](#tab/in-process)

In [C# class libraries](./functions-dotnet-class-library.md), use the `DaprServiceInvocationTrigger` to trigger a Dapr service invocation binding, which supports the following properties.

| Parameter | Description | 
| --------- | ----------- | 
| **MethodName** | _Optional._ The name of the method the Dapr caller should use. If not specified, the name of the function is used as the method name. | 

# [Isolated process](#tab/isolated-process)

The following table explains the parameters for the `DaprServiceInvocationTrigger`.

| Parameter | Description | 
| --------- | ----------- | 
| **MethodName** | _Optional._ The name of the method the Dapr caller should use. If not specified, the name of the function is used as the method name. | 

---

::: zone-end

::: zone pivot="programming-language-javascript, programming-language-powershell"

## Configuration
The following table explains the binding configuration properties that you set in the function.json file.

|function.json property | Description|
|---------|----------------------|
|**type** | Must be set to `daprServiceInvocationTrigger`.|
|**name** | The name of the variable that represents the Dapr data in function code. |
|**direction** | Must be set to `in`.  |

::: zone-end

::: zone pivot="programming-language-python"

## Configuration

# [Python v2](#tab/v2)

The following table explains the binding configuration properties for `@dapp.dapr_service_invocation_trigger` that you set in your Python code.

|Property | Description|
|---------|----------------------|
|**arg_name** | The argument name. In the example, this value is set to `payload`. |
|**method_name** | The name of the variable that represents the Dapr data. |

# [Python v1](#tab/v1)

The following table explains the binding configuration properties that you set in the function.json file.

|function.json property | Description|
|---------|----------------------|
|**type** | Must be set to `daprServiceInvocationTrigger`.|
|**name** | The name of the variable that represents the Dapr data in function code. |
|**direction** | Must be set to `in`.  |

---

::: zone-end

::: zone pivot="programming-language-csharp"

See the [Example section](#example) for complete examples.

## Usage
To use a Dapr Service Invocation trigger, run `DaprServiceInvocationTrigger`. 

You can learn more about which components to use with the Service Invocation trigger and how to set them up in the official Dapr documentation.

- [Dapr component specs](https://docs.dapr.io/reference/components-reference/)
- [Dapr service invocation](https://docs.dapr.io/developing-applications/building-blocks/service-invocation/)

::: zone-end

::: zone pivot="programming-language-javascript, programming-language-powershell, programming-language-python"

See the [Example section](#example) for complete examples.

## Usage
To use a Dapr Service Invocation trigger, define your `daprServiceInvocationTrigger` binding in a functions.json file.  

You can learn more about which components to use with the Service Invocation trigger and how to set them up in the official Dapr documentation.

- [Dapr component specs](https://docs.dapr.io/reference/components-reference/)
- [Dapr service invocation](https://docs.dapr.io/developing-applications/building-blocks/service-invocation/)

::: zone-end

## Next steps

Choose one of the following links to review the reference article for a specific Dapr binding type:

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