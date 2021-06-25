---
title:  Use Managed Identities with bring-your-own-storage (BYOS) accounts
titleSuffix: Azure Applied AI Services
description: Understand how to use managed identities with BYOS accounts
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 06/10/2021
ms.author: lajanuar
---

# Use Managed Identities with bring-your-own-storage (BYOS) accounts

> [!IMPORTANT]
> Azure role-based access control (Azure RBAC) assignment is currently in preview and is not recommended for production workloads. Certain features might not be supported or might have constrained capabilities. You will use Azure RBAC assignments to grant permissions for managed identities.

 If you have a private Azure storage account protected by a Virtual Network (VNet) or firewall or you have enabled a bring your own storage (BYOS) account, Form Recognizer, cannot directly access your storage account data. Private Azure storage account access and authentication is supported by [Managed Identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview). Once managed identities is enabled, Form Recognizer service can access your storage account using an assigned managed identity credential. In this article, you'll learn to enable a system-assigned managed identity and create a user-assigned managed identity for your Form Recognizer instance.

> [!NOTE]
>
> * The [**Train Custom Model**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/TrainCustomModelAsync) API retrieves your training data from an Azure Storage blob container. If you have a private storage account, a Managed Identity credential **is required** for training operations.
>
 > * The **Analyze** [**Receipt**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeReceiptAsync), [**Business Card**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeBusinessCardAsync), [**Invoice**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9843c2794cbb1a96291), [**Identity Document**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5f74a7738978e467c5fb8707), and [**Custom Form**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm) APIs can extract data from a single document by posting requests as raw binary content. In those scenarios, there is no requirement for a Managed Identity credential. If you choose to store your documents in a private storage account and provide the URL in your POST request, a Managed Identity credential **is required**.
>
> * For all operations using documents from storage accounts available via the public Internet, you will provide a shared access signature (**SAS**) URL for your Azure blog storage container in your POST request. To retrieve your SAS URL, go to your storage resource in the Azure portal and select the **Storage Explorer** tab. Navigate to your container, right-click, and select **Get shared access signature**. It's important to get the SAS for your container, not for the storage account itself. Make sure the **Read**, **Write**, **Delete** and **List** permissions are checked, and click **Create**. Then copy the value in the **URL** section to a temporary location. It should have the form: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.

## Prerequisites

To get started, you'll need:

* An active [**Azure account**](https://azure.microsoft.com/free/cognitive-services/).  If you don't have one, you can [**create a free account**](https://azure.microsoft.com/free/).

* A [**Form Recognizer**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) or [**Cognitive Services**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) resource in the Azure portal. For detailed steps, _see_ [Create a Cognitive Services resource using the Azure portal](/azure/cognitive-services/cognitive-services-apis-create-account?tabs=multiservice%2Cwindows).

* An [**Azure blob storage account**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). You will create containers to store and organize your blob data within your storage account. If the account has a firewall, you must have the [exception for trusted Microsoft services](/azure/storage/common/storage-network-security?tabs=azure-portal#manage-exceptions) checkbox enabled.

    :::image type="content" source="media/managed-identities/allow-trusted-services-checkbox-portal-view.png" alt-text="Screenshot: allow trusted services checkbox, portal view":::

* A brief understanding of [**Azure role-based access control (Azure RBAC)**](/azure/role-based-access-control/role-assignments-portal) using the Azure portal.

## Managed Identity assignments

There are two types of managed identities:

* A [**system-assigned**](#enable-a-system-assigned-managed-identity) managed identity is **enabled** directly on a service instance. It is not enabled by default; you have to go to your resource and update the identity setting. The system-assigned managed identity is tied to your resource throughout its lifecycle. If you delete your resource, the managed identity will be deleted as well.

* A [**user-assigned**](#create-a-user-assigned-managed-identity) managed identity is **created** as a standalone Azure resource and assigned to one or more Azure service instances. A user-assigned identity is managed separately from the resources that use it and has an independent lifecycle.

In the following steps, we will enable managed identities and grant Form Recognizer limited access to your Azure blob storage account.

## Enable a system-assigned managed identity

>[!IMPORTANT]
>
> To enable a system-assigned managed identity, you need **Microsoft.Authorization/roleAssignments/write** permissions, such as [**Owner**](/azure/role-based-access-control/built-in-roles#owner) or [**User Access Administrator**](/azure/role-based-access-control/built-in-roles#user-access-administrator). You can specify a scope at four levels: management group, subscription, resource group, or resource.

1. Sign in to the [Azure portal](https://portal.azure.com) using an account associated with your Azure subscription.

1. Navigate to your **Form Recognizer** resource page in the Azure portal.

1. In the left rail, Select **Identity** from the **Resource Management** list:

    :::image type="content" source="media/managed-identities/resource-management-identity-tab.png" alt-text="Screenshot: resource management identity tab in the Azure portal.":::

1. In the main window, toggle the **System assigned Status** tab to **On**.

1. Under **Permissions** select **Azure role assignments**:

    :::image type="content" source="media/managed-identities/enable-system-assigned-managed-identity-portal.png" alt-text="Screenshot: enable system-assigned managed identity in Azure portal.":::

1. An Azure role assignments page will open. Choose your subscription from the drop-down menu then select **&plus; Add role assignment**.

    :::image type="content" source="media/managed-identities/azure-role-assignments-page-portal.png" alt-text="Screenshot: Azure role assignments page in the Azure portal.":::

>[!NOTE]
>
> If you are unable to assign a role in the Azure portal because the Add > Add role assignment option is disabled or you get the permissions error, "you do not have permissions to add role assignment at this scope", check that you are currently signed in as a user with an assigned a role that has Microsoft.Authorization/roleAssignments/write permissions such as Owner or User Access Administrator at the Storage scope for the storage resource.

 5. In this step you are going to assign a **Storage Blob Data Reader** role to your Form Recognizer service. In the **Add role assignment** pop-up window complete the fields as follows and select **Save**:

    | Field | Value|
    |------|--------|
    |**Scope**| ***Storage***|
    |**Subscription**| ***The subscription associated with your storage resource***.|
    |**Resource**| ***The name of your storage resource***|
    |**Role** | ***Storage Blob Data Reader***—allowed read access to Azure Storage blob containers and data.|

     :::image type="content" source="media/managed-identities/add-role-assignment-window.png" alt-text="Screenshot: add role assignments page in the Azure portal.":::

1. After you've received the Added Role assignment, confirmation pop-up message, refresh the page to see the added role assignment. If you don't see the change right away, wait and try refreshing the page once more. When you assign roles or remove role assignments, it can take up to 30 minutes for changes to take effect.

    :::image type="content" source="media/managed-identities/add-role-assignment-confirmation.png" alt-text="Screenshot: Added role assignment confirmation pop-up message.":::

    :::image type="content" source="media/managed-identities/assigned-roles-window.png" alt-text="Screenshot: Azure role assignments window.":::

You have completed the steps to enable a service-assigned managed identity. With this identity credential, you can grant specific access rights to a single Azure service. If you need to assign a managed identity to  multiple Azure services, you'll need to create a user-assigned managed identity.

## Create a user-assigned managed identity

>[!IMPORTANT]
>
 > * To enable a user-assigned managed identity you need Microsoft.Authorization/roleAssignments/write permissions, such as [**Owner**](/azure/role-based-access-control/built-in-roles#owner), [**User Access Administrator**](/azure/role-based-access-control/built-in-roles#user-access-administrator), or [**Managed Identity Contributor**](/azure/role-based-access-control/built-in-roles#managed-identity-contributor).

### Create a user-assigned managed identity resource

1. Sign in to the [Azure portal](https://portal.azure.com) using an account associated with your Azure subscription.

1. In the search box, type **Managed Identities** and under **Services** select **Managed Identities**.

1. On the Managed Identities page, select the **&plus; Create** tab or the **Create managed identity** button.

    :::image type="content" source="media/managed-identities/create-managed-identity-in-portal.png" alt-text="Screenshot: create Managed Identities page in the Azure portal.":::

4. On the **Create User Assigned Managed Identity** page complete the fields as follows and select **Review &plus; create**:

    | Field | Value|
    |------|--------|
    |**Subscription**| ***The subscription associated with your storage resource***.|
    |**Resource group**| ***Choose an existing resource group or select Create new to create a new resource group***.
    | **Region**| ***Choose the same region where your Form Recognizer resource and Azure blob storage account are deployed.***.|
    |**Name**| ***Choose a name for your user-assigned managed identity***.|

5. Select **Review &plus; create**.

6. If you agree with the selections and entries that you have made, select **Create**.

    :::image type="content" source="media/managed-identities/create-user-managed-identity-in-portal.png" alt-text="Screenshot: create a user-assigned managed identity resource in the Azure portal":::

#### Assign roles to your user-assigned managed identity

You are going to create two role assignments—**Reader** and **Storage Blob Data Reader**. You will need both **user-assigned** identities to use your custom domain. 

Sign in to the [Azure portal](https://portal.azure.com) using an account associated with your Azure subscription. 

1. Navigate to your storage account page.

1. From the left rail, choose **Access Control (IAM)** and then select **&plus;  Add** from the main window.

    :::image type="content" source="media/managed-identities/access-control-iam-window.png" alt-text="Screenshot: the access control window in the portal.":::

1. Select **Add role assignment** from the drop-down window.

1. In the pop-up window, complete the following fields and select **Save**.

    | Field | Value |
    |------|--------|
    |**Role**| ***Reader***|
    |**Assign access to**|***User assigned managed identity***|
    |**Subscription**| ***The subscription associated with your storage resource***|
    |**Select**|***Enter your preferred user-assigned identity.***|

    :::image type="content" source="media/managed-identities/add-role-assignment-blob-data-reader.png" alt-text="Screenshot: add role assignment pop-up to add storage blob data reader role.":::

1. On the **Access Control (IAM)** page select **&plus;  Add** from the main window once more.

1. Select **Add role assignment** from the drop-down window.

1. In the pop-up window, complete the following fields and select **Save**.

    | Field | Value |
    |------|--------|
    |**Role**|***Storage Blob Data Reader*** |
    |**Assign access to**|***User assigned managed identity***|
    |**Subscription**| ***The subscription associated with your storage resource***|
    |**Select**|***Enter your preferred user-assigned identity.***|

    :::image type="content" source="media/managed-identities/add-role-assignment-reader.png" alt-text="Screenshot: add role assignment pop-up to add reader role:":::

1. Once you have created your user-assigned identity and assigned it the **Reader** and **Storage Blob Data Reader** roles, navigate to your Form Recognizer resource page in the Azure portal.

1. In the left rail, Select **Identity** from the Resource Management list:

    :::image type="content" source="media/managed-identities/resource-management-identity-tab.png" alt-text="Screenshot: resource management identity tab in Azure portal.":::

1. In the main window, choose the **User assigned** tab then select **&plus; Add**

    :::image type="content" source="media/managed-identities/select-user-assigned-managed-identity-in-portal.png" alt-text="Screenshot: select user-assigned managed identity in the Azure portal.":::

1. In the pop-up window, complete the following fields and select **Add**:

    | Field | Value |
    |------|--------|
    |**Subscription** | ***The subscription associated with your storage resource*** |
    | **User assigned managed identities** | ***If you have created more than one user-assigned managed identity, select the same identity resource that was assigned the **Reader** and **Storage Blob Data Reader** roles in the [assign a role to your user-assigned managed identity](#assign-roles-to-your-user-assigned-managed-identity).***

 That's it! You've learned how to implement system-assigned and user-assigned managed identities for your Form Recognizer and Azure storage resources. Now, you can train a custom model or analyze documents using files stored in your BYOS account.

## Learn more about  managed identities

> [!div class="nextstepaction"]
> [Managed identities for Azure resources frequently asked questions - Azure AD](/azure/active-directory/managed-identities-azure-resources/managed-identities-faq)
