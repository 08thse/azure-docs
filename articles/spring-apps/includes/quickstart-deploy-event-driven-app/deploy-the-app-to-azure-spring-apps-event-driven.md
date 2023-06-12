---
author: karlerickson
ms.author: v-shilichen
ms.service: spring-apps
ms.custom: event-tier1-build-2022
ms.topic: include
ms.date: 05/26/2022
---

<!-- 
For clarity of structure, a separate markdown file is used to describe how to prepare event-driven project.

[!INCLUDE [deployment-event-driven](../../includes/quickstart-deploy-event-driven-app/deployment-event-driven.md)]

-->

This section provides the steps to deploy your application to Azure Spring Apps.

### [Azure portal](#tab/Azure-portal)

Use the [Maven plugin for Azure Spring Apps](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Spring-Apps) to deploy your jar file.

1. Navigate to the sample project directory and execute the following command to config the app in Azure Spring Apps:

   ```bash
   ./mvnw com.microsoft.azure:azure-spring-apps-maven-plugin:1.17.0:config
   ```

   Command interaction description:
    - **OAuth2 login**: You need to authorize the login to Azure based on the OAuth2 protocol.
    - **Select subscription**: Select the subscription list number of the Azure Spring Apps instance you created, which defaults to the first subscription in the list. If you use the default number, press Enter directly.
    - **Select Azure Spring Apps for deployment**: Select the list number of the Azure Spring Apps instance you created. If you use the default number, press Enter directly.
    - **Input the app name(simple-event-driven-app)**: Provide an app name. Use the default project artifact ID as the name, press Enter directly.
    - **Expose public access for this app (Simple Event Driven App)?**: Enter *n*.
    - **Confirm to save all the above configurations (Y/n)**: Enter `y`. If Enter `n`, the configuration doesn't be saved in the POM files.

1. Use the following command to deploy the app:

   ```bash
   ./mvnw com.microsoft.azure:azure-spring-apps-maven-plugin:1.17.0:deploy
   ```

   Command interaction description:
    - **OAuth2 login**: You need to authorize the login to Azure based on the OAuth2 protocol.

   After the command is executed, you can see the following log signs that the deployment was successful.

   ```text
   [INFO] Deployment(default) is successfully updated.
   [INFO] Deployment Status: Running
   ```

### [Azure Developer CLI](#tab/Azure-Developer-CLI)

Use AZD to package the app, provision the Azure resources required by the web application and then deploy to Azure Spring Apps.

1. Run the following command to package a deployable copy of your application:

   ```bash
   azd package
   ```

1. Run the following command to deploy the application code to those newly provisioned resources:

   ```bash
   azd deploy
   ```

   The console outputs messages similar to the following:

   ```text
   Deploying services (azd deploy)
   
   WARNING: Feature 'springapp' is in alpha stage.
   To learn more about alpha features and their support, visit https://aka.ms/azd-feature-stages.
   
   (✓) Done: Deploying service simple-event-driven-app
   - No endpoints were found
   
   
   SUCCESS: Your application was deployed to Azure in xx minutes xx seconds.
   You can view the resources created under the resource group rg-<your-environment-name>-<a-random-string> in Azure Portal:
   https://portal.azure.com/#@/resource/subscriptions/<your-subscription-id>/resourceGroups/rg-<your-environment-name>-<a-random-string>/overview
   ```

> [!NOTE]
> You can also use `azd up` to combine the previous three commands: `azd package` (packages a deployable copy of your application), `azd provision` (provisions Azure resources), and `azd deploy` (deploys application code). See more details from [Azure-Samples/ASA-Samples-Web-Application](https://github.com/Azure-Samples/ASA-Samples-Web-Application).

---