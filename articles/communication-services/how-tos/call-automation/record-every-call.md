---
title: Record a call when it starts
titleSuffix: An Azure Communication Services how-to document
description: "In this how-to, you'll learn how to record a call through Azure Communication Services once it starts"
author: ddematheu2
manager: shahen
services: azure-communication-services
ms.author: dademath
ms.date: 02/09/2022
ms.topic: how-to
ms.service: azure-communication-services
---

# Record a call when it starts

Call recording is often used directly through the UI of a calling application, where the user triggers the recording. For applications within industries like banking or healthcare, call recording is required from the get-go. The service needs to automatically record for compliance purposes. This sample shows how to record a call when it starts. It uses Azure Communication Services and Azure Event Grid to trigger an Azure Function when a call starts. It automatically will record every call within your Azure Communication Services resource.

In this QuickStart, we will focus on showcasing the processing of call started events through Azure Functions using Event Grid triggers. We will leverage the Call Automation SDK for Azure Communication Services to start recording.

The Call Started event when a call start is formatted in the following way:

```json
[
  {
    "id": "a8bcd8a3-12d7-46ba-8cde-f6d0bda8feeb",
    "topic": "/subscriptions/{subscription-id}/resourcegroups/{group-name}/providers/microsoft.communication/communicationservices/{communication-services-resource-name}",
    "subject": "call/{serverCallId}/startedBy/8:acs:bc360ba8-d29b-4ef2-b698-769ebef85521_0000000c-1fb9-4878-07fd-0848220077e1",
    "data": {
      "startedBy": {
        "communicationIdentifier": {
          "rawId": "8:acs:bc360ba8-d29b-4ef2-b698-769ebef85521_0000000c-1fb9-4878-07fd-0848220077e1",
          "communicationUser": {
            "id": "8:acs:bc360ba8-d29b-4ef2-b698-769ebef85521_0000000c-1fb9-4878-07fd-0848220077e1"
          }
        },
        "role": "{role}"
      },
      "serverCallId": "{serverCallId}",
      "group": {
        "id": "00000000-0000-0000-0000-000000000000"
      },
      "room": {
        "id": "{roomId}"
      },
      "isTwoParty": false,
      "correlationId": "{correlationId}",
      "isRoomsCall": true
    },
    "eventType": "Microsoft.Communication.CallStarted",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-09-22T17:02:38.6905856Z"
  }
]
```

## Setting up our local environment

1. Using [Visual Studio Code](https://code.visualstudio.com/), install the [Azure Functions Extension](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions).
2. With the extension, create an Azure Function following these [instructions](https://learn.microsoft.com/azure/azure-functions/create-first-function-vs-code-node).

   Configure the function with the following instructions:
   - Language: C#
   - Template: Azure Event Grid Trigger
   - Function Name: User defined

    Once created, you will see a function created in your directory like this:

    ```csharp
    
    using System;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Extensions.EventGrid;
    using Microsoft.Extensions.Logging;
    using Azure.Messaging.EventGrid;
    using System.Threading.Tasks;
    
    namespace Company.Function
    {
        public static class acs_recording_test
        {
            [FunctionName("acs_recording_test")]
            public static async Task RunAsync([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
            {
                log.LogInformation(eventGridEvent.EventType);
            }
    
        }
    }

    ```

## Configure Azure Function to receive call started event

1. Configure switch statement within Azure Function to perform actions when the two different events we care about trigger. (Microsoft.Communication.CallStarted and Microsoft.Communication.RecordingFileStatus.Updated)

```csharp

    public static async Task RunAsync([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
    {
        if(eventGridEvent.EventType == "Microsoft.Communication.CallStarted")
        {
            log.LogInformation("Call started");
            var callEvent = eventGridEvent.Data.ToObjectFromJson<CallStartedEvent>();
        }
    }

```

## Start recording

1. Create a method to handle the call started events. This method will trigger recording to start when the call started.

```csharp

    public static async Task RunAsync([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
    {
        if(eventGridEvent.EventType == "Microsoft.Communication.CallStarted")
        {
            log.LogInformation("Call started");
            var callEvent = eventGridEvent.Data.ToObjectFromJson<CallStartedEvent>();
            await startRecordingAsync(callEvent.serverCallId);
        }
    }

    public static async Task startRecordingAsync (String serverCallId)
    {
        CallAutomationClient callAutomationClient = new CallAutomationClient(Environment.GetEnvironmentVariable("ACS_CONNECTION_STRING"));
        StartRecordingOptions recordingOptions = new StartRecordingOptions(new ServerCallLocator(serverCallId));
        recordingOptions.RecordingChannel = RecordingChannel.Mixed;
        recordingOptions.RecordingContent = RecordingContent.AudioVideo;
        recordingOptions.RecordingFormat = RecordingFormat.Mp4;        
        var startRecordingResponse = await callAutomationClient.GetCallRecording()
            .StartRecordingAsync(recordingOptions).ConfigureAwait(false);
    }

```

### Running locally

To run the function locally, you will simply press `F5` in Visual Studio Code. We will use [ngrok](https://ngrok.com/) to hook our locally running Azure Function with Azure Event Grid.

1. Once the function is running, we will configure ngrok. (You will need to [download ngrok](https://ngrok.com/download) for your environment.)

   ```bash

    ngrok http 7071

    ```

    Copy the ngrok link provided where your function is running.

2. Configure SMS events through Event Grid within your Azure Communication Services resource. We will do this using the [Azure CLI](https://learn.microsoft.com/cli/azure/install-azure-cli). You will need the resource id for your Azure Communication Services resource found in the Azure Portal.

    ```bash

    az eventgrid event-subscription create --name "<<EVENT_SUBSCRIPTION_NAME>>" --endpoint-type webhook --endpoint "<<NGROK URL>> " --source-resource-id "<<RESOURCE_ID>>"  --included-event-types Microsoft.Communication.SMSReceived 

    ```

3. Now that everything is hooked up, test the flow by sending an SMS to the phone number you have on your Azure Communication Services resource. You should see the console logs on your terminal where the function is running. If you added the code to respond to the SMS, you should see that text message delivered back to you.

### Deploy to Azure

To deploy the Azure Function to Azure, you will need to follow these [instructions](https://learn.microsoft.com/azure/azure-functions/create-first-function-vs-code-node#deploy-the-project-to-azure). Once deployed, we will configure Event Grid for the Azure Communication Services resource. With the URL for the Azure Function that was deployed (URL found in the Azure Portal under the function), we will run a similar command as above:

```bash

az eventgrid event-subscription update --name "<<EVENT_SUBSCRIPTION_NAME>>" --endpoint-type azurefunction --endpoint "<<AZ FUNCTION URL>> " --source-resource-id "<<RESOURCE_ID>>"

```

Since this is an update to the previous command we ran, make sure to use the same event subscription name you used above.

You can test as above, by sending an SMS to the phone number you have procured through Azure Communication Services resource.
