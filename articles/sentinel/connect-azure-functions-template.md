---
title: Use Azure Functions to connect your data source to Azure Sentinel | Microsoft Docs
description: Learn how to configure data connectors that use Azure Functions to get data from data sources into Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''

ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: yelevin
---
# Use Azure Functions to connect your data source to Azure Sentinel

You can use Azure Functions, in conjunction with various coding languages such as [PowerShell](../azure-functions/functions-reference-powershell.md) or Python, to create a serverless connector to REST API endpoints of your compatible data sources.

Azure Function Apps allow you to easily connect Azure Sentinel to your data source's REST API and pull in logs. This document will show you how to configure Azure Sentinel for this purpose. You may also need to configure your source system as well. You can find vendor- and product-specific information links in each data connector's page in the portal, or on the [Partner data connectors](partner-data-connectors.md) documentation page.

> [!NOTE]
> - Data will be stored in the geographic location of the workspace on which you are running Azure Sentinel.
>
> - The use of Azure Functions to ingest data into Azure Sentinel may result in additional data ingestion costs. Check the [Azure Functions pricing](https://azure.microsoft.com/pricing/details/functions/) page for details.

## Prerequisites

The following are required to use Azure Functions to connect Azure Sentinel to your product or service and pull its logs into Azure Sentinel:

- You must have read and write permissions on the Azure Sentinel workspace.

- You must have read permissions to shared keys for the workspace. [Learn more about workspace keys](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

- You must have read and write permissions on Azure Functions, to create a Function App. [Learn more about Azure Functions](../azure-functions/index.yml).

- You will also need credentials for accessing the product's API - either a username and password, a token, a key, or some other combination. You may also need other API information such as an endpoint URI. This information can be found in each product's documentation. You can find the relevant links in each data connector's page in the portal, or in your product's section on the [Partner data connectors](partner-data-connectors.md) page.

## Configure and connect your data source

> [!NOTE]
> - You can securely store workspace and API authorization keys or tokens in Azure Key Vault. Azure Key Vault provides a secure mechanism to store and retrieve key values. [Follow these instructions](../app-service/app-service-key-vault-references.md) to use Azure Key Vault with an Azure Function App.
>
> - Some data connectors depend on a parser based on a [Kusto Function](/azure/data-explorer/kusto/query/functions/user-defined-functions) to work as expected. See your product's section on the [Partner data connectors](partner-data-connectors.md) page for links to instructions to create the Kusto function and alias.

### STEP 1 - Get your source system's API credentials

Follow your source system's instructions to get its API credentials / authorization keys / tokens, as specified on the data connector page in the portal and in your product's section on the [Partner data connectors](partner-data-connectors.md) page, and copy/paste them into a text file for later.

### STEP 2 - Deploy the connector and the associated Azure Function App

#### Choose a deployment option

# [Azure Resource Manager (ARM) template](#tab/ARM)

This method provides an automated deployment of the Okta SSO connector using an ARM template.

1. In the Azure Sentinel portal, select **Data connectors**. Select your Azure Functions-based connector from the list, and then **Open connector page**.

1. Under **Configuration**, copy the Azure Sentinel **workspace ID** and **primary key** and paste them aside.

1. Select **Deploy to Azure**. (You may have to scroll down to find the button.)

1. The **Custom deployment** screen will appear.
    - Select a **subscription**, **resource group**, and **region** in which to deploy your Function App.

    - Enter your API credentials / authorization keys / tokens that you saved in [Step 1](#step-1---get-your-source-systems-api-credentials) above.

    - Enter your Azure Sentinel **Workspace ID** and **Workspace Key** (primary key) that you copied and put aside.

    - Complete any other fields in the form on the **Custom deployment** screen. See your data connector page in the portal or your product's section on the [Partner data connectors](partner-data-connectors.md) page for assistance.

    - Select **Review + create**. When the validation completes, click **Create**.

1. **Assign the necessary permissions to your Function App:**

    Azure Functions-based connectors use an environment variable to store log access timestamps. In order for the application to write to this variable, permissions must be assigned to the system assigned identity.

    1. In the Azure portal, navigate to **Function App**.

    1. In the **Function App** blade, select your Function App from the list, then select **Identity** under **Settings** in the Function App's navigation menu.

    1. In the **System assigned** tab, set the **Status** to **On**. 

    1. Select **Save**, and an **Azure role assignments** button will appear. Click it.

    1. In the **Azure role assignments** screen, select **Add role assignment**. Set **Scope** to **Subscription**, select your subscription from the **Subscription** drop-down, and set **Role** to **App Configuration Data Owner**. 

    1. Select **Save**.

# [Manual deployment with PowerShell](#tab/MPS)

1. In the Azure Sentinel portal, select **Data connectors**. Select your Azure Functions-based connector from the list, and then **Open connector page**.

1. Under **Configuration**, copy the Azure Sentinel **workspace ID** and **primary key** and paste them aside.

1. **Create a Function App**
    1. From the Azure portal, search for and select **Function App**.

    1. In the **Function App** page, select **Create**. The **Create Function App** wizard will open.

    1. In the **Basics** tab:
        - Select a **subscription** and **resource group**.
        - Give your function app a name.
        - Set **Runtime stack** to *PowerShell Core*.
        - Select the appropriate version number.
        - Select the **region** in which you want to deploy, and then select **Next : Hosting**.

    1. In the **Hosting** tab:
        - Choose the **Storage account** you want to use, or create a new one.
        - Select *Consumption (Serverless)* as your **Plan type**.

    1. Make any other configuration changes you need, then click **Review + create**.

    1. Wait for the "Validation passed" message, then click **Create**.

    1. When the deployment completes, select **Go to resource**.

1. **Import Function App Code**
    1. In the newly created Function App, select **Functions** from the navigation menu.

    1. In the **Functions** page, select **+ Add**.
    
    1. In the **Add function** panel, select the **Timer** trigger.

    1. Enter a unique name for your function and enter a *cron* expression to specify the schedule. The default interval is every 5 minutes.

    1. When the function has been created, click on **Code + Test** on the left pane.

    1. Download the Function App Code supplied by your source system's vendor and copy and paste it into the **Function App** *run.ps1* editor, replacing what's there by default. You can find the download link on the connector page or in your product's section on the [Partner data connectors](partner-data-connectors.md) page.

    1. Click **Save**.

1. **Configure the Function App**
    1. In your Function App's page, select **Configuration** under **Settings** in the navigation menu.

    1. In the **Application settings** tab, select **+ New application setting**.

    1. Add the prescribed application settings for your product individually, with their respective case-sensitive string values. See your product's section of the [Partner data connectors](partner-data-connectors.md) page for the application settings to add.

1. Complete Setup. ***WHAT'S LEFT TO DO???***

# [Manual deployment with Python](#tab/MPY)

1. In the Azure Sentinel portal, select **Data connectors**. Select your Azure Functions-based connector from the list, and then **Open connector page**.

1. Under **Configuration**, copy the Azure Sentinel **workspace ID** and **primary key** and paste them aside.

1. **Deploy a Function App**

    > [!NOTE]
    > You will need to [prepare Visual Studio Code](../azure-functions/create-first-function-vs-code-python.md) (VS Code) for Azure Function development.

    1. Download the Azure Function App file using the link supplied on the data connector page and in your product's section of the [Partner data connectors](partner-data-connectors.md) page. Extract the archive to your local development computer.

    1. Start VS Code. From the menu bar, select **File > Open Folder...**.

    1. Select the top-level folder from the files extracted from the archive.

    1. Choose the Azure icon in the VS Code Activity bar (on the left). Then, in the **Azure: Functions** area, choose the **Deploy to function app** button. 

        > [!NOTE]
        > If you aren't already signed in, choose the Azure icon in the Activity bar. Then, in the **Azure: Functions** area, choose **Sign in to Azure**.  
        > If you're already signed in, go to the next step.


    1. Provide the following information at the prompts:
        - **Select folder**: Choose a folder from your workspace, or browse to a folder that contains your function app.
        - **Select subscription**: Choose the subscription to use.
        - Select **Create new Function App in Azure**. (Don't choose the **Advanced** option.)
        - **Enter a globally unique name for the function app**: Give your function app a name that would be valid in a URL path. The name you choose will be validated to make sure that it's unique throughout Azure Functions. (e.g. ConflAuditXXXXX).
        - **Select a runtime**: Choose *Python 3.8*.

    1. Deployment will begin. A notification is displayed after your function app is created and the deployment package is applied.

    1. Return to your Function App's page for configuration.

1. **Configure the Function App**
    1. In your Function App's page, select **Configuration** under **Settings** in the navigation menu.

    1. In the **Application settings** tab, select **+ New application setting**.

    1. Add the prescribed application settings for your product individually, with their respective case-sensitive string values. See your product's section of the [Partner data connectors](partner-data-connectors.md) page for the application settings to add.

---

## Find your data

After a successful connection is established, the data appears in **Logs** under *CustomLogs*, in the tables listed in your product's section of the [Partner data connectors](partner-data-connectors.md) page.

To query data, enter one of those table names - or the relevant Kusto function alias - in the query window.

See the **Next steps** tab in the connector page for some useful sample queries.

## Validate connectivity

It may take up to 20 minutes until your logs start to appear in Log Analytics. 

## Next steps

In this document, you learned how to connect Azure Functions-based solutions to Azure Sentinel. To learn more about Azure Sentinel, see the following articles:

- Learn how to [get visibility into your data and potential threats](quickstart-get-visibility.md).
- Get started [detecting threats with Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use workbooks](tutorial-monitor-your-data.md) to monitor your data.