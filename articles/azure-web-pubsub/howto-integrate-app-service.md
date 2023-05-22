---
title: Integrate - build a real-time collaborative whiteboard using Azure Web PubSub and deploy it to Azure App Service
description: A how-to guide about how to use Azure Web PubSub to enable real-time collaboration on a digital whiteboard and deploy as a Web App using Azure App Service
author: KevinGuo
ms.author: kevinguo-ed
ms.service: azure-web-pubsub
ms.custom: devx-track-azurecli
ms.topic: tutorial
ms.date: 05/17/2023
---
# How-to: build a real-time collaborative whiteboard using Azure Web PubSub and deploy it to Azure App Service

A new class of applications are re-imagining what modern work could be. While [Microsoft Word](https://www.microsoft.com/en-ww/microsoft-365/word) brings editors together, [Figma](https://www.figma.com) gathers up designers on the same creative endeavor. This class of applications builds on a user experience that makes us feel connected with our remote collaborators. From a technical point of view, user's activities need to be synchronized across users' screens at a low latency.

## Overview
In this how-to guide, we will take a cloud-native approach and leverage Azure services to build a real-time collaborative whiteboard and we will deploy the project as a Web App to Azure App Service. The whiteboard app is accessible in the browser and allows anyone can draw on the same canvas.

"GIF Missing - Finished project | Mobile and Browser"
> [!div class="nextstepaction"]
> [Check out live whiteboard demo](https://azure.github.io/azure-webpubsub/demos/whiteboard)

### Architecture

|Azure service name    | Purpose           | Benefits         | 
|----------------------|-------------------|------------------|
|[Azure App Service](https://learn.microsoft.com/en-us/azure/app-service/)  | Provides the hosting environment for the backend application, which is built with [Express](https://expressjs.com/) | Fully managed environment for application backends, with no need to worry about infrastructure where the code runs 
|[Azure Web PubSub](https://learn.microsoft.com/en-us/azure/azure-web-pubsub/overview) | Provides low-latency, bi-directional data exchange channel between the backend application and clients | Drastically reduces server load by freeing server from managing persistent WebSocket connections and scales to 100K concurrent client connections with just one resource

:::image type="content" source="./media/howto-integrate-app-service/architecture.jpg" alt-text="Architecture diagram of the collaborative whiteboard app":::

## Prerequisites
You can find detailed explanation of the [data flow](#data-flow) at the end of this how-to guide as we are going to focus on building and deloying the whiteboard app first.
 
In order to follow the step-by-step guide, you will need
> [!div class="checklist"]
> * An [Azure](https://portal.azure.com/) account. If you don't have an Azure subscription, create an [Azure free account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) before you begin.
> * [Azure CLI](/cli/azure/install-azure-cli) (version 2.29.0 or higher) or [Azure Cloud Shell](../cloud-shell/quickstart.md) to manage Azure resources.

## Create Azure resources using Azure CLI
# [1. Sign in](#tab/signin-cli)
1. Sign in to Azure CLI by running the following command.
    ```azurecli-interactive
    az login
    ```

1. Create a resource group on Azure.
    ```azurecli-interactive
    az group create \
      --location "westus" \  
      --name "whiteboard-group"
    ```

# [2. Create a Web App resource](#tab/create-web-app)
1. Create a free App Service plan.
    ```azurecli-interactive
    az appservice plan create \ 
      --resource-group "whiteboard-group" \ 
      --name "demo" \ 
      --sku FREE
      --is-linux
    ```

1. Create a Web App resource 
    ```azurecli-interactive
    az webapp create \
      --resource-group "whiteboard-group" \
      --name "whiteboard-app" \ 
      --plan "demo" \
      --runtime "NODE:18-lts"
    ```

# [3. Create a Web PubSub resource](#tab/create-web-pubsub)
1. Create a Web PubSub resource.
    ```azurecli-interactive
    az webpubsub create \
      --name "whiteboard-app" \
      --resource-group "whiteboard-group" \
      --location "westus" \
      --sku Free_F1
    ```

1. Show and store the value of `primaryConnectionString` somewhere for later use.
    ```azurecli-interactive
    az webpubsub key show \
      --name "whiteboard-app" \
      --resource-group "whiteboard-group"
    ```
---
## Get the application code
Run the following command to get a copy of the application code. You can find detailed explanation of the [data flow](#data-flow) at the end of this how-to guide.
```bash
git clone https://github.com/Azure/awps-webapp-sample.git
```

## Deploy the application to App Service
1. App Service supports many deployment workflows. For this guide, we are going to deploy a ZIP package. Run the following commands to prepare the ZIP.
```bash
npm install
npm run build
zip -r app.zip *
```

2. Use the following command to deploy it to Azure App Service.
```azurecli-interactive
az webapp deployment source config-zip \
  --resource-group "whiteboard-group" \
  --name "whiteboard-app" \
  --src app.zip
```

3. Set Azure Web PubSub connection string in the application settings. This is the `primaryConnectionString` you stored from an earlier step.
```azurecli-interactive
az webapp config appsettings set \
  --resource-group "whiteboard-group" \
  --name "whiteboard-app" \
  --setting Web_PubSub_ConnectionString="<primaryConnectionString>"
```

## Configure upstream server to handle events coming from Web PubSub
Whenever a client sends a message to Web PubSub service, the service sends an HTTP request to an endpoint you specify. This is the mechanism your backend server uses to further process messages, for example, if you can persist messages in a database of choice. 

As is with HTTP requests, Web PubSub service needs to know where to locate your application server. Since the backend application is now deployed to App Service, we get a publically accessible domain name for it. 
1. Show and store the value of `name` somewhere.
    ```azurecli-interactive
    az webapp config hostname list \
      --resource-group "whiteboard-group"
      --webapp-name "whiteboard-app" 
    ```

1. The endpoint we decided to expose on the backend server is [`/eventhandler`](https://github.com/Azure/awps-webapp-sample/blob/main/whiteboard/server.js#L17) and the `hub` name for whiteboard app [`"sample_draw"`](https://github.com/Azure/awps-webapp-sample/blob/main/whiteboard/server.js#l14)
    ```azurecli-interactive
    az webpubsub hub create \ 
      --resource-group "whiteboard-group" \
      --name "whiteboard-app" \
      --hub-name "sample_draw" \
      --event-handler url-template="https://<Replace with the hostname of your Web App resource>/eventhandler" user-event-pattern="*" system-event="connected" system-event="disconnected" 
    ```
> [!IMPORTANT]
> `url-template` has three parts: protocol + hostname + path, which in our case is `https://<The hostname of your Web App resource>/eventhandler`.

## View the whiteboard app in a broswer
Now head over to your browser and visit your deployed Web App. It is recommended to have multiple browser tabs open so that you can experience the real-time collaborative aspect of the app. Or better, share the link with a colleague or friend.

## Data flow
### Overview
The data flow section dives deeper into how the whiteboard app was built. The whiteboard app has two transport
- HTTP service written as an Express app and hosted on App Service. 
- WebSocket connections managed by Azure Web PubSub.
:::row:::
    :::column span="2":::
        The client, built with [Vue](https://vuejs.org/), makes an HTTP request for a Client Access Token to an endpoint `/negotiate`. The backend application is an [Express app](https://expressjs.com/) and hosted as a Web App using Azure App Service. {...Code link missing}
    :::column-end:::
    :::column:::
        :::image type="content" source="./media/howto-integrate-app-service/dataflow-1.jpg" alt-text="Step one of app data flow" lightbox="./media/howto-integrate-app-service/dataflow-1.jpg":::
    :::column-end:::
:::row-end:::

:::row:::
    :::column span="2":::
        When the backend application successfully returns the Client Access Token to the connecting client, the client uses it to establish a WebSocket connection with Azure Web PubSub.  {...Code link missing}
    :::column-end:::
    :::column:::
        :::image type="content" source="./media/howto-integrate-app-service/dataflow-2.jpg" alt-text="Step two of app data flow" lightbox="./media/howto-integrate-app-service/dataflow-2.jpg":::
    :::column-end:::
:::row-end:::

:::row:::
    :::column span="2":::
        If the handshake with Azure Web PubSub is successful, the client will be added to a group named `draw`, effectively subscribing to messages published to this group. Also, the client is given the permission to send messages to the `draw` group. {...Code link missing}
    :::column-end:::
    :::column:::
        :::image type="content" source="./media/howto-integrate-app-service/dataflow-3.jpg" alt-text="Step three of app data flow" lightbox="./media/howto-integrate-app-service/dataflow-3.jpg":::
    :::column-end:::
:::row-end:::
> [!NOTE]
> To keep this how-to guide focused, all connecting clients are added to the same group named `draw` and is given the permission to send messages to this group. To manage client connections at a granular level, see the full references of the APIs provided by Azure Web PubSub. {...missing link}

:::row:::
    :::column span="2":::
        The backend application is notified by Azure Web PubSub that a client has connected and it handles the `onConnected` event by calling the `sendToAll()`, with a payload of the latest number of connected clients.
    :::column-end:::
    :::column:::
        :::image type="content" source="./media/howto-integrate-app-service/dataflow-4.jpg" alt-text="Step four of app data flow" lightbox="./media/howto-integrate-app-service/dataflow-4.jpg":::
    :::column-end:::
:::row-end:::
> [!NOTE]
> It is important to note that if there are a large number of online users in the `draw` group, with **a single** network call from the backend application, all the online users will be notified that a new user has just joined. This drastically reduces the complexity and load of the backend application.

:::row:::
    :::column span="2":::
        As soon as a client establishes a persistent connection with Web PubSub, it makes an HTTP request to the backend application to fetch the latest shape and background data at `/diagram`. This demonstrates how an HTTP service hosted on App Service can be combined with Web PubSub, App Service being a scalable and highly available HTTP service and Web PubSub taking care of real-time communication.
    :::column-end:::
    :::column:::
        :::image type="content" source="./media/howto-integrate-app-service/dataflow-5.jpg" alt-text="Step five of app data flow" lightbox="./media/howto-integrate-app-service/dataflow-5.jpg":::
    :::column-end:::
:::row-end:::

:::row:::
    :::column span="2":::
        Now that the clients and backend application have two ways to exchange data. One is the conventional HTTP request-response cycle and the other is the persistent, bi-directional channel through Web PubSub. The drawing activities *(editing vector shapes)*, which originate from one user and need to be immediately broadcasted to all users, is managed by the backend application and delivered through Web PubSub. {code missing}
    :::column-end:::
    :::column:::
        :::image type="content" source="./media/howto-integrate-app-service/dataflow-6.jpg" alt-text="Step six of app data flow" lightbox="./media/howto-integrate-app-service/dataflow-6.jpg":::
    :::column-end:::
:::row-end:::

## Clean up resources
Although the application uses only the free tiers of both services, it is best practice to delete resources if you no longer need them. You can delete the resource group along with the resources in it using following command,

```azurecli-interactive
az group delete 
  --name "whiteboard-group"
```

## Next steps
> [!div class="nextstepaction"]
> [Check out more demos built with Web PubSub](https://azure.github.io/azure-webpubsub/)

