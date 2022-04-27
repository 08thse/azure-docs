---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/27/2022
ms.custom: devdivchpfy22	
ms.author: glenga
---
## Run the function in Azure

1. Back in the **Azure: Functions** area in the side bar, expand your subscription, your new function app, and **Functions**. Right-click (Windows) or <kbd>Ctrl -</kbd> click (macOS) the `HttpExample` function and select **Execute Function Now...**.

    :::image type="content" source="media/functions-vs-code-run-remote/execute-function-now.png" alt-text="Screenshot og Execute function now in Azure from Visual Studio Code.":::

1. In the **Enter request body** you'll see the request message body value as `{ "name": "Azure" }`. Press Enter to send this request message to your function.  

1. When the function executes in Azure and returns a response, a notification is raised in Visual Studio Code.
