---
title: Using Azure resources in custom classification 
titleSuffix: Azure Cognitive Services
description: Learn about the steps for using Azure resources with custom classification.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 07/15/2021
ms.author: aahi
---

# Using Azure resources for custom text classification

Before starting with custom text classificaton projects, you will create an Azure Language resource and connect it to a your Azure storage account where your data is uploaded. Use this article to learn how to set up Azure resources to work with custom classification.

## Creating new Azure resources

Before you start using custom classification, you will need a Language Services resource. We recommend the steps in the [quickstart](../quickstart.md) for creating one in the Azure portal. Creating a resource in the Azure portal lets you create an Azure blob storage account at the same time, with all of the required permissions pre-configured. 

# [Azure portal](#tab/portal)

[!INCLUDE [create a new resource from the Azure portal](../includes/resource-creation-azure-portal.md)]

<!-- :::image type="content" source="../../media/azure-portal-resource-credentials.png" alt-text="A screenshot showing the resource creation screen in Language Studio." lightbox="../../media/azure-portal-resource-credentials.png"::: -->

# [Language Studio](#tab/studio)

### Create a new resource from Language Studio

If it's your first time logging in, you'll see a window appear in [Language Studio](https://language.azure.com/) that will let you choose a language resource or create a new one. You can also create a resource by clicking the settings icon in the top right corner, selecting **Resources**, then clicking **Create a new resource**.

> [!IMPORTANT]
> * To use Custom Text Classification, you'll need a Language Services resource in **West US 2** or **West Europe** with the Standard (S) pricing tier.
> * Be sure to to select **Managed Identity** when you create a resource. 

:::image type="content" source="../../media/create-new-resource-small.png" alt-text="A screenshot showing the resource creation screen in Language Studio." lightbox="../../media/create-new-resource.png":::

To use custom classification, you'll need to [create an Azure storage account](/azure/storage/common/storage-account-create) if you dont have one already. 

Next step is to assign the [correct roles](#Roles-for-your-storage-account) for the storage account to connect it to your Language resource. 

# [Azure CLI](#tab/cli)

### Create a new resource with the Azure CLI

You can create a new resource and a storage account using the following CLI [template](https://github.com/Azure-Samples/cognitive-services-sample-data-files) and [parameters](https://github.com/Azure-Samples/cognitive-services-sample-data-files) files, which are hosted on GitHub.

Edit the following values in the parameters file:

| Parameter name | Value description |
|--|--|
|`name`| Name of your TA resource|
|`location`| Region in which your resource is hosted. Custom text is only available in **West US 2** and **West Europe**.|
|`sku`| Pricing tier of your resource. Custom text only works with **S** tier|
|`storageResourceName`| Name of your storage account|
|`storageLocation`| Region in which your storage account is hosted.|
|`storageSkuType`| SKU of your [storage account](/rest/api/storagerp/srp_sku_types).|
|`storageResourceGroupName`| Resource group of your storage account|
<!-- |builtInRoleType| Set this role to **"Contributor"**| -->

Use the following PowerShell command to deploy the Azure Resource Manager (ARM) template with the files you edited.

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile <path-to-arm-template> `
  -TemplateParameterFile <path-to-parameters-file>
```

See the ARM template documentation for information on [deploying templates](/azure/azure-resource-manager/templates/deploy-powershell#parameter-files) and [parameter files](/azure/azure-resource-manager/templates/parameter-files?tabs=json).

--- 

## (optional) Using a preexisting Azure resource

You can use an existing Text Analytics resource to get started with custom features as long as this resource meets the below requirements:

|Requirement  |Description  |
|---------|---------|
|Regions     | Make sure your existing resource is provisioned in one of the two supported regions, **West US 2** or **West Europe**. If not, you will need to create a new resource in these regions.        |
|Pricing tier     | Make sure your existing resource is in the Standard (**S**) pricing tier. This is the only supported pricing tier to use the custom text feature. If not, you will need to create a new resource.        |
|Managed identity     | Make sure that the resource managed identity setting is enabled. If it isn't read the next section. |

To use custom classification, you'll need to [create an Azure storage account](/azure/storage/common/storage-account-create) if you dont have one already. 

Next step is to assign the [correct roles](#Roles-for-your-storage-account) for the storage account to connect it to your Language resource. 


## Enable identity management for your resource

Your Language Services resource must have identity management, which can be enabled either using the Azure portal or from Language Studio. To enable it using [Language Studio](https://language.azure.com/):
1. Click the settings icon in the top right corner of the screen
2. Select **Resources**
3. Select **Managed Identity** for your Azure resource.

## Roles for your storage account

Your Azure blob storage account must have the below roles:

* Your resource has the **owner** or **contributor** role on the storage account.
* Your resource has the **Storage blob data owner** or **Storage blob data contributor** role on the storage account.
* Your resource has the **Reader** role on the storage account.

To set proper roles on your storage account:

1. Go to your storage account page in the [Azure portal](https://ms.portal.azure.com/).
2. Select **Access Control (IAM)** in the left navigation menu.
3. Select **Add** to **Add Role Assignments**, and choose the **Owner** or **Contributor** role. You can search for user names in the **Select** field.

[!INCLUDE [Storage connection note](../includes/storage-account-note.md)]

## Prepare training data

* As a prerequisite for creating a Custom text classifcation project, your training data needs to be uploaded to a blob container in your storage account. You can create and upload training files from Azure directly or through using the Azure Storage Explorer tool. Using Azure Storage Explorer tool allows you to upload more data in less time.

  * [Create and upload files from Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container)
  * [Create and upload files using Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs)

* You can only use `.txt`. files for custom text classification. If your data is in other format, you can use [Cognitive Services Language Utilities tool](https://github.com/microsoft/CognitiveServicesLanguageUtilities/tree/main/CLUtils) to parse your file to `.txt` format.

* You can upload an either have tagged or you cna tag your data in Langugae Studio. For tagged, data it has to follows the [tags file format](tag-data.md#data-tag-json-file-format). 

>[!TIP]
> [**Review recommended practices**](..//concepts/recommended-practices.md) for data selection.

## Next steps

* [Custom classification quickstart](../quickstart.md)
* [Recommended practices](../concepts/recommended-practices.md)
