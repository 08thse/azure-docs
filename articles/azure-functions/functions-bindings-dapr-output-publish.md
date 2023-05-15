---
title: Dapr Publish output binding for Azure Functions
description: Learn how to provide Dapr Publish output binding data to an Azure Function.
ms.topic: reference
ms.date: 05/15/2023
ms.devlang: csharp, java, javascript, powershell, python
ms.custom: "devx-track-csharp, devx-track-python"
zone_pivot_groups: programming-languages-set-functions-lang-workers
---

# Dapr Publish output binding for Azure Functions

::: zone pivot="programming-language-csharp, programming-language-javascript, programming-language-python"

The publish output binding allows you to read Dapr data as output to an Azure Function.

For information on setup and configuration details, see the [overview](./functions-bindings-dapr.md).

::: zone-end

::: zone pivot="programming-language-csharp, programming-language-javascript, programming-language-python"

## Example

::: zone-end

::: zone pivot="programming-language-csharp"

A C# function can be created using one of the following C# modes:

- [In-process class library](./functions-dotnet-class-library.md): compiled C# function that runs in the same process as the Functions runtime. 
- [Isolated worker process class library](./dotnet-isolated-process-guide.md): compiled C# function that runs in a worker process that is isolated from the runtime. Isolated worker process is required to support C# functions running on non-LTS versions .NET and the .NET Framework.         

# [In-process](#tab/in-process)

The following example demonstrates using a Dapr publish output binding to perform a Dapr publish operation to a pub/sub component and topic. 

```csharp
[FunctionName("PublishOutputBinding")]
public static void Run(
    [HttpTrigger(AuthorizationLevel.Function, "post", Route = "topic/{topicName}")] HttpRequest req,
    [DaprPublish(PubSubName = "%PubSubName%", Topic = "{topicName}")] out DaprPubSubEvent pubSubEvent,
    ILogger log)
{
    string requestBody = new StreamReader(req.Body).ReadToEnd();
    pubSubEvent = new DaprPubSubEvent(requestBody);
}
```

# [Isolated process](#tab/isolated-process)

More samples for the Dapr output publish binding are available in the [GitHub repository](todo).

<!--

:::code language="csharp" source="https://www.github.com/azure/azure-functions-dapr-extension/samples/dotnet-isolated-azurefunction/OutputBinding/PublishOutputBinding.cs" range="8-39"::: 
-->

---

::: zone-end 

::: zone pivot="programming-language-javascript"

The following examples show Dapr triggers in a _function.json_ file and JavaScript code that uses those bindings. 

Here's the _function.json_ file for `daprPublish`:

```json
{
  "bindings": 
    {
      "type": "daprPublish",
      "direction": "out",
      "pubsubname": "messagebus",
      "topic": "{topicName}",
      "name": "payload"
    }
}
```

Here's the JavaScript code for the Dapr Publish output binding trigger:

```javascript
module.exports = async function (context, req) {
    context.log("Node HTTP trigger function processed a request.");
    context.bindings.payload = { payload: req.body };
    context.done(null);
};
```

::: zone-end

::: zone pivot="programming-language-python"

The following example shows a Dapr Publish output binding, which uses the [v1 Python programming model](functions-reference-python.md).

Here's the _function.json_ file for `daprPublish`:

```json
{
  "scriptFile": "__init__.py",
  "bindings": 
    {
      "type": "daprPublish",
      "direction": "out",
      "name": "pubEvent",
      "pubsubname": "messagebus",
      "topic": "B"
    }
}
```

For more information about *function.json* file properties, see the [Configuration](#configuration) section explains these properties.

Here's the Python code:

```python
def main(subEvent,
         pubEvent: func.Out[bytes]) -> None:
    logging.info('Python function processed a TransferEventBetweenTopics request from the Dapr Runtime.')
    subEvent_json = json.loads(subEvent)
    payload = "Transfer from Topic A: " + str(subEvent_json["data"])
    pubEvent.set(json.dumps({"payload": payload }))
```

::: zone-end

::: zone pivot="programming-language-csharp"

## Attributes
Both in-process and isolated process C# libraries use the <!--attribute API here--> attribute to define the function.

# [In-process](#tab/in-process)

In [C# class libraries], use the `HttpTrigger` to trigger a Dapr Publish output binding, which supports the following properties.

|function.json property | Description|
|---------|----------------------|
| **PubSubName** | The name of the Dapr pub/sub to send the message. |
| **Topic** | The name of the Dapr topic to send the message. |

# [Isolated process](#tab/isolated-process)

The following table explains the parameters for the `DaprPublish`.

TODO: table has in-proc parameters - need out-of-proc

|function.json property | Description|
|---------|----------------------|
| **PubSubName** | The name of the Dapr pub/sub to send the message. |
| **Topic** | The name of the Dapr topic to send the message. |


---

::: zone-end

::: zone pivot="programming-language-javascript, programming-language-python"

## Configuration
The following table explains the binding configuration properties that you set in the _function.json_ file.

|function.json property | Description|
|---------|----------------------|
|**type** | Must be set to `daprPublish`. |
|**direction** | Must be set to `out`. |
|**name** | The name of the variable that represents the Dapr data in function code. |
|**pubsubname** | The name of the pub/sub component service. |
|**topic** | The name of the pub/sub topic. |

::: zone-end

::: zone pivot="programming-language-csharp"

See the [Example section](#example) for complete examples.

## Usage
The parameter type supported by the Dapr Publish output binding depends on the Functions runtime version, the extension package version, and the C# modality used.

TODO: Need usage content. 

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
The parameter type supported by the Dapr Publish output binding depends on the Functions runtime version, the extension package version, and the C# modality used.

TODO: Need usage content. 

::: zone-end

::: zone pivot="programming-language-python"

See the [Example section](#example) for complete examples.

## Usage
The parameter type supported by the Dapr Publish output binding depends on the Functions runtime version, the extension package version, and the C# modality used.

TODO: Need usage content. 

::: zone-end

<!---## Extra sections Put any sections with content that doesn't fit into the above section headings down here. This will likely get moved to another article after the refactor. -->

::: zone pivot="programming-language-java,programming-language-powershell"

> [!NOTE]
> Currently, Dapr triggers and bindings are only supported in C#, JavaScript, and Python. 

::: zone-end

## Next steps
- Triggers 
  - [Dapr input binding](./functions-bindings-dapr-trigger-input.md)
  - [Dapr service invocation](./functions-bindings-dapr-trigger-svc-invoke.md)
  - [Dapr topic](./functions-bindings-dapr-trigger-topic.md)
- Input
  - [Dapr state](./functions-bindings-dapr-input-state.md)
  - [Dapr secret](./functions-bindings-dapr-input-secret.md)
- Dapr output bindings
  - [Dapr state](./functions-bindings-dapr-output-state.md)
  - [Dapr invoke](./functions-bindings-dapr-output-invoke.md)
  - [Dapr output](./functions-bindings-dapr-output.md)

