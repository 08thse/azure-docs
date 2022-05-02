---
title: Create SAS tokens for containers and blobs with the Azure portal
description: Learn how to generate shared access signature (SAS) tokens for containers and blobs in the Azure portal.
ms.topic: how-to
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.date: 05/02/2022
ms.author: lajanuar
recommendations: false
---

# Generate SAS tokens for storage containers

Azure Blob Storage offers three types of resources:

* **Storage** accounts provide a unique namespace in Azure for your data.
* **Containers** are located in storage accounts and organize sets of blobs.
* **Blobs** are located in containers and store text and binary data such as files, text, and images.

## When to use a shared access signature

* **Training custom models**. Your assembled set of training documents *must* be uploaded to an Azure Blob Storage container.
* **Using storage containers with public access**. You can opt to use a SAS token to grant limited access to your storage resources that have public read access.

In this article, you'll learn how to generate user delegation shared access signature (SAS) tokens for Azure Blob containers using the Azure Storage Explorer or the Azure portal. A user delegation SAS is secured with Azure AD credentials.

At a high level, here's how SAS tokens work: your application provides the SAS token to Azure Storage as part of a request. If the storage service verifies that the shared access signature is valid, the request is authorized. If the shared access signature is considered invalid, the request is declined with error code 403 (Forbidden).

> [!IMPORTANT]
>
> * If your Azure storage account is protected by a virtual network or firewall, you can't grant access by using a SAS token. You'll have to use a [managed identity](managed-identity-byos.md) to grant access to your storage resource.
>
> * [Managed identity](managed-identity-byos.md) supports both privately and publicly accessible Azure Blob Storage accounts.
>
> * Shared access signature grant permissions to storage resources, and should be protected in the same manner as an account key. Operations that use shared access signatures should be performed only over an HTTPS connection, and shared access signature URIs should only be distributed on a secure connection such as HTTPS.

## Prerequisites

To get started, you'll need:

* An active [Azure account](https://azure.microsoft.com/free/cognitive-services/). If you don't have one, you can [create a free account](https://azure.microsoft.com/free/).
* A [Form Recognizer](https://portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) or [Cognitive Services multi-service](https://portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) resource.
* A **standard performance** [Azure Blob Storage account](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM). You'll create containers to store and organize your blob data within your storage account. If you don't know how to create an Azure storage account with a container, following these quickstarts:

  * [Create a storage account](../../storage/common/storage-account-create.md). When you create your storage account, select **Standard** performance in the **Instance details** > **Performance** field.
  * [Create a container](../../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container). When you create your container, set **Public access level** to **Container** (anonymous read access for containers and blobs) in the **New Container** window.

## Upload your documents

1. Go to the [Azure portal](https://portal.azure.com/#home). Select **Your storage account** > **Data storage** > **Containers**.

   :::image type="content" source="media/sas-tokens/data-storage-menu.png" alt-text="Screenshot that shows the Data storage menu in the Azure portal.":::

1. Select a container from the list.
1. Select **Upload** from the menu at the top of the page.

    :::image type="content" source="media/sas-tokens/container-upload-button.png" alt-text="Screenshot that shows the container Upload button in the Azure portal.":::

   The **Upload blob** window appears.
1. Select your files to upload.

    :::image type="content" source="media/sas-tokens/upload-blob-window.png" alt-text="Screenshot that shows the Upload blob window in the Azure portal.":::

> [!NOTE]
> By default, the REST API uses form documents located at the root of your container. You can also use data organized in subfolders if specified in the API call. For more information, see [Organize your data in subfolders](./build-training-data-set.md#organize-your-data-in-subfolders-optional).

## Create SAS tokens

### [Azure Storage Explorer](#tab/explorer)

Azure Storage Explorer is a free standalone app that enables you to easily manage your Azure cloud storage resources from your desktop.

* You'll need the [**Azure Storage Explorer**](../../vs-azure-tools-storage-manage-with-storage-explorer.md) app installed in your Windows, macOS, or Linux development environment.

* After the Azure Storage Explorer app is installed, [connect it the storage account](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#connect-to-a-storage-account-or-service) you're using for Form Recognizer and follow the steps below:

1. Open the Azure Storage Explorer app on your local machine and navigate to your connected **Storage Accounts**.
1. Expand the Storage Accounts node and select **Blob Containers**.
1. Expand the Blob Containers node and right-click a storage **container** node to display the options menu.
1. Select **Get Shared Access Signature...** from options menu.
1. In the **Shared Access Signature** window, make the following selections:
    * Select your **Access policy** (the default is none).
    * Specify the signed key **Start** and **Expiry** date and time. A short lifespan is recommended because, once generated, a SAS can't be revoked.
    * Select the **Time zone** for the Start and Expiry date and time (default is Local).
    * Define your container **Permissions** by checking and/or clearing the appropriate check box.
    * Review and select **Create**.

1. A new window will appear with the **Container** name, **URI**, and **Query string** for your container.  
1. **Copy and paste the container, URI, and query string values in a secure location. They'll only be displayed once and can't be retrieved once the window is closed.**
1. To construct a SAS URL, append the SAS token (URI) to the URL for a storage service.

### [Azure portal](#tab/portal)

The Azure portal is a web-based, unified console that enables you to manage your Azure subscription an resources using a graphical user interface (GUI).

Go to the [Azure portal](https://portal.azure.com/#home) and navigate as follows:  

 **Your storage account** → **containers** → **your container** → **your blob**

1. Select **Generate SAS** from the menu near the top of the page.

1. Select **Signing method** → **User delegation key**.

1. Define **Permissions** by selecting or clearing the appropriate checkbox. Make sure the **Read**, **Write**, **Delete**, and **List** permissions are selected.

    :::image type="content" source="media/sas-tokens/sas-permissions.png" alt-text="Screenshot that shows the SAS permission fields in the Azure portal.":::

    >[!IMPORTANT]
    >
    > * If you receive a message similar to the following one, you'll need to assign access to the blob data in your storage account:
    >
    >     :::image type="content" source="media/sas-tokens/need-permissions.png" alt-text="Screenshot that shows the lack of permissions warning.":::
    >
     > * [Azure role-based access control](../../role-based-access-control/overview.md) (Azure RBAC) is the authorization system used to manage access to Azure resources. Azure RBAC helps you manage access and permissions for your Azure resources.
    > * [Assign an Azure role for access to blob data](../../role-based-access-control/role-assignments-portal.md?tabs=current) shows you how to assign a role that allows for read, write, and delete permissions for your Azure storage container. For example, see [Storage Blob Data Contributor](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor).

1. Specify the signed key **Start** and **Expiry** times. When you create a shared access signature (SAS), the default duration is 48 hours. After 48 hours, you'll need to create a new token. Consider setting a longer duration period for the time you'll be using your storage account Translator Service.The value for the expiry time is a maximum of seven days from the start of the shared access signature.

1. The **Allowed IP addresses** field is optional and specifies an IP address or a range of IP addresses from which to accept requests. If the request IP address doesn't match the IP address or address range specified on the SAS token, it won't be authorized.

1. The **Allowed protocols** field is optional and specifies the protocol permitted for a request made with the shared access signature. The default value is HTTPS.

1. Select **Generate SAS token and URL**.

1. The **Blob SAS token** query string and **Blob SAS URL** appear in the lower area of the window. To use the Blob SAS token, append it to a storage service URI.

1. Copy and paste the **Blob SAS token** and **Blob SAS URL** values in a secure location. They're displayed only once and can't be retrieved after the window is closed.

## Create a shared access signature with the Azure CLI

1. To create a user delegation SAS for a container by using the Azure CLI, make sure that you've installed version 2.0.78 or later. To check your installed version, use the `az --version` command.

1. Call the [az storage container generate-sas](/cli/azure/storage/container#az-storage-container-generate-sas) command.

1. The following parameters are required:

    * `auth-mode login`. This parameter ensures that requests made to Azure Storage are authorized with your Azure AD credentials.
    * `as-user`. This parameter indicates that the generated SAS is a user delegation SAS.

1. Supported permissions for a user delegation SAS on a container include Add (a), Create (c), Delete (d), List (l), Read (r), and Write (w). Make sure **r**, **w**, **d**, and **l** are included as part of the permissions parameters.

1. When you create a user delegation SAS with the Azure CLI, the maximum interval during which the user delegation key is valid is seven days from the start date. Specify an expiry time for the shared access signature that's within seven days of the start time. For more information, see [Create a user delegation SAS for a container or blob with the Azure CLI](../../storage/blobs/storage-blob-user-delegation-sas-create-cli.md#use-azure-ad-credentials-to-secure-a-sas).

### Example

Generate a user delegation SAS. Replace the placeholder values in the brackets with your own values:

```azurecli-interactive
az storage container generate-sas \
    --account-name <storage-account> \
    --name <container> \
    --permissions rwdl \
    --expiry <date-time> \
    --auth-mode login \
    --as-user
```

## Use your Blob SAS URL

Two options are available:

* To use your Blob SAS URL with the [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/TrainCustomModelAsync), add the SAS URL to the request body:

  ```json
  {
      "source":"<BLOB SAS URL>"
  }
  ```

* To use your Blob SAS URL with the [Form Recognizer labeling tool](https://fott-2-1.azurewebsites.net/connections/create), add the SAS URL to the **Connection Settings** > **Azure blob container** > **SAS URI** field:

  :::image type="content" source="media/sas-tokens/fott-add-sas-uri.png" alt-text="Screenshot that shows the SAS URI field.":::

That's it. You've learned how to create SAS tokens to authorize how clients access your data.

> [!TIP]
> You can also create user delegation SAS tokens using [PowerShell](/azure/storage/blobs/storage-blob-user-delegation-sas-create-powershell), [Azure CLI](/azure/storage/blobs/storage-blob-user-delegation-sas-create-cli) or [.NET](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-blob-user-delegation-sas-create-dotnet).

## Next step

> [!div class="nextstepaction"]
> [Build a training data set](build-training-data-set.md)

## See also

* Create user delegation SAS tokens:
  * [PowerShell](/azure/storage/blobs/storage-blob-user-delegation-sas-create-powershell)
  * [Azure CLI](/azure/storage/blobs/storage-blob-user-delegation-sas-create-cli)
  * [.NET](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-blob-user-delegation-sas-create-dotnet).