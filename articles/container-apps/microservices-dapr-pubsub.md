---
title: "Microservices communication using Dapr Service Invocation"
description: Enable two sample Dapr applications to communicate and leverage Azure Container Apps.
author: hhunter-ms
ms.author: hannahhunter
ms.service: container-apps
ms.topic: how-to
ms.date: 02/06/2023
zone_pivot_group_filename: container-apps/dapr-zone-pivot-groups.json
zone_pivot_groups: dapr-languages-set
---

# Microservices communication using Dapr Service Invocation 

[Dapr](https://dapr.io/) (Distributed Application Runtime) is a runtime that helps you build resilient stateless and stateful microservices. While you can deploy and manage the Dapr OSS project yourself, deploying your Dapr applications to the Container Apps platform:

- Provides a managed and supported Dapr integration
- Seamlessly updates Dapr versions
- Exposes a simplified Dapr interaction model to increase developer productivity

In this tutorial, you'll:
> [!div class="checklist"]
> * Create a publisher microservice and a subscriber microservice that communicate locally via a Redis topic to demonstrate how [Dapr enables a publish-subcribe pattern](https://docs.dapr.io/developing-applications/building-blocks/pubsub/pubsub-overview/). 
> * Redeploy the same services to communicate via Azure Service Bus using `azd up` to Azure Container Apps via the Azure Developer CLI. 

The Pub/sub API enables microservices to communicate with each other using messages for event-driven architectures. The sample Dapr pub/sub project includes:
1. A message generator (publisher) `checkout` service that generates messages of a specific topic.
1. A (subscriber) `order-processor` service that listens for messages from the `checkout` service of a specific topic. 

:::image type="content" source="media/microservices-dapr-azd/pubsub-diagram.png" alt-text="Diagram of the Dapr pub/sub sample.":::

> [!NOTE]
> This tutorial uses [Azure Developer CLI (`azd`)](/developer/azure-developer-cli/overview.md), which is currently in preview. Preview features are available on a self-service, opt-in basis. Previews are provided "as is" and "as available," and they're excluded from the service-level agreements and limited warranty. The `azd` previews are partially covered by customer support on a best-effort basis.

## Prerequisites

- Install [Azure Developer CLI](/developer/azure-developer-cli/install-azd.md)
- [Install](https://docs.dapr.io/getting-started/install-dapr-cli/) and [init](https://docs.dapr.io/getting-started/install-dapr-selfhost/) Dapr
- [Docker Desktop](https://www.docker.com/products/docker-desktop/)
- Install [Git](https://git-scm.com/downloads)

::: zone pivot="nodejs"

## Run the Dapr applications locally with Node.js

### Prepare the project

1. Clone the [sample Dapr application](https://github.com/Azure-Samples/pubsub-dapr-nodejs-servicebus) to your local machine.

   ```bash
   git clone https://github.com/Azure-Samples/pubsub-dapr-nodejs-servicebus.git
   ```

1. Navigate into the sample's root directory.

   ```bash
   cd pubsub-dapr-nodejs-servicebus
   ```

### Run the Dapr applications using the Dapr CLI

Start by running the `order-processor` subscriber service with Dapr.

1. From the sample's root directory, change directories to `order-processor`.

   ```bash
   cd order-processor
   ```
1. Install the dependencies:

   ```bash
   npm install
   ```

1. Run the `order-processor` service with Dapr.

   ```bash
   dapr run --app-port 5001 --app-id order-processing --app-protocol http --dapr-http-port 3501 --components-path ../components -- npm run start
   ```

1. In a new terminal window, from the sample's root directory, navigate to the `checkout` publisher service.

   ```bash
   cd checkout
   ```

1. Install the dependencies:

   ```bash
   npm install
   ```

1. Run the `checkout` service with Dapr.

   ```bash
   dapr run --app-id checkout --app-protocol http --components-path ../components -- npm run start
   ```

   **Expected output:**

   In both terminals, 10 messages are published and received before exiting the application:

   `checkout` output:

   ```
   == APP == Published data: {"orderId":1}
   == APP == Published data: {"orderId":2}
   == APP == Published data: {"orderId":3}
   == APP == Published data: {"orderId":4}
   == APP == Published data: {"orderId":5}
   == APP == Published data: {"orderId":6}
   == APP == Published data: {"orderId":7}
   == APP == Published data: {"orderId":8}
   == APP == Published data: {"orderId":9}
   == APP == Published data: {"orderId":10}
   ```

   `order-processor` output:

   ```
   == APP == Subscriber received: {"orderId":1}
   == APP == Subscriber received: {"orderId":2}
   == APP == Subscriber received: {"orderId":3}
   == APP == Subscriber received: {"orderId":4}
   == APP == Subscriber received: {"orderId":5}
   == APP == Subscriber received: {"orderId":6}
   == APP == Subscriber received: {"orderId":7}
   == APP == Subscriber received: {"orderId":8}
   == APP == Subscriber received: {"orderId":9}
   == APP == Subscriber received: {"orderId":10}
   ```

1. Make sure both applications have stopped by running the following commands. In the checkout terminal:

   ```sh
   dapr stop --app-id checkout
   ```

   In the order-processor terminal:

   ```sh
   dapr stop --app-id order-processor
   ```

## Deploy the Dapr application template using `azd`

Deploy the Dapr application to Azure Container Apps using [`azd`](/developer/azure-developer-cli/overview.md).

### Prepare the project

1. In a new terminal window, navigate into the [sample's](https://github.com/Azure-Samples/pubsub-dapr-nodejs-servicebus) root directory.

   ```bash
   cd pubsub-dapr-nodejs-servicebus
   ```

### Run using Azure Developer CLI

1. Provision the infrastructure and deploy the Dapr application to Azure Container Apps:

   ```azdeveloper
   azd up
   ```

1. When prompted in the terminal, provide the following parameters:

   | Parameter | Description |
   | --------- | ----------- |
   | `Environment Name` | Prefix for the resource group that will be created to hold all Azure resources. |
   | `Azure Location`   | The Azure location where your resources will be deployed. |
   | `Azure Subscription` | The Azure Subscription where your resources will be deployed. |

   This process may take some time to complete, as the `azd up` command:

   - Initializes your project (azd init)
   - Creates and configures all necessary Azure resources (azd provision)
   - Deploys the code (azd deploy)

   As the `azd up` command completes, the CLI output displays two Azure portal links to monitor the deployment progress.

   **Expected output:**

   ```azdeveloper
   Initializing a new project (azd init)
   
   
   Provisioning Azure resources (azd provision)
   Provisioning Azure resources can take some time
   
     You can view detailed progress in the Azure Portal:
     https://portal.azure.com
   
     (✓) Done: Resource group: resource-group-name
     (✓) Done: Application Insights: app-insights-name
     (✓) Done: Portal dashboard: portal-dashboard-name
     (✓) Done: Log Analytics workspace: log-analytics-name
     (✓) Done: Key vault: key-vault-name
     (✓) Done: Container Apps Environment: ca-env-name
     (✓) Done: Container App: ca-checkout-name
     (✓) Done: Container App: ca-orders-name
   
   
   Deploying services (azd deploy)
   
     (✓) Done: Deploying service checkout
     (✓) Done: Deploying service orders
     - Endpoint: https://ca-orders-name.endpoint.region.azurecontainerapps.io/
   
   SUCCESS: Your Azure app has been deployed!
   You can view the resources created under the resource group hhhhhunter-azd-rg in Azure Portal:
   https://portal.azure.com/#@/resource/subscriptions/subscription-id/resourceGroups/resource-group-name/overview
   ```

### Confirm successful deployment 

In the Azure portal, verify the `checkout` service is publishing messages to the Azure Service Bus topic. 

1. Copy the `checkout` Container App name from the terminal output.

1. Navigate to the [Azure portal](https://ms.portal.azure.com) and search for the Container App resource by name.

1. In the Container App dashboard, select **Monitoring** > **Log stream**.

1. Confirm the `checkout` container is logging the same output as in the terminal earlier.

   :::image type="content" source="media/microservices-dapr-azd/log-streams-portal-view-checkout-svc.png" alt-text="Screenshot of the checkout service container's log stream in the Azure portal.":::

1. Do the same for the `order-processor` service to verify that it's receiving messages from the subscribed Azure Service Bus topic.

   :::image type="content" source="media/microservices-dapr-azd/log-streams-portal-view-order-processor-svc.png" alt-text="Screenshot of the order processor service container's log stream in the Azure portal.":::


## What happened?

Upon successful completion of the `azd up` command:

- The Azure resources referenced in the [sample project's `./infra` directory](https://github.com/Azure-Samples/pubsub-dapr-nodejs-servicebus/tree/main/infra) have been provisioned to the Azure subscription you specified. You can now view those Azure resources via the Azure portal.
- The app has been built and deployed to Azure Container Apps. Using the web app URL output from the `azd up` command, you can browse to the fully functional app.


::: zone-end

::: zone pivot="python"

## Run the Dapr applications locally with Python

### Prepare the project

1. Clone the [sample Dapr application](https://github.com/Azure-Samples/pubsub-dapr-python-servicebus) to your local machine.

   ```bash
   git clone https://github.com/Azure-Samples/pubsub-dapr-python-servicebus.git
   ```

1. Navigate into the sample's root directory.

   ```bash
   cd pubsub-dapr-python-servicebus
   ```

### Run the Dapr applications using the Dapr CLI

Start by running the `order-processor` subscriber service with Dapr.

1. From the sample's root directory, change directories to `order-processor`.

   ```bash
   cd order-processor
   ```
1. Install the dependencies:

   ```bash
   pip3 install -r requirements.txt
   ```

1. Run the `order-processor` service with Dapr.

   ```bash
   dapr run --app-id order-processor --components-path ../components/ --app-port 5001 -- python3 app.py
   ```

1. In a new terminal window, from the sample's root directory, navigate to the `checkout` publisher service.

   ```bash
   cd checkout
   ```

1. Install the dependencies:

   ```bash
   pip3 install -r requirements.txt
   ```

1. Run the `checkout` service with Dapr.

   ```bash
   dapr run --app-id checkout --components-path ../components/ -- python3 app.py
   ```

   **Expected output:**

   In both terminals, 10 messages are published and received before exiting the application:

   `checkout` output:

   ```
   == APP == Published data: {"orderId":1}
   == APP == Published data: {"orderId":2}
   == APP == Published data: {"orderId":3}
   == APP == Published data: {"orderId":4}
   == APP == Published data: {"orderId":5}
   == APP == Published data: {"orderId":6}
   == APP == Published data: {"orderId":7}
   == APP == Published data: {"orderId":8}
   == APP == Published data: {"orderId":9}
   == APP == Published data: {"orderId":10}
   ```

   `order-processor` output:

   ```
   == APP == Subscriber received: {"orderId":1}
   == APP == Subscriber received: {"orderId":2}
   == APP == Subscriber received: {"orderId":3}
   == APP == Subscriber received: {"orderId":4}
   == APP == Subscriber received: {"orderId":5}
   == APP == Subscriber received: {"orderId":6}
   == APP == Subscriber received: {"orderId":7}
   == APP == Subscriber received: {"orderId":8}
   == APP == Subscriber received: {"orderId":9}
   == APP == Subscriber received: {"orderId":10}
   ```

1. Make sure both applications have stopped by running the following commands. In the checkout terminal:

   ```sh
   dapr stop --app-id checkout
   ```

   In the order-processor terminal:

   ```sh
   dapr stop --app-id order-processor
   ```

## Deploy the Dapr application template using `azd`

Deploy the Dapr application to Azure Container Apps using [`azd`](/developer/azure-developer-cli/overview.md).

### Prepare the project

1. In a new terminal window, navigate into the [sample's](https://github.com/Azure-Samples/pubsub-dapr-python-servicebus) root directory.

   ```bash
   cd pubsub-dapr-python-servicebus
   ```

### Run using Azure Developer CLI

1. Provision the infrastructure and deploy the Dapr application to Azure Container Apps:

   ```azdeveloper
   azd up
   ```

1. When prompted in the terminal, provide the following parameters:

   | Parameter | Description |
   | --------- | ----------- |
   | `Environment Name` | Prefix for the resource group that will be created to hold all Azure resources. |
   | `Azure Location`   | The Azure location where your resources will be deployed. |
   | `Azure Subscription` | The Azure Subscription where your resources will be deployed. |

   This process may take some time to complete, as the `azd up` command:

   - Initializes your project (azd init)
   - Creates and configures all necessary Azure resources (azd provision)
   - Deploys the code (azd deploy)

   As the `azd up` command completes, the CLI output displays two Azure portal links to monitor the deployment progress.

   **Expected output:**

   ```azdeveloper
   Initializing a new project (azd init)
   
   
   Provisioning Azure resources (azd provision)
   Provisioning Azure resources can take some time
   
     You can view detailed progress in the Azure Portal:
     https://portal.azure.com
   
     (✓) Done: Resource group: resource-group-name
     (✓) Done: Application Insights: app-insights-name
     (✓) Done: Portal dashboard: portal-dashboard-name
     (✓) Done: Log Analytics workspace: log-analytics-name
     (✓) Done: Key vault: key-vault-name
     (✓) Done: Container Apps Environment: ca-env-name
     (✓) Done: Container App: ca-checkout-name
     (✓) Done: Container App: ca-orders-name
   
   
   Deploying services (azd deploy)
   
     (✓) Done: Deploying service checkout
     (✓) Done: Deploying service orders
     - Endpoint: https://ca-orders-name.endpoint.region.azurecontainerapps.io/
   
   SUCCESS: Your Azure app has been deployed!
   You can view the resources created under the resource group hhhhhunter-azd-rg in Azure Portal:
   https://portal.azure.com/#@/resource/subscriptions/subscription-id/resourceGroups/resource-group-name/overview
   ```

### Confirm successful deployment 

In the Azure portal, verify the `checkout` service is publishing messages to the Azure Service Bus topic. 

1. Copy the `checkout` Container App name from the terminal output.

1. Navigate to the [Azure portal](https://ms.portal.azure.com) and search for the Container App resource by name.

1. In the Container App dashboard, select **Monitoring** > **Log stream**.

1. Confirm the `checkout` container is logging the same output as in the terminal earlier.

   :::image type="content" source="media/microservices-dapr-azd/log-streams-portal-view-checkout-svc.png" alt-text="Screenshot of the checkout service container's log stream in the Azure portal.":::

1. Do the same for the `order-processor` service to verify that it's receiving messages from the subscribed Azure Service Bus topic.

   :::image type="content" source="media/microservices-dapr-azd/log-streams-portal-view-order-processor-svc.png" alt-text="Screenshot of the order processor service container's log stream in the Azure portal.":::


## What happened?

Upon successful completion of the `azd up` command:

- The Azure resources referenced in the [sample project's `./infra` directory](https://github.com/Azure-Samples/pubsub-dapr-python-servicebus/tree/main/infra) have been provisioned to the Azure subscription you specified. You can now view those Azure resources via the Azure portal.
- The app has been built and deployed to Azure Container Apps. Using the web app URL output from the `azd up` command, you can browse to the fully functional app.


::: zone-end

::: zone pivot="csharp"

## Run the Dapr applications locally with Python

### Prepare the project

1. Clone the [sample Dapr application](https://github.com/Azure-Samples/pubsub-dapr-csharp-servicebus) to your local machine.

   ```bash
   git clone https://github.com/Azure-Samples/pubsub-dapr-csharp-servicebus.git
   ```

1. Navigate into the sample's root directory.

   ```bash
   cd pubsub-dapr-csharp-servicebus
   ```

### Run the Dapr applications using the Dapr CLI

Start by running the `order-processor` subscriber service with Dapr.

1. From the sample's root directory, change directories to `order-processor`.

   ```bash
   cd order-processor
   ```
1. Install the dependencies:

   ```bash
   dotnet restore
   dotnet build
   ```

1. Run the `order-processor` service with Dapr.

   ```bash
   dapr run --app-id order-processor --components-path ../components/ --app-port 7001 -- dotnet run --project .
   ```

1. In a new terminal window, from the sample's root directory, navigate to the `checkout` publisher service.

   ```bash
   cd checkout
   ```

1. Install the dependencies:

   ```bash
   dotnet restore
   dotnet build
   ```

1. Run the `checkout` service with Dapr.

   ```bash
   dapr run --app-id checkout --components-path ../components/ -- dotnet run --project .
   ```

   **Expected output:**

   In both terminals, 10 messages are published and received before exiting the application:

   `checkout` output:

   ```
   == APP == Published data: {"orderId":1}
   == APP == Published data: {"orderId":2}
   == APP == Published data: {"orderId":3}
   == APP == Published data: {"orderId":4}
   == APP == Published data: {"orderId":5}
   == APP == Published data: {"orderId":6}
   == APP == Published data: {"orderId":7}
   == APP == Published data: {"orderId":8}
   == APP == Published data: {"orderId":9}
   == APP == Published data: {"orderId":10}
   ```

   `order-processor` output:

   ```
   == APP == Subscriber received: {"orderId":1}
   == APP == Subscriber received: {"orderId":2}
   == APP == Subscriber received: {"orderId":3}
   == APP == Subscriber received: {"orderId":4}
   == APP == Subscriber received: {"orderId":5}
   == APP == Subscriber received: {"orderId":6}
   == APP == Subscriber received: {"orderId":7}
   == APP == Subscriber received: {"orderId":8}
   == APP == Subscriber received: {"orderId":9}
   == APP == Subscriber received: {"orderId":10}
   ```

1. Make sure both applications have stopped by running the following commands. In the checkout terminal:

   ```sh
   dapr stop --app-id checkout
   ```

   In the order-processor terminal:

   ```sh
   dapr stop --app-id order-processor
   ```

## Deploy the Dapr application template using `azd`

Deploy the Dapr application to Azure Container Apps using [`azd`](/developer/azure-developer-cli/overview.md).

### Prepare the project

1. In a new terminal window, navigate into the [sample's](https://github.com/Azure-Samples/pubsub-dapr-csharp-servicebus) root directory.

   ```bash
   cd pubsub-dapr-csharp-servicebus
   ```

### Run using Azure Developer CLI

1. Provision the infrastructure and deploy the Dapr application to Azure Container Apps:

   ```azdeveloper
   azd up
   ```

1. When prompted in the terminal, provide the following parameters:

   | Parameter | Description |
   | --------- | ----------- |
   | `Environment Name` | Prefix for the resource group that will be created to hold all Azure resources. |
   | `Azure Location`   | The Azure location where your resources will be deployed. |
   | `Azure Subscription` | The Azure Subscription where your resources will be deployed. |

   This process may take some time to complete, as the `azd up` command:

   - Initializes your project (azd init)
   - Creates and configures all necessary Azure resources (azd provision)
   - Deploys the code (azd deploy)

   As the `azd up` command completes, the CLI output displays two Azure portal links to monitor the deployment progress.

   **Expected output:**

   ```azdeveloper
   Initializing a new project (azd init)
   
   
   Provisioning Azure resources (azd provision)
   Provisioning Azure resources can take some time
   
     You can view detailed progress in the Azure Portal:
     https://portal.azure.com
   
     (✓) Done: Resource group: resource-group-name
     (✓) Done: Application Insights: app-insights-name
     (✓) Done: Portal dashboard: portal-dashboard-name
     (✓) Done: Log Analytics workspace: log-analytics-name
     (✓) Done: Key vault: key-vault-name
     (✓) Done: Container Apps Environment: ca-env-name
     (✓) Done: Container App: ca-checkout-name
     (✓) Done: Container App: ca-orders-name
   
   
   Deploying services (azd deploy)
   
     (✓) Done: Deploying service checkout
     (✓) Done: Deploying service orders
     - Endpoint: https://ca-orders-name.endpoint.region.azurecontainerapps.io/
   
   SUCCESS: Your Azure app has been deployed!
   You can view the resources created under the resource group hhhhhunter-azd-rg in Azure Portal:
   https://portal.azure.com/#@/resource/subscriptions/subscription-id/resourceGroups/resource-group-name/overview
   ```

### Confirm successful deployment 

In the Azure portal, verify the `checkout` service is publishing messages to the Azure Service Bus topic. 

1. Copy the `checkout` Container App name from the terminal output.

1. Navigate to the [Azure portal](https://ms.portal.azure.com) and search for the Container App resource by name.

1. In the Container App dashboard, select **Monitoring** > **Log stream**.

1. Confirm the `checkout` container is logging the same output as in the terminal earlier.

   :::image type="content" source="media/microservices-dapr-azd/log-streams-portal-view-checkout-svc.png" alt-text="Screenshot of the checkout service container's log stream in the Azure portal.":::

1. Do the same for the `order-processor` service to verify that it's receiving messages from the subscribed Azure Service Bus topic.

   :::image type="content" source="media/microservices-dapr-azd/log-streams-portal-view-order-processor-svc.png" alt-text="Screenshot of the order processor service container's log stream in the Azure portal.":::


## What happened?

Upon successful completion of the `azd up` command:

- The Azure resources referenced in the [sample project's `./infra` directory](https://github.com/Azure-Samples/pubsub-dapr-csharp-servicebus/tree/main/infra) have been provisioned to the Azure subscription you specified. You can now view those Azure resources via the Azure portal.
- The app has been built and deployed to Azure Container Apps. Using the web app URL output from the `azd up` command, you can browse to the fully functional app.

::: zone-end

## Clean up resources

If you're not going to continue to use this application, delete the Azure resources you've provisioned with the following command:

```azdeveloper
azd down
```

## Next steps

- Learn more about [deploying Dapr applications to Azure Container Apps](./microservices-dapr.md).
- Learn more about [Azure Developer CLI](/developer/azure-developer-cli/overview.md) and [making your applications compatible with `azd`](/developer/azure-developer-cli/make-azd-compatible.md).