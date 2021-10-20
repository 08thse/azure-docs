---
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
---

## Prerequisites

* Azure subscription - [Create one for free](https://azure.microsoft.com/free/cognitive-services)

## Create a new Azure resource and Azure Blob Storage account

Before you can use custom NER, you will need to create an Azure Language resource, which will give you the credentials needed to create a project and start training a model. You will also need an Azure storage account, where you can upload your dataset that will be used to building your model.

> [!IMPORTANT]
> To get started quickly, we recommend creating a new Azure Language resource using the steps provided below, which will let you create the resource, and configure a storage account at the same time, which is easier than doing it later.
>
> If you have a pre-existing resource you'd like to use, you will need to configure it and a storage account separately. See [create project](../../how-to/create-project.md#using-a-pre-existing-azure-resource)  for information.

1. Go to the [Azure portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) to create a new Azure Text Analytics resource. If you're asked to select additional features, select **Custom text classification & custom NER**. When you create your resource, ensure it has the following parameters.

    |Azure resource requirement  |Required value  |
    |---------|---------|
    |Location | "West US 2" or "West Europe"         |
    |Pricing tier     | Standard (**S**) pricing tier        |

2. In the **Custom Named Entity Recognition (NER) & Custom Classification (Preview)** section, select an existing storage account or select **Create a new storage account**. Note that these values are for this quickstart, and not necessarily the [storage account values](/azure/storage/common/storage-account-overview) you will want to use in production environments.

    |Storage account value  |Recommended value  |
    |---------|---------|
    | Name | Any name |
    | Performance | Standard |
    | Account kind| Storage (general purpose v1) |
    | Replication | Locally-redundant storage (LRS)
    |Location | Any location closest to you, for best latency.        |

## Upload sample data to blob container

After you have created an Azure storage account and linked it to your Language Service resource, you will need to upload the example files for this quickstart. These files will later be used to train your model.

1. [Download sample data](https://github.com/Azure-Samples/cognitive-services-sample-data-files) for this quickstart from GitHub.

2. Go to your Azure storage account in the [Azure portal](https://ms.portal.azure.com). Navigate to your account, and upload the sample data to it.

The provided sample dataset contains TBD

## Create a custom named entity recognition project

[!INCLUDE [Create custom NER project](../create-project.md)]

## Train your model

Typically after you create a project, you would import your data and begin tagging the entities within it to train the NER model. For this quickstart, you will use the example tagged data file you downloaded earlier, and stored in your Azure storage account.

A model is the machine learning object that will be trained to classify text. Your model will learn from the example data, and be able to classify technical support tickets afterwards.

1. Select **Train** from the left side menu.

2. Select the model you want to train from the **Model name** dropdown. If you don’t have models already, type in the name of your model and click on **create new model**.

    :::image type="content" source="../../media/train-model.png" alt-text="Select the model you want to train" lightbox="../../media/train-model.png":::

3. Click on the **Train** button at the bottom of the page.

    > [!NOTE]
    > * While training, your data will be spilt into 3 parts; 80% for training, 10% for validation and 10% for testing.
    > * If the model you selected is already trained, a pop-up will appear to confirm overwriting the last model state.
    > * Training can take up to a few hours.

## Deploy model

Generally after training a model you would review its [evaluation details](../../how-to/view-model-evaluation.md) and make adjustments if necessary. in this quickstart, you will just deploy your model, and make it available for you to try. 

>[!NOTE]
>You can only have one deployed model per project. Deploying a new model replaces any existing deployed model.

1. Select **Deploy model** from the left side menu.

2. Select the model you want to deploy, then select **Deploy model**.

## Test your model

After your model is deployed, you can start using it for entity extraction. Use the following steps to send your first entity extraction request.

1. Select **Test model** from the left side menu.

2. Select the model you want to test.

3. Add your text to the textbox, you can also upload a `.txt` file. 

4. Click on **Run the test**.

5. In the **Result** tab, you can see the predicted classes for your text. You can also view the JSON response under the **JSON** tab. 

    :::image type="content" source="../../media/test-model-results.png" alt-text="View the test results" lightbox="../../media/test-model-results.png":::


## Clean up resources

When you don't need your project anymore, you can delete your project using [Language Studio](https://language.azure.com/customText/projects/extraction). Select **Custom Named Entity Recognition (NER)** in the left navigation menu, select project you want to delete and click on **Delete**.
