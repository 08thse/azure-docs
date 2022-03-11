---
title: 'Quickstart: create a workspace in Azure Managed Grafana Preview using the Azure portal'
description: Learn how to create a Managed Grafana workspace using the Azure portal 
ms.service: managed-grafana
ms.topic: quickstart
author: maud-lv
ms.author: malev
ms.date: 03/31/2022
--- 

# Quickstart: Create a workspace in Azure Managed Grafana Preview using the Azure portal

Get started by using the Azure portal to create a new workspace in Azure Managed Grafana Preview.

## Prerequisite

An Azure account with an active subscription. [Create an account for free](https://azure.microsoft.com/free/dotnet).

## Create a Managed Grafana workspace

1. Sign in to the [Azure portal](https://portal.azure.com) with your Azure account.  

1. In the upper-left corner of the home page, select **Create a resource**. In the **Search services and Marketplace** box, enter *Grafana* and select **Enter**.

1. Select **Managed Grafana** from the search results, and then select Create.

    :::image type="content" source="media/quickstart-portal-grafana-create.png" alt-text="Screenshot of the Azure portal. Create Grafana workspace." border="true":::

1. In the Create Grafana Workspace pane, enter the following settings.

    :::image type="content" source="media/quickstart-portal-form.png" alt-text="Screenshot of the Azure portal. Create workspace form." border="true":::

    | Setting             | Sample value     | Description                                                                                                         |
    |---------------------|------------------|---------------------------------------------------------------------------------------------------------------------|
    | Subscription id     | MySubscription   | Select the Azure subscription you want to use.                                                                      |
    | Resource group name | GrafanaResources | Select or create a resource group for your Azure Managed Grafana resources.                                         |
    | Location            | East US          | Use Location to specify the geographic location in which to host your resource. Choose the location closest to you. |
    | Name                | GrafanaWorkspace | Enter a unique resource name. It will be used as the domain name in your workspace URL.                             |

1. Select **Next : Permission >** to access rights for your Grafana dashboard and data sources:
   1. Make sure **System assigned identity** is set on to **On** so that Log Analytics reader can access your subscription.
   2. Make sure that you are listed as a Grafana administrator. You can also add additional users as administrators at the point or later.

    Note: For advanced scenarios, you can uncheck these options and configure data permissions later. The user who created the workspace is automatically assigned Admin permission to the workspace.

    > [!NOTE]
    > If creating a Grafana workspace fails the first time, please try again. The failure might be due to a limitation in our backend, and we are actively working to fix.

1. Optionally select **Next : Tags** and add tags to categorize resources.

1. Select **Next : Review + create >** and on **Create**.

Wait for the creation of the Azure Managed Grafana resource.

## Sign in to your Managed Grafana workspace console

1. Once the deployment is complete, select **Go to resource** to open your resource.  

    :::image type="content" source="media/quickstart-portal-deployment-complete.png" alt-text="Screenshot of the Azure portal. Message: Your deployment is complete." border="true":::

1. In the **Overview** tab, in the Essentials section, note the endpoint URL, and open it to access the Managed Grafana workspace console. Sign-in is automatically configured via Azure Active Directory. If prompted, connect to your Azure account.

    :::image type="content" source="media/quickstart-workspace-overview.png" alt-text="Screenshot of the Azure portal. Endpoint URL display." border="true":::

    :::image type="content" source="media/quickstart-portal-grafana-workspace.png" alt-text="Screenshot of a Managed Grafana dashboard." border="true":::

You can now start interacting with the Grafana console to configure data sources, create dashboards, reporting and alerts.

## Next steps

> [!div class="nextstepaction"]
> [Configure Permissions for Azure Managed Grafana](./how-to-configure-permissions.md).
