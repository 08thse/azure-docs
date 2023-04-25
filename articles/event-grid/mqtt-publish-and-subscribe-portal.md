---
title: 'Quickstart: Publish and Subscribe on an MQTT topic'
description: 'Quickstart guide to use Azure Event Grid MQTT and Azure portal to publish and subscribe MQTT messages on a topic'
ms.topic: quickstart
ms.date: 04/20/2023
author: veyaddan
ms.author: veyaddan
---

# Quickstart: Publish and subscribe to MQTT messages on Event Grid Namespace with Azure portal

Azure Event Grid supports messaging using the MQTT protocol.  Clients (both devices and cloud applications) can publish and subscribe MQTT messages over flexible hierarchical topics for scenarios such as high scale broadcast, and command & control.

In this article, you use the Azure CLI to do the following tasks:
1. Create an Event Grid Namespace and enable MQTT
2. Create subresources such as clients, topic spaces
3. Grant clients access to publish and subscribe to topic spaces
4. Publish and receive messages between clients

## Prerequisites
- If you don't have an [Azure subscription](https://learn.microsoft.com/en-us/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), create an [Azure free account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) before you begin.
- If you're new to Azure Event Grid, read through [Event Grid overview](https://learn.microsoft.com/en-us/azure/event-grid/overview) before starting this tutorial.
- Make sure that port 8883 is open in your firewall. The sample in this tutorial uses MQTT protocol, which communicates over port 8883. This port may be blocked in some corporate and educational network environments.
- You need an X.509 client certificate to generate the thumbprint and authenticate the client connection.
- Use the Bash environment in [Azure Cloud Shell](https://learn.microsoft.com/en-us/azure/cloud-shell/overview). For more information, see [Quickstart for Bash in Azure Cloud Shell](https://learn.microsoft.com/en-us/azure/cloud-shell/quickstart).
- If you prefer to run CLI reference commands locally, [install](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli) the Azure CLI. If you're running on Windows or macOS, consider running Azure CLI in a Docker container. For more information, see [How to run the Azure CLI in a Docker container](https://learn.microsoft.com/en-us/cli/azure/run-azure-cli-docker).
- If you're using a local installation, sign in to the Azure CLI by using the [az login](https://learn.microsoft.com/en-us/cli/azure/reference-index#az-login) command. To finish the authentication process, follow the steps displayed in your terminal. For other sign-in options, see [Sign in with the Azure CLI](https://learn.microsoft.com/en-us/cli/azure/authenticate-azure-cli).
- When you're prompted, install the Azure CLI extension on first use. For more information about extensions, see [Use extensions with the Azure CLI](https://learn.microsoft.com/en-us/cli/azure/azure-cli-extensions-overview).
- Run [az version](https://learn.microsoft.com/en-us/cli/azure/reference-index?#az-version) to find the version and dependent libraries that are installed. To upgrade to the latest version, run [az upgrade](https://learn.microsoft.com/en-us/cli/azure/reference-index?#az-upgrade).
- This article requires version 2.17.1 or later of the Azure CLI. If using Azure Cloud Shell, the latest version is already installed.


> [!IMPORTANT]
> The Azure [CLI Event Grid extension](https://learn.microsoft.com/en-us/cli/azure/eventgrid?view=azure-cli-latest) does not yet support namespaces and any of the resources it contains. We will use [Azure CLI resource](https://learn.microsoft.com/en-us/cli/azure/resource?view=azure-cli-latest) to create Event Grid resources.


## Create a Namespace
An Event Grid Namespace serves as an application container that can house resources such as clients, topic spaces.  It gives you a unique FQDN.

Save the Namespace object in namespace.json file in resources folder.

```json
{
    "properties": {
        "inputSchema": "CloudEventSchemaV1_0",
        "topicSpacesConfiguration": {
            "state": "Enabled",
        }
    },
    "location": "<region name>"
}
```

Use the az resource command to create a namespace.  Update the command with your subscription ID, Resource group ID, and a Namespace name.

```azurecli-interactive
az resource create --resource-type Microsoft.EventGrid/namespaces --id /subscriptions/<Subscription ID>/resourceGroups/<Resource Group>/providers/Microsoft.EventGrid/namespaces/<Namespace Name> --is-full-object --api-version 2023-06-01-preview --properties @./resources/namespace.json
```

> [!NOTE]
> To keep the QuickStart simple, you'll be creating a namespace with minimal properties. For detailed steps about configuring network, security, and other settings on other pages of the wizard, see Create a Namespace.

## Create clients

Store the object in client1.json file

```json
{
    "properties": {
        "state": "Enabled",
        "authenticationName": “client1-authnID", 
        "clientCertificateAuthentication": {
            "allowedThumbprints": [
"8E7968B9C434EAA8139BE58A21F2E7FB15D94C344B3B2ED8F8BC02E4C5FEB7E7"
            ]
         }
    }
}
```

Use the az resource command to create the first client.  Update the command with your subscription ID, Resource group ID, and a Namespace name.

```azurecli-interactive
az resource create --resource-type Microsoft.EventGrid/namespaces/clients --id /subscriptions/<Subscription ID>/resourceGroups/<Resource Group>/providers/Microsoft.EventGrid/namespaces/<Namespace Name>/clients/<Client Name> --is-full-object --api-version 2023-06-01-preview --properties @./resources/client1.json
```

Store the below object in client2.json file.  

```json
{
    "properties": {
        "state": "Enabled",
        "authenticationName": “client2-authn-ID", 
        "clientCertificateAuthentication": {
            "allowedThumbprints": [
"8E7968B9C434EAA8139BE58A21F2E7FB15D94C344B3B2ED8F8BC02E4C5FEB7E7"
            ]
         }
    }
}
```

Use the az resource command to create the second client.  Update the command with your subscription ID, Resource group ID, namespace and a client name.

```azurecli-interactive
az resource create --resource-type Microsoft.EventGrid/namespaces/clients --id /subscriptions/<Subscription ID>/resourceGroups/<Resource Group>/providers/Microsoft.EventGrid/namespaces/<Namespace Name>/clients/<Client Name> --is-full-object --api-version 2023-06-01-preview --properties @./resources/client2.json
```

> [!NOTE]
> For QuickStart, we use Thumbprint match for authentication.  For detailed steps on using X.509 CA certificate chain for client authentication, see [Client Authentication](./mqtt-client-authentication.md).

Also, we use the default $all client group, which includes all the clients in the namespace for this exercise.  To learn more about creating custom client groups using client attributes, see client groups.

## Create topic spaces

Store the below object in topicspace.json file.

```json
{ 
    "properties": {
        "topicTemplates": [
            "contosotopics/topic1"
        ]
    }
}
```

Use the az resource command to create the topic space.  Update the command with your subscription ID, Resource group ID, namespace name, and a topic space name.

```azurecli-interactive
az resource create --resource-type Microsoft.EventGrid/namespaces/topicSpaces --id /subscriptions/<Subscription ID>/resourceGroups/<Resource Group>/providers/Microsoft.EventGrid/namespaces/<Namespace Name>/topicSpaces/<Topic Space Name> --is-full-object --api-version 2023-06-01-preview --properties @./resources/topicspace.json
```

## Create PermissionBindings

Store the first permission binding object in permissionbinding1.json file.  Replace the topic space name with your topic space name.  This permission binding is for publisher.

```json
{
    "properties": {
        "clientGroupName": "$all",
        "permission": "Publisher”,
        "topicSpaceName": "<topicspace name>"
    }
}
```

Use the az resource command to create the first permission binding.  Update the command with your subscription ID, Resource group ID, namespace name, and a permission binding name.

```azurecli-interactive
az resource create --resource-type Microsoft.EventGrid/namespaces/permissionBindings --id /subscriptions/<Subscription ID>/resourceGroups/<Resource Group>/providers/Microsoft.EventGrid/namespaces/<Namespace Name>/permissionBindings/<Permission Binding Name> --api-version 2023-06-01-preview --properties @./resources/permissionbinding1.json
```

Store the second permission binding object in permissionbinding2.json file.  Replace the topic space name with your topic space name.  This permission binding is for subscriber.

```json
{
    "properties": {
        "clientGroupName": "$all",
        "permission": "Subscriber”,
        "topicSpaceName": "<topicspace name>"
    }
}
```

Use the az resource command to create the second permission binding.  Update the command with your subscription ID, Resource group ID, namespace name and a permission binding name.

```azurecli-interactive
az resource create --resource-type Microsoft.EventGrid/namespaces/permissionBindings --id /subscriptions/<Subscription ID>/resourceGroups/<Resource Group>/providers/Microsoft.EventGrid/namespaces/<Namespace Name>/permissionBindings/<Permission Binding Name> --api-version 2023-06-01-preview --properties @./resources/permissionbinding2.json
```

## Connect the client and publish / subscribe MQTT messages

The following sample code is a simple .NET publisher that attempts to connect, and publish to a namespace, and subscribes to the topic.  You can use the code to modify per your requirement and run the below code in Visual Studio or any of your favorite tools.  

You need to install the MQTTnet package (version 4.1.4.563) from NuGet to run the code.  
(In Visual Studio, right click on the project name in Solution Explorer, go to Manage NuGet packages, search for MQTTnet.  Select MQTTnet package and install.)


> [!NOTE]
>The following sample code is only for demonstration purposes and is not intended for production use.

**Sample C# code to connect a client, publish/subscribe MQTT message on a topic**

```csharp
using MQTTnet.Client;
using MQTTnet;
using System.Security.Cryptography.X509Certificates;

string hostname = "contosoegnamespace.eastus2-1.ts.eventgrid.azure.net";
string clientId = "client3-session1";  //client ID can be the session identifier.  A client can have multiple sessions using username and clientId.
string x509_pem = @" client certificate cer.pem file path\client.cer.pem";  //Provide your client certificate .cer.pem file path
string x509_key = @"client certificate key.pem file path\client.key.pem";  //Provide your client certificate .key.pem file path

var certificate = new X509Certificate2(X509Certificate2.CreateFromPemFile(x509_pem, x509_key).Export(X509ContentType.Pkcs12));

var mqttClient = new MqttFactory().CreateMqttClient();

var connAck = await mqttClient!.ConnectAsync(new MqttClientOptionsBuilder()
    .WithTcpServer(hostname, 8883)
    .WithClientId(clientId).WithCredentials(“client3-authnid”, "")  //use client authentication name in the username
    .WithTls(new MqttClientOptionsBuilderTlsParameters()
    {
        UseTls = true,
        Certificates = new X509Certificate2Collection(certificate)
    })

    .Build());

Console.WriteLine($"Client Connected: {mqttClient.IsConnected} with CONNACK: {connAck.ResultCode}");

mqttClient.ApplicationMessageReceivedAsync += async m => await Console.Out.WriteAsync($"Received message on topic: '{m.ApplicationMessage.Topic}' with content: '{m.ApplicationMessage.ConvertPayloadToString()}'\n\n");

var suback = await mqttClient.SubscribeAsync("contosotopics/topic1");
suback.Items.ToList().ForEach(s => Console.WriteLine($"subscribed to '{s.TopicFilter.Topic}' with '{s.ResultCode}'"));

while (true)
{
    var puback = await mqttClient.PublishStringAsync("contosotopics/topic1", "hello world!");
    Console.WriteLine(puback.ReasonString);
    await Task.Delay(1000);
}

```

You can replicate and modify the code for multiple clients to perform publish / subscribe among the clients.
