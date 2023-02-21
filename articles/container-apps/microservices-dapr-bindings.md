---
title: "Event-driven work using Dapr bindings"
description: Deploy a sample Dapr bindings application to Azure Container Apps.
author: hhunter-ms
ms.author: hannahhunter
ms.service: container-apps
ms.topic: how-to
ms.date: 01/26/2023
zone_pivot_group_filename: container-apps/dapr-zone-pivot-groups.json
zone_pivot_groups: dapr-languages-set
---

# Event-driven work using Dapr bindings 

[Dapr](https://dapr.io/) (Distributed Application Runtime) is a runtime that helps you build resilient stateless and stateful microservices. While you can deploy and manage the Dapr OSS project yourself, deploying your Dapr applications to the Container Apps platform:

- Provides a managed and supported Dapr integration
- Seamlessly updates Dapr versions
- Exposes a simplified Dapr interaction model to increase developer productivity

In this tutorial, you create a microservice to demonstrate [Dapr's bindings API](https://docs.dapr.io/developing-applications/building-blocks/bindings/bindings-overview/) to work with external systems as inputs and outputs. You'll:
> [!div class="checklist"]
> * Use the Dapr CLI to locally run a microservice application that leverages the Dapr bindings APIs. 
> * Redeploy the same application using `azd up` to Azure Container Apps via the Azure Developer CLI. 

The sample Dapr bindings application:
1. Listens to input binding events from a system CRON component (a standard UNIX utility used to schedule commands for automatic execution at specific intervals). 
1. Outputs the contents of local data to a [PostgreSQL](https://www.postgresql.org/) component output binding.  

:::image type="content" source="media/microservices-dapr-azd/bindings-application.png" alt-text="Diagram of the Dapr binding application.":::

> [!NOTE]
> This tutorial uses [Azure Developer CLI (`azd`)](/developer/azure-developer-cli/overview.md), which is currently in preview. Preview features are available on a self-service, opt-in basis. Previews are provided "as is" and "as available," and they're excluded from the service-level agreements and limited warranty. The `azd` previews are partially covered by customer support on a best-effort basis.

## Prerequisites

- Install [Azure Developer CLI](/developer/azure-developer-cli/install-azd.md)
- [Install](https://docs.dapr.io/getting-started/install-dapr-cli/) and [init](https://docs.dapr.io/getting-started/install-dapr-selfhost/) Dapr
- [Docker Desktop](https://www.docker.com/products/docker-desktop/)
- Install [Git](https://git-scm.com/downloads)

::: zone pivot="nodejs"

## Run the Dapr application locally with Node.js

### Prepare the project

1. Clone the [sample Dapr application](https://github.com/Azure-Samples/bindings-dapr-nodejs-cron-postgres) to your local machine.

   ```bash
   git clone https://github.com/Azure-Samples/bindings-dapr-nodejs-cron-postgres.git
   ```

1. Navigate into the sample's root directory.

   ```bash
   cd bindings-dapr-nodejs-cron-postgres
   ```

### Run the Dapr application using the Dapr CLI

Start by running the PostgreSQL container and JavaScript service with [Docker Compose](https://docs.docker.com/compose/) and Dapr.

1. From the sample's root directory, change directories to `db`.

   ```bash
   cd db
   ```
1. Run the container with Docker Compose.

   ```bash
   docker compose up -d
   ```

1. Open a new terminal window and navigate into `/batch` in the sample directory.

   ```bash
   cd bindings-dapr-nodejs-cron-postgres/batch
   ```

1. Install the dependencies:

   ```bash
   npm install
   ```

1. Run the JavaScript service application with Dapr.

   ```bash
   dapr run --app-id batch-sdk --app-port 5002 --dapr-http-port 3500 --components-path ../components -- node index.js
   ```

   The `dapr run` command runs the Dapr binding application locally. Once the application is running successfully, the terminal window shows the output binding data.

   **Expected output:**
   
   A batch script runs every 10 seconds using an input CRON binding. The script processes a JSON file and outputs data to an SQL database using the PostgreSQL Dapr binding.
   
   ```
   == APP == {"sql": "insert into orders (orderid, customer, price) values (1, 'John Smith', 100.32);"}
   == APP == {"sql": "insert into orders (orderid, customer, price) values (2, 'Jane Bond', 15.4);"}
   == APP == {"sql": "insert into orders (orderid, customer, price) values (3, 'Tony James', 35.56);"}
   == APP == Finished processing batch
   == APP == {"sql": "insert into orders (orderid, customer, price) values (1, 'John Smith', 100.32);"}
   == APP == {"sql": "insert into orders (orderid, customer, price) values (2, 'Jane Bond', 15.4);"}
   == APP == {"sql": "insert into orders (orderid, customer, price) values (3, 'Tony James', 35.56);"}
   == APP == Finished processing batch
   == APP == {"sql": "insert into orders (orderid, customer, price) values (1, 'John Smith', 100.32);"}
   == APP == {"sql": "insert into orders (orderid, customer, price) values (2, 'Jane Bond', 15.4);"}
   == APP == {"sql": "insert into orders (orderid, customer, price) values (3, 'Tony James', 35.56);"}
   == APP == Finished processing batch
   ```

1. In the `./db` terminal, stop the PostgreSQL container:

   ```bash
   docker compose stop
   ```

## Deploy the Dapr application template using `azd`

Deploy the Dapr bindings application to Azure Container Apps and Azure Postgres using [`azd`](/developer/azure-developer-cli/overview.md).

### Prepare the project

1. Navigate into the [sample's](https://github.com/Azure-Samples/bindings-dapr-nodejs-cron-postgres) root directory.

   ```bash
   cd bindings-dapr-nodejs-cron-postgres
   ```

### Run using Azure Developer CLI

1. Provision the infrastructure and deploy the Dapr application to Azure Container Apps:

   ```azdeveloper
   azd up
   ```

1. When prompted in the terminal, provide the following parameters:

   | Parameter | Description |
   | --------- | ----------- |
   | `Environment Name` | Prefix for the resource group created to hold all Azure resources. |
   | `Azure Location`   | The Azure location for your resources. |
   | `Azure Subscription` | The Azure Subscription for your resources. |

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
     https://portal.azure.com/#blade/HubsExtension/DeploymentDetailsBlade/overview
   
     (✓) Done: Resource group: resource-group-name
     (✓) Done: Log Analytics workspace: log-analytics-name
     (✓) Done: Application Insights: app-insights-name
     (✓) Done: Portal dashboard: dashboard-name
     (✓) Done: Azure Database for PostgreSQL flexible server: postgres-server
     (✓) Done: Key vault: key-vault-name
     (✓) Done: Container Apps Environment: container-apps-env-name
     (✓) Done: Container App: container-app-name
   
   
   Deploying services (azd deploy)
   
     (✓) Done: Deploying service api
     - Endpoint: https://your-container-app-endpoint.region.azurecontainerapps.io/
   
   SUCCESS: Your Azure app has been deployed!
   You can view the resources created under the resource group rg-hh-azd-test in Azure Portal:
   https://portal.azure.com/#@/resource/subscriptions/your-subscription-ID/resourceGroups/your-resource-group/overview
   ```

### Confirm successful deployment 

In the Azure portal, verify the batch Postgres container is logging each insert successfully every 10 seconds. 

1. Copy the Container App name from the terminal output.

1. Navigate to the [Azure portal](https://ms.portal.azure.com) and search for the Container App resource by name.

1. In the Container App dashboard, select **Monitoring** > **Log stream**.

1. Confirm the container is logging the same output as in the terminal earlier.

   :::image type="content" source="media/microservices-dapr-azd/log-streams-portal-view.png" alt-text="Screenshot of the container app's log stream in the Azure portal.":::

## What happened?

Upon successful completion of the `azd up` command:

- Azure Developer CLI provisioned the Azure resources referenced in the [sample project's `./infra` directory](https://github.com/Azure-Samples/bindings-dapr-nodejs-cron-postgres-/tree/master/infra) to the Azure subscription you specified. You can now view those Azure resources via the Azure portal.
- The app deployed to Azure Container Apps. Using the web app URL output from the `azd up` command, you can browse to the fully functional app.

::: zone-end

::: zone pivot="python"

## Run the Dapr application locally with Python

### Prepare the project

1. Clone the [sample Dapr application](https://github.com/greenie-msft/bindings-dapr-python-cron-postgres) to your local machine.

   ```bash
   git clone https://github.com/greenie-msft/bindings-dapr-python-cron-postgres.git
   ```

1. Navigate into the sample's root directory.

   ```bash
   cd bindings-dapr-python-cron-postgres
   ```

### Run the Dapr application using the Dapr CLI

Start by running the PostgreSQL container and Python service with [Docker Compose](https://docs.docker.com/compose/) and Dapr.

1. From the sample's root directory, change directories to `db`.

   ```bash
   cd db
   ```
1. Run the container with Docker Compose.

   ```bash
   docker compose up -d
   ```

1. Open a new terminal window and navigate into `/batch` in the sample directory.

   ```bash
   cd bindings-dapr-python-cron-postgres/batch
   ```

1. Install the dependencies:

   ```bash
   pip install -r requirements.txt
   ```

1. Run the Python service application with Dapr.

   ```bash
   dapr run --app-id batch-sdk --app-port 5001 --dapr-http-port 3500 --components-path ../components -- python3 app.py
   ```

   The `dapr run` command runs the Dapr binding application locally. Once the application is running successfully, the terminal window shows the output binding data.

   **Expected output:**
   
   A batch script runs every 10 seconds using an input CRON binding. The script processes a JSON file and outputs data to an SQL database using the PostgreSQL Dapr binding.
   
   ```
   == APP == {"sql": "insert into orders (orderid, customer, price) values (1, 'John Smith', 100.32);"}
   == APP == {"sql": "insert into orders (orderid, customer, price) values (2, 'Jane Bond', 15.4);"}
   == APP == {"sql": "insert into orders (orderid, customer, price) values (3, 'Tony James', 35.56);"}
   == APP == Finished processing batch
   == APP == {"sql": "insert into orders (orderid, customer, price) values (1, 'John Smith', 100.32);"}
   == APP == {"sql": "insert into orders (orderid, customer, price) values (2, 'Jane Bond', 15.4);"}
   == APP == {"sql": "insert into orders (orderid, customer, price) values (3, 'Tony James', 35.56);"}
   == APP == Finished processing batch
   == APP == {"sql": "insert into orders (orderid, customer, price) values (1, 'John Smith', 100.32);"}
   == APP == {"sql": "insert into orders (orderid, customer, price) values (2, 'Jane Bond', 15.4);"}
   == APP == {"sql": "insert into orders (orderid, customer, price) values (3, 'Tony James', 35.56);"}
   == APP == Finished processing batch
   ```

1. In the `./db` terminal, stop the PostgreSQL container:

   ```bash
   docker compose stop
   ```

## Deploy the Dapr application template using `azd`

Deploy the Dapr bindings application to Azure Container Apps and Azure Postgres using [`azd`](/developer/azure-developer-cli/overview.md).

### Prepare the project

1. Navigate into the [sample's](https://github.com/greenie-msft/bindings-dapr-python-cron-postgres) root directory.

   ```bash
   cd bindings-dapr-python-cron-postgres
   ```

### Run using Azure Developer CLI

1. Provision the infrastructure and deploy the Dapr application to Azure Container Apps:

   ```azdeveloper
   azd up
   ```

1. When prompted in the terminal, provide the following parameters:

   | Parameter | Description |
   | --------- | ----------- |
   | `Environment Name` | Prefix for the resource group created to hold all Azure resources. |
   | `Azure Location`   | The Azure location for your resources. |
   | `Azure Subscription` | The Azure Subscription for your resources. |

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
     https://portal.azure.com/#blade/HubsExtension/DeploymentDetailsBlade/overview
   
     (✓) Done: Resource group: resource-group-name
     (✓) Done: Log Analytics workspace: log-analytics-name
     (✓) Done: Application Insights: app-insights-name
     (✓) Done: Portal dashboard: dashboard-name
     (✓) Done: Azure Database for PostgreSQL flexible server: postgres-server
     (✓) Done: Key vault: key-vault-name
     (✓) Done: Container Apps Environment: container-apps-env-name
     (✓) Done: Container App: container-app-name
   
   
   Deploying services (azd deploy)
   
     (✓) Done: Deploying service api
     - Endpoint: https://your-container-app-endpoint.region.azurecontainerapps.io/
   
   SUCCESS: Your Azure app has been deployed!
   You can view the resources created under the resource group rg-hh-azd-test in Azure Portal:
   https://portal.azure.com/#@/resource/subscriptions/your-subscription-ID/resourceGroups/your-resource-group/overview
   ```

### Confirm successful deployment 

In the Azure portal, verify the batch Postgres container is logging each insert successfully every 10 seconds. 

1. Copy the Container App name from the terminal output.

1. Navigate to the [Azure portal](https://ms.portal.azure.com) and search for the Container App resource by name.

1. In the Container App dashboard, select **Monitoring** > **Log stream**.

1. Confirm the container is logging the same output as in the terminal earlier.

   :::image type="content" source="media/microservices-dapr-azd/log-streams-portal-view.png" alt-text="Screenshot of the container app's log stream in the Azure portal.":::

## What happened?

Upon successful completion of the `azd up` command:

- Azure Developer CLI provisioned the Azure resources referenced in the [sample project's `./infra` directory](https://github.com/Azure-Samples/bindings-dapr-nodejs-cron-postgres-/tree/master/infra) to the Azure subscription you specified. You can now view those Azure resources via the Azure portal.
- The app deployed to Azure Container Apps. Using the web app URL output from the `azd up` command, you can browse to the fully functional app.
::: zone-end

::: zone pivot="csharp"

## Run the Dapr application locally with .NET

### Prepare the project

1. Clone the [sample Dapr application](https://github.com/Azure-Samples/bindings-dapr-csharp-cron-postgres) to your local machine.

   ```bash
   git clone https://github.com/Azure-Samples/bindings-dapr-csharp-cron-postgres.git
   ```

1. Navigate into the sample's root directory.

   ```bash
   cd bindings-dapr-csharp-cron-postgres
   ```

### Run the Dapr application using the Dapr CLI

Start by running the PostgreSQL container and JavaScript service with [Docker Compose](https://docs.docker.com/compose/) and Dapr.

1. From the sample's root directory, change directories to `db`.

   ```bash
   cd db
   ```
1. Run the container with Docker Compose.

   ```bash
   docker compose up -d
   ```

1. Open a new terminal window and navigate into `/batch` in the sample directory.

   ```bash
   cd bindings-dapr-csharp-cron-postgres/batch
   ```

1. Install the dependencies:

   ```bash
   dotnet restore
   dotnet build
   ```

1. Run the .NET service application with Dapr.

   ```bash
   dapr run --app-id batch-sdk --app-port 7002 --components-path ../components -- dotnet run
   ```

   The `dapr run` command runs the Dapr binding application locally. Once the application is running successfully, the terminal window shows the output binding data.

   **Expected output:**
   
   A batch script runs every 10 seconds using an input CRON binding. The script processes a JSON file and outputs data to an SQL database using the PostgreSQL Dapr binding.
   
   ```
   == APP == {"sql": "insert into orders (orderid, customer, price) values (1, 'John Smith', 100.32);"}
   == APP == {"sql": "insert into orders (orderid, customer, price) values (2, 'Jane Bond', 15.4);"}
   == APP == {"sql": "insert into orders (orderid, customer, price) values (3, 'Tony James', 35.56);"}
   == APP == Finished processing batch
   == APP == {"sql": "insert into orders (orderid, customer, price) values (1, 'John Smith', 100.32);"}
   == APP == {"sql": "insert into orders (orderid, customer, price) values (2, 'Jane Bond', 15.4);"}
   == APP == {"sql": "insert into orders (orderid, customer, price) values (3, 'Tony James', 35.56);"}
   == APP == Finished processing batch
   == APP == {"sql": "insert into orders (orderid, customer, price) values (1, 'John Smith', 100.32);"}
   == APP == {"sql": "insert into orders (orderid, customer, price) values (2, 'Jane Bond', 15.4);"}
   == APP == {"sql": "insert into orders (orderid, customer, price) values (3, 'Tony James', 35.56);"}
   == APP == Finished processing batch
   ```

1. In the `./db` terminal, stop the PostgreSQL container:

   ```bash
   docker compose stop
   ```

## Deploy the Dapr application template using `azd`

Deploy the Dapr bindings application to Azure Container Apps and Azure Postgres using [`azd`](/developer/azure-developer-cli/overview.md).

### Prepare the project

1. Navigate into the [sample's](https://github.com/Azure-Samples/bindings-dapr-csharp-cron-postgres) root directory.

   ```bash
   cd bindings-dapr-csharp-cron-postgres
   ```

### Run using Azure Developer CLI

1. Provision the infrastructure and deploy the Dapr application to Azure Container Apps:

   ```azdeveloper
   azd up
   ```

1. When prompted in the terminal, provide the following parameters:

   | Parameter | Description |
   | --------- | ----------- |
   | `Environment Name` | Prefix for the resource group created to hold all Azure resources. |
   | `Azure Location`   | The Azure location for your resources. |
   | `Azure Subscription` | The Azure Subscription for your resources. |

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
     https://portal.azure.com/#blade/HubsExtension/DeploymentDetailsBlade/overview
   
     (✓) Done: Resource group: resource-group-name
     (✓) Done: Log Analytics workspace: log-analytics-name
     (✓) Done: Application Insights: app-insights-name
     (✓) Done: Portal dashboard: dashboard-name
     (✓) Done: Azure Database for PostgreSQL flexible server: postgres-server
     (✓) Done: Key vault: key-vault-name
     (✓) Done: Container Apps Environment: container-apps-env-name
     (✓) Done: Container App: container-app-name
   
   
   Deploying services (azd deploy)
   
     (✓) Done: Deploying service api
     - Endpoint: https://your-container-app-endpoint.region.azurecontainerapps.io/
   
   SUCCESS: Your Azure app has been deployed!
   You can view the resources created under the resource group rg-hh-azd-test in Azure Portal:
   https://portal.azure.com/#@/resource/subscriptions/your-subscription-ID/resourceGroups/your-resource-group/overview
   ```

### Confirm successful deployment 

In the Azure portal, verify the batch Postgres container is logging each insert successfully every 10 seconds. 

1. Copy the Container App name from the terminal output.

1. Navigate to the [Azure portal](https://ms.portal.azure.com) and search for the Container App resource by name.

1. In the Container App dashboard, select **Monitoring** > **Log stream**.

1. Confirm the container is logging the same output as in the terminal earlier.

   :::image type="content" source="media/microservices-dapr-azd/log-streams-portal-view.png" alt-text="Screenshot of the container app's log stream in the Azure portal.":::

## What happened?

Upon successful completion of the `azd up` command:

- Azure Developer CLI provisioned the Azure resources referenced in the [sample project's `./infra` directory](https://github.com/Azure-Samples/bindings-dapr-nodejs-cron-postgres-/tree/master/infra) to the Azure subscription you specified. You can now view those Azure resources via the Azure portal.
- The app deployed to Azure Container Apps. Using the web app URL output from the `azd up` command, you can browse to the fully functional app.

::: zone-end



## Clean up resources

If you're not going to continue to use this application, delete the Azure resources you've provisioned with the following command:

```azdeveloper
azd down
```

## Next steps

- Learn more about [deploying Dapr applications to Azure Container Apps](./microservices-dapr.md).
- Learn more about [Azure Developer CLI](/developer/azure-developer-cli/overview.md) and [making your applications compatible with `azd`](/developer/azure-developer-cli/make-azd-compatible.md).
