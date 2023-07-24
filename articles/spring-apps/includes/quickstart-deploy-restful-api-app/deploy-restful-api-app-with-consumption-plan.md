---
author: KarlErickson
ms.author: v-shilichen
ms.service: spring-apps
ms.topic: include
ms.date: 06/9/2023
---

<!-- 
For clarity of structure, a separate markdown file is used to describe how to deploy to Azure Spring Apps with Consumption plan.

[!INCLUDE [deploy-restful-api-app-with-consumption-plan](includes/quickstart-deploy-restful-api-app/deploy-restful-api-app-with-consumption-plan.md)]

-->

## 2. Prepare the Spring project

First we need to prepare the Spring project to run locally.

#### [Azure portal](#tab/Azure-portal)

Use the following steps to clone and run the app locally.

1. Use the following command to clone the sample project from GitHub:

   ```bash
   git clone https://github.com/Azure-Samples/ASA-Samples-Restful-Application.git
   ```

1. If you want to run locally, please complete steps [Expose RESTful APIs](#expose-restful-apis) and [Update the application configuration](#update-the-application-configuration) first and then execute the following command to run the sample application by Maven.

   ```bash
   ./mvnw spring-boot:run
   ```

1. Access the ToDo RESTful APIs based on `http://localhost:8080` address.

#### [Azure Developer CLI](#tab/Azure-Developer-CLI)

Use AZD to initialize the web application from the Azure Developer CLI templates.

1. Open a terminal, create a new empty folder, and change directory to it.
2. Run the following command to initialize the project.

    ```bash
    azd init --template Azure-Samples/ASA-Samples-Restful-Application
    ```

   Command interaction description:
    - **OAuth2 login**: You need to authorize the login to Azure based on the OAuth2 protocol.
    - **Please enter a new environment name**: Provide an environment name, which will be used as a suffix for the resource group that will be created to hold all Azure resources. This name should be unique within your Azure subscription.

   The console outputs messages similar to the following:

    ```text
    Initializing a new project (azd init)

    Downloading template code to: <your-local-path>
    (✓) Done: Initialized git repository

    Enter a new environment name: <your-env-name>

    SUCCESS: New project initialized!
    You can view the template code in your directory: <your-local-path>
    Learn more about running 3rd party code on our DevHub: https://aka.ms/azd-third-party-code-notice
    ```

---

## 3. Prepare the cloud environment

The main resources required to run this sample are an Azure Spring Apps instance, an Azure Database for PostgreSQL instance. This section provides the steps to create these resources.

#### [Azure portal](#tab/Azure-portal)

### 3.1. Sign in to the Azure portal

Open your web browser and go to the [Azure portal](https://portal.azure.com/). Enter your credentials to sign in to the portal. The default view is your service dashboard.

### 3.2. Create an Azure Spring Apps instance

Use the following steps to create a service instance:

1. Select **Create a resource** in the corner of the portal.

1. Select **Compute** > **Azure Spring Apps**.

   :::image type="content" source="../../media/quickstart-deploy-restful-api-app/create-service-instance.png" alt-text="Screenshot of the Azure portal showing the Create a resource page with Azure Spring Apps highlighted." lightbox="../../media/quickstart-deploy-restful-api-app/create-service-instance.png":::

1. Fill out the **Basics** form with the following information:

   Use the following table as a guide for completing the form, the recommended **Plan** is `Standard consumption & dedicated (preview)`.

   | Setting                    | Suggested Value                              | Description                                                                                                                                                                                                                                                                                        |
   |----------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
   | Subscription               | Your subscription name                       | The Azure subscription that you want to use for your server. If you have multiple subscriptions, choose the subscription in which you'd like to be billed for the resource.                                                                                                                        |
   | Resource group             | *myresourcegroup*                            | A new resource group name or an existing one from your subscription.                                                                                                                                                                                                                               |
   | Name                       | *myasa*                                      | A unique name that identifies your Azure Spring Apps service. The name must be between 4 and 32 characters long and can contain only lowercase letters, numbers, and hyphens. The first character of the service name must be a letter and the last character must be either a letter or a number. |
   | Plan                       | *Standard consumption & dedicated (preview)* | Pricing Tier determines the resource and cost associated with your instance.                                                                                                                                                                                                                       |
   | Region                     | The region closest to your users             | The location that is closest to your users.                                                                                                                                                                                                                                                        |
   | Container Apps Environment | *myenvironment*                              | Select which Container Apps environment instance to share the same virtual network with other services and resources.                                                                                                                                                                              |

   :::image type="content" source="../../media/quickstart-deploy-restful-api-app/create-consumption-service-basics.png" alt-text="Screenshot of the Azure portal showing the Create Azure Spring Apps consumption plan page." lightbox="../../media/quickstart-deploy-restful-api-app/create-consumption-service-basics.png":::

   Use the following table as a guide for the Container Apps Environment creation:

   | Setting              | Suggested Value   | Description                                                                               |
   |----------------------|-------------------|-------------------------------------------------------------------------------------------|
   | Environment name     | *myenvironment*   | A unique name that identifies your Azure Container Apps Environment service.              |
   | Plan                 | *Consumption*     | Pricing Tier determines the resource and cost associated with your instance.              |
   | Zone Redundant       | *Disabled*        | Whether to create your Container Apps Environment service in an Azure availability zone.  |

   :::image type="content" source="../../media/quickstart-deploy-restful-api-app/create-apps-container-env-basics.png" alt-text="Screenshot of the Azure portal showing the Create Azure Container Apps page." lightbox="../../media/quickstart-deploy-restful-api-app/create-apps-container-env-basics.png":::

1. Select **Review and Create** to review your selections. Select **Create** to provision the Azure Spring Apps instance.

1. On the toolbar, select the **Notifications** icon (a bell) to monitor the deployment process. Once the deployment is done, you can select **Pin to dashboard**, which creates a tile for this service on your Azure portal dashboard as a shortcut to the service's **Overview** page. Select **Go to resource** to open the service's **Overview** page.

   :::image type="content" source="../../media/quickstart-deploy-restful-api-app/notifications.png" alt-text="Screenshot of the Azure portal showing the Overview page with the Notifications pane open." lightbox="../../media/quickstart-deploy-restful-api-app/notifications.png":::

> [!IMPORTANT]
> The Consumption workload profile has a pay-as-you-go billing model, with no starting cost. You're billed for the dedicated workload profile based on the provisioned resources. For more information, see [Workload profiles in Consumption + Dedicated plan structure environments in Azure Container Apps (preview)](../../../container-apps/workload-profiles-overview.md) and [Azure Spring Apps pricing](https://azure.microsoft.com/pricing/details/spring-apps/).

### 3.3. Prepare the PostgreSQL instance

[!INCLUDE [provision-postgresql-flexible](./provision-postgresql.md)]

### 3.4. Connect app instance to PostgreSQL instance

Use the following steps to connect your service instances:

1. Go to your Azure Spring Apps instance in the Azure portal.

1. From the navigation pane, open the **Apps** pane and select **Create App**.

1. On the **Create App** page, fill in the app name *simple-todo-api* and select *Java 17* as the runtime platform.

   :::image type="content" source="../../media/quickstart-deploy-restful-api-app/create-consumption-app.png" alt-text="Screenshot of the Azure portal showing the consumption app creation." lightbox="../../media/quickstart-deploy-restful-api-app/create-consumption-app.png":::

1. Select **Create** to finish the app creation and select the app to view details.

1. Select **Configuration** from the navigation pane, select **Environment variables** to set environment variables.

1. Add the following environment variables for the PostgreSQL connection, then select **Save** to finish the app configuration update. Be sure to replace the placeholders with your own values you created in the previous step.

   ```shell
   SPRING_DATASOURCE_URL=jdbc:postgresql://<your-PostgreSQL-server-name>:5432/<your-PostgreSQL-database-name>
   SPRING_DATASOURCE_USERNAME=<your-PostgreSQL-admin-user>
   SPRING_DATASOURCE_PASSWORD=<your-PostgreSQL-admin-password>
   ```

   :::image type="content" source="../../media/quickstart-deploy-restful-api-app/consumption-app-env-variables.png" alt-text="Screenshot of the Azure portal showing the Basics tab of the Create connection pane for connecting to PostgreSQL." lightbox="../../media/quickstart-deploy-restful-api-app/consumption-app-env-variables.png":::

#### [Azure Developer CLI](#tab/Azure-Developer-CLI)

1. Run the following command to log in Azure with OAuth2, ignore this step if you have already logged in.
   ```bash
   azd auth login
   ```
1. Run the following command to enable Azure Spring Apps feature.
   ```bash
   azd config set alpha.springapp on
   ```
1. Run the following command to package a deployable copy of your application, provision the template's infrastructure to Azure and also deploy the application code to those newly provisioned resources.
   ```bash
   azd provision
   ```
   Command interaction description:
    - **Select an Azure Subscription to use**: Use arrows to move, type to filter, then press Enter.
    - **Select an Azure location to use**: Use arrows to move, type to filter, then press Enter.
   
   The console outputs messages similar to the ones below:

   ```text
   SUCCESS: Your application was provisioned in Azure in xx minutes xx seconds.
   You can view the resources created under the resource group rg-<your-environment-name>> in Azure Portal:
   https://portal.azure.com/#@/resource/subscriptions/<your-subscription-id>/resourceGroups/<your-resource-group>/overview
   ```
   
   > [!NOTE]
   > This may take a while to complete. You will see a progress indicator as it provisions Azure resources.

---

### Expose RESTful APIs

This section provides the steps to expose your RESTful APIs in Azure AD.

1. Sign in to the [Azure portal](https://portal.azure.com/).

1. If you have access to multiple tenants, use the **Directory + subscription** filter (:::image type="icon" source="../../media/quickstart-deploy-restful-api-app/portal-directory-subscription-filter.png" border="false":::) to select the tenant in which you want to register an application.

1. Search for and Select **Azure Active Directory**.

1. Under **Manage**, select **App registrations** > **New registration**.

1. Enter a name for your application in the **Name** field, for example `Todo`. Users of your app might see this name, and you can change it later.

1. For **Supported account types**, select **Accounts in any organizational directory (Any Azure AD directory - Multitenant) and personal Microsoft accounts**.

1. Select **Register** to create the application.

1. On the app **Overview** page, look for the **Application (client) ID** value, and then record it for later use. You need it to configure the YAML configuration file for this project.

1. Under **Manage**, select **Expose an API**, find the **Application ID URI** at the beginning of the page, select **Add**.

1. On the **Edit application ID URI** page, accept the proposed Application ID URI (`api://{client ID}`) or use a meaningful name instead of the client ID, 
   such as `api://simple-todo`, and select **Save**.

1. Under **Manage**, select **Expose an API** > **Add a scope**, and then enter the following information:

    - For **Scope name**, enter `ToDo.Read`.
    - For **Who can consent**, select **Admins only**.
    - For **Admin consent display name**, enter `Read the ToDo data`.
    - For **Admin consent description**, enter `Allows authenticated users to read the ToDo data.`.
    - For **State**, keep **Enabled**.
    - Select **Add scope**.

1. Repeat the previous step to add two other scopes: `ToDo.Write` and `ToDo.Delete`.

   :::image type="content" source="../../media/quickstart-deploy-restful-api-app/expose-an-api.png" alt-text="Image that shows the Expose an API of a RESTful API application." lightbox="../../media/quickstart-deploy-restful-api-app/expose-an-api.png":::

### Update the application configuration

This section provides the steps to update the YAML file to use your Azure AD registered application information to establish a relationship with the RESTful API application.

Update the configuration of `spring.cloud.azure.active-directory` in the configuration file. Be sure to replace the placeholders with your own values you created in the previous step.

```yaml
spring:
  cloud:
    azure:
      active-directory:
        credential:
          client-id: <your-application-ID-of-ToDo>
        app-id-uri: <your-application-ID-URI-of-ToDo>
```

> [!NOTE]
> In v1.0 tokens, the configuration requires the client ID of the API, while in v2.0 tokens, you can use the client ID or the application ID URI in the request. You can configure both to properly complete the audience validation.

Use the following command to rebuild the sample project:

```bash
./mvnw clean package
```

## 4. Deploy the app to Azure Spring Apps

Now we can deploy the app to Azure Spring Apps.

#### [Azure portal](#tab/Azure-portal)

Use the [Maven plugin for Azure Spring Apps](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Spring-Apps) to deploy.

1. Navigate to the sample project directory and execute the following command to config the app in Azure Spring Apps:

   ```bash
   ./mvnw com.microsoft.azure:azure-spring-apps-maven-plugin:1.18.0:config
   ```

   Command interaction description:
    - **OAuth2 login**: You need to authorize the login to Azure based on the OAuth2 protocol.
    - **Select subscription**: Select the subscription list number of the Azure Spring Apps instance you just created, which defaults to the first subscription in the list. If you use the default number, press Enter directly.
    - **Select Azure Spring Apps for deployment**: Select the number of the Azure Spring Apps instance you just created, If you use the default number, press Enter directly.
    - **Input the app name(simple-todo-api)**: Input a name for your application deployment on Azure Spring Apps, If you use the default name, press Enter directly.
    - **Expose public access for this app (RESTful API for SimpleTodo)?**: Enter *y*.
    - **Confirm to save all the above configurations (Y/n)**: Enter *y*. If Enter *n*, the configuration will not be saved in the pom files.

1. Use the following command to deploy the app:

   ```bash
   ./mvnw azure-spring-apps:deploy
   ```

   Command interaction description:
    - **OAuth2 login**: You need to authorize the login to Azure based on the OAuth2 protocol.

   After the command is executed, you can see the following log signs that the deployment was successful.

   ```text
   [INFO] App(simple-todo-api) is successfully updated.
   [INFO] Deployment Status: Running
   [INFO]   InstanceName:simple-todo-api--default-xxxxxxx-xxxxxxxxxx-xxxxx  Status:Running Reason:null       DiscoverStatus:NONE
   [INFO] Getting public url of app(simple-todo-api)...
   [INFO] Application url: https://simple-todo-api.xxxxxxxx-xxxxxxxx.xxxxxx.azurecontainerapps.io
   ```

   The output **Application url** is the base endpoint to access the ToDo API application.

#### [Azure Developer CLI](#tab/Azure-Developer-CLI)

Use AZD to package the app, provision the Azure resources required by the web application and then deploy to Azure Spring Apps.

1. Run the following command to package a deployable copy of your application

   ```bash
   azd package
   ```

1. Run the following command to deploy the application code to those newly provisioned resources.

   ```bash
   azd deploy
   ```

   The console outputs messages similar to the following:

   ```text
   Deploying services (azd deploy)
   
   WARNING: Feature 'springapp' is in alpha stage.
   To learn more about alpha features and their support, visit https://aka.ms/azd-feature-stages.
   
   ...
   
   Deploying service simple-todo-api (Fetching endpoints for spring app service)
   - Endpoint: https://simple-todo-api.xxxxxxxx-xxxxxxxx.xxxxxx.azurecontainerapps.io
   
   
   SUCCESS: Your application was deployed to Azure in xx minutes xx seconds.
   You can view the resources created under the resource group rg-<your-environment-name> in Azure Portal:
   https://portal.azure.com/#@/resource/subscriptions/<your-subscription-id>/resourceGroups/rg-<your-environment-name>/overview
   ```

   The output **Endpoint** is the base endpoint to access the ToDo API application.

> [!NOTE]
> You can also use `azd up` to combine the previous three commands: `azd package` (packages a deployable copy of your application), `azd provision` (provisions Azure resources), and `azd deploy` (deploys application code). See more details from [Azure-Samples/ASA-Samples-API-Application](https://github.com/Azure-Samples/ASA-Samples-API-Application).

---