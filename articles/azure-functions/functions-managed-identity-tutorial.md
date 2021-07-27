---
title: How to configure Azure Functions with identity-based connections
description: Article that shows you how to use identity-based connections with Azure Functions instead of connection strings
ms.topic: article
ms.date: 7/26/2021

---

# Tutorial: Creating a function app with identity-based Connections

This article shows you how to configure a secretless function app using identity-based connections instead of connection strings. To learn more about identity-based connections, see [Configure an identity-based connection.](functions-reference.md#configure-an-identity-based-connection).

In this tutorial, you'll:

> [!div class="checklist"]
> * create a function app without Azure Files
> * enable managed identity on the function app
> * Deploy a function using run from package
> * Use managed identity for AzureWebJobsStorage
> * Update the extension bundle for non C# languages.

## Create a Function App without Azure Files

Azure Files currently doesn't support managed identity for SMB file shares, so you'll create a function app and configure it without Azure Files. For more information on apps without Azure Files, refer [here](./storage-considerations.md#create-an-app-without-azure-files) 

> [!Note]
> Do not create the Function App until you have edited the ARM template as specified by the steps in this section.

1. On the Azure portal menu or the **Home** page, select **Create a resource**.

1. On the **New** page, select **Compute** > **Function App**.

1. On the **Basics** page, use the following table to configure the function app settings.

    | Setting      | Suggested value  | Description |
    | ------------ | ---------------- | ----------- |
    | **Subscription** | Your subscription | Subscription under which this new function app is created. |
    | **[Resource Group](../azure-resource-manager/management/overview.md)** |  myResourceGroup | Name for the new resource group where you'll create your function app. |
    | **Function App name** | Globally unique name | Name that identifies your new function app. Valid characters are `a-z` (case insensitive), `0-9`, and `-`.  |
    |**Publish**| Code | Choose to publish code files or a Docker container. |
    | **Runtime stack** | .NET | This tutorial uses .NET. |
    |**Region**| Preferred region | Choose a [region](https://azure.microsoft.com/regions/) near you or near other services that your functions access. |

1. Select **Next: Hosting**. On the **Hosting** page, enter the following settings.

    | Setting      | Suggested value  | Description |
    | ------------ | ---------------- | ----------- |
    | **[Storage account](../storage/common/storage-account-create.md)** |  Globally unique name |  Create a storage account used by your function app. Storage account names must be between 3 and 24 characters long. They may contain numbers and lowercase letters only. You can also use an existing account, which must meet the [storage account requirements](./storage-considerations.md#storage-account-requirements). |
    |**Operating system**| Windows | This tutorial uses Windows. |
    | **[Plan](./functions-scale.md)** | Consumption (Serverless) | Hosting plan that defines how resources are allocated to your function app. |

1. Select **Next: Monitoring**. On the **Monitoring** page, enter the following settings.

    | Setting      | Suggested value  | Description |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](./functions-monitoring.md)** | Default | Create an Application Insights resource of the same app name in the nearest supported region. Expand this setting if you need to change the **New resource name** or store your data in a different **Location** in an [Azure geography](https://azure.microsoft.com/global-infrastructure/geographies/). |

1. Select **Review + create**.

1. Do not create your Function App. Select **Download a template for automation** to the right of the **Next** button.

1. Select **Deploy**.
    :::image type="content" source="./media/functions-secretless-tutorial/1-function-create-deploy.png" alt-text="Screenshot of where to find the deploy button for creating a Function.":::

1. Select **Edit template**.
    :::image type="content" source="./media/functions-secretless-tutorial/2-function-edit-template.png" alt-text="Screenshot of how to edit a template for Function creation.":::

1. In the editor, remove the WEBSITE_CONTENTAZUREFILECONNECTIONSTRING and WEBSITE_CONTENTSHARE app settings, lines 82-89 in the below image.
    :::image type="content" source="./media/functions-secretless-tutorial/3-function-remove-app-settings.png" alt-text="Screenshot of which settings to remove for Azure Files secretless.":::

1. Select **Save** to save the updated ARM template without Azure Files.

1. On the **Review + create** page, verify your configuration. You may need to update some settings such as the **Resource Group**. Then, select **Create** to provision and deploy the function app.

1. If you access your function app once created, you will get a warning about Storage not being configured properly. This is expected as you have removed Azure Files. Later in this tutorial, you will set up run-from-package which will remove the warning.

1. Congratulations! You've successfully created your function app without Azure Files.

## Enable Managed Identity

To make your function app secretless, you will need to enable managed identity. This tutorial uses the system assigned identity. To learn more about managed identities refer [here](../app-service/overview-managed-identity.md). 

1. In your function app, in the left menu, select **Identity**.
    :::image type="content" source="./media/functions-secretless-tutorial/4-add-system-assigned-id.png" alt-text="Screenshot how to add a system assigned identity.":::

1. Select **System assigned**.

1. Switch the **Status** to **On**, and select **Save**.

1. Next, you will add role assignments. This function app needs to be able to access blob content in its storage account without a key.

1. Select **Azure role assignments** to open the **Azure role assignments** page. 

1. Select **Azure role assignment (Preview)** to open the **Add role assignment** blade.
    :::image type="content" source="./media/functions-secretless-tutorial/5-role-assignment.png" alt-text="Screenshot of how to role assignments for storage":::

1. Enter the following settings.

    | Setting      | Suggested value  | Description |
    | ------------ | ---------------- | ----------- |
    | **Scope** |  Storage |  Scope is a set of resources that the role assignment applies to. Scope has levels that are inherited at lower levels. For example, if you select a subscription scope, the role assignment applies to all resource groups and resources in the subscription. |
    |**Subscription**| Your subscription | Subscription under which this new function app is created. |
    |**Resource**| Your storage account | The storage account for your function app. |
    | **Role** | Storage Blob Data Owner | A role is a collection of permissions. Grants full access to manage all resources, including the ability to assign roles in Azure RBAC. |

1. Select **Save**.

1. Congratulations! You have created a System assigned Storage Blob Data Owner role! This will allow you to use managed identity to pull packages from the blob account, so when you configure run-from-package, you won't need to use a SAS. 

## Deploy a function using Run From Package

You'll now deploy a timer triggered function to confirm things are configured correctly so far to not require secrets in AzureWebJobsStorage. 

1. Create a timer triggered function app in Visual Studio. For steps, follow the steps for creating an HTTP triggered function app in the [Visual Studio quickstart](./functions-create-your-first-function-visual-studio.md#create-a-function-app-project), but create a timer trigger instead.

1. In order to publish a package to the blob, you'll need to manually add the package.

1. Right-click the project and select **Publish**. 
    :::image type="content" source="./media/functions-secretless-tutorial/6-publish-app.png" alt-text="Screenshot of how to open the publish an app view in Visual Studio.":::

1. Select **Folder** for the target, and select **Next**.

1. On the new window, select **Finish**.

1. Select **Publish**.

1. In your directory, zip the published output **bin** folder, **host** file, and **Function** (Timer) folder.

1. In the portal view for your storage account, select **Containers** from the left blade.
    :::image type="content" source="./media/functions-secretless-tutorial/7-new-store-container.png" alt-text="Screenshot of how to create a new storage container.":::

1. Select to create a new **Container**.

1. In the **New container** blade, provide a name, and select **Create**.

1. Select the container you created.

1. Now, upload your zipped file. Select **Upload**.
    :::image type="content" source="./media/functions-secretless-tutorial/8-upload-zip.png" alt-text="Screenshot of how to go to upload a package.":::

1. In the **Upload blob** blade, select your zipped file, and select **Upload**.

1. Once uploaded, select your uploaded blob zip file.
    :::image type="content" source="./media/functions-secretless-tutorial/9-copy-url.png" alt-text="Screenshot of how to get the zipped blob url.":::

1. Copy the blob url for your published zip file. 

1. In your function app, select **Configurations** from the left blade.
    :::image type="content" source="./media/functions-secretless-tutorial/10-web-run-from-package.png" alt-text="Screenshot of how to add the run from package app setting.":::

1. Select **New application setting**.

1. Enter **WEBSITE_RUN_FROM_PACKAGE** for the Name, and paste your blob url for the value field.

1. Select **OK** and **Save**.

## Verify your function app configuration

1. Test your app setup. In your function app, select **Functions** from the left blade.
    :::image type="content" source="./media/functions-secretless-tutorial/11-timer-test.png" alt-text="Screenshot of how to open the test a timer trigger view.":::

1. Select **Timer**

1. In the left blade, go to **Code+Test**.
    :::image type="content" source="./media/functions-secretless-tutorial/12-test-run.png" alt-text="Screenshot of how to test the timer trigger.":::

1. Select **Test/Run**, and select **Run** in the blade that opens.

1. You should see a similar message to the last image. Congratulations! You've successfully run your timer trigger.

## Use Managed Identity for AzureWebJobsStorage

The next step is to switch AzureWebJobsStorage to be secretless. This should already work because the function app has the Storage Blob Data Owner role, and you will just need to update the app setting. 

1. In your function app, in the left menu, select **Configuration**.
    :::image type="content" source="./media/functions-secretless-tutorial/13-update-azurewebjobsstorage.png" alt-text="Screenshot of how to update the AzureWebJobsStorage app setting.":::

1. Select the **AzureWebJobsStorage** app setting, and configure it with the below settings.

    | Setting      | Suggested value  | Description |
    | ------------ | ---------------- | ----------- |
    | **Name** |  AzureWebJobsStorage__accountName | Update the name from **AzureWebJobsStorage** to use the identity instead of secrets. |
    |**Value**| Your account name | Update the name from the connection string to just your **AccountName** to use the identity instead of secrets. Ex. `DefaultEndpointsProtocol=https;AccountName=identityappstore;AccountKey=...` would become `identityappstore`|

1. Follow the previous steps for testing your timer trigger to make sure everything is still working correctly.

1. Congratulations! You've successfully removed all secrets in your function app by configuring it without Azure Files and using managed identity to load the application content from blob storage using WEBSITE_RUN_FROM_PACKAGE. 

## Use Managed Identity to access Key Vault

There's one more key-like setting in your Function App, and that’s your App Insights connection string. This is not technically a secret as it contains the instrumentation key. However, you can configure this setting using Key Vault and use managed identity to access it. Steps are in the following [tutorial](./functions-managed-identity-key-vault.md).

## Use Managed Identity with Triggers

Step-by-step tutorials:
    | Trigger      | Tutorial  |
    | ------------ | ---------------- |
    | **Storage Queue Trigger** |  [Storage Queue Trigger tutorial](./functions-managed-identity-storage-queue.md) |
    | **Service Bus Queue Trigger** |  [Service Bus Trigger tutorial](./functions-managed-identity-servicebus-queue.md) |

### Use Managed Identity for local development

The Service Bus Queue Trigger tutorial also details [how to use managed identities with local development](./functions-managed-identity-servicebus-queue.md#use-managed-identity-for-local-development).

### Event Hubs and Blob Trigger

You can follow similar steps for Event Hubs and Blob trigger. Event Hubs follows the same patterns as Service Bus, and you can follow the [Service Bus Trigger tutorial](./functions-managed-identity-servicebus-queue.md). For Blobs, the functions host uses queues internally to run the blob trigger, so you would need to make sure that the function app has both blob and queue role assignments, for both the account that is configured for AzureWebJobsStorage, and the account that contains the blobs you're triggering on. For more information, see [Configure an identity-based connection.](functions-reference.md#configure-an-identity-based-connection).

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## Next steps 

In this tutorial, you created a function app with identity-based connections.

Use the following links to learn more Azure Functions with identity-based connections:

- [Managed identity in Azure Functions](../app-service/overview-managed-identity.md)
- [identity-based connections in Azure Functions](./functions-reference.md#configure-an-identity-based-connection)
- [Connecting to host storage with an Identity](./functions-reference.md#connecting-to-host-storage-with-an-identity)
- [Creating a Function App without Azure Files](./storage-considerations.md#create-an-app-without-azure-files)
- [Run Azure Functions from a package file](./run-functions-from-deployment-package.md)
- [Use Key Vault references in Azure Functions](../app-service/app-service-key-vault-references.md)
- [Configuring the account used by Visual Studio for local development](/dotnet/api/azure/identity-readme.md#authenticating-via-visual-studio)
- [Functions documentation for local development](./functions-reference.md#local-development-with-identity-based-connections)