---
title: Quickstart - Use Language Studio
titleSuffix: Azure Cognitive Services
description: Use this article to quickly get started with Language Studio
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 07/15/2021
ms.author: aahi
---

# Quickstart: use Language Studio to being using custom text classification

In this article, we use the Language studio to demonstrate key concepts of Custom text classification. As an example we will build a custom classification model to triage support tickets.

## Prerequisites

### Create new resource from Azure portal

Go to the [Azure portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) to create a new resource from Azure. If you're asked to select additional features, select **Skip this step**. When you create your resource, ensure it has the following values to call the custom text classification API.  

|Requirement  |Required value  |
|---------|---------|
|Location | "West US 2" or "West Europe"         |
|Pricing tier     | Standard (**S**) pricing tier        |

> [!IMPORTANT]
> In the **Custom Extraction & Custom Classification (Preview)** section, make sure you choose an existing storage account, or create a new one. A storage account is required to use custom text classification. While you can specify a storage account later, it's easier to do it now. 

## Create a custom classification project

1. Login through the [Language Studio portal](https://language.azure.com). A window will appear to let you select your subscription and Language Services resource. Select the resource you created in the above step. 

2. Scroll down until you see **Custom text classification** from the available services, and select it.

3. Select **Create new project** from the top menu in your projects page. Creating a project will let you tag data, train, evaluate, improve, and deploy your models. 

4. In the **Connect storage** screen that appears, connect your storage account using the drop-down menu. If you cannot find your storage account, make sure you created a resource using the steps above. If you're using a preexisting resource, see [creating Azure resources](../concepts/using-azure-resources.md). When you are done, select **Next**. 
 
    >[!NOTE]
    > * You only need to do this step once for each new resource you use. 
    > * This process is irreversible, if you connect a storage account to your resource you cannot disconnect it later.
    > * You can only connect your resource to one storage account.
    > * If you've already connected a storage account, you will see a **Select project type** screen instead. See the next step.

5. Select your project type. For this quickstart we will create a multi label classification project. Then click **Next**.

6. Enter the project information, including a name, description and the language of the files in your project. You will not be able to change the name of your project later. 

7. Review the data you entered and select **Create Project**.

## Import the example tagged data

Typically, you would import your data and begin [tagging the intents](../how-to/tag-data.md) within it to train the classification model. For this quickstart you will use an example file that already contains tagged data. 

1. [Download the data file](https://github.com/Azure-Samples/cognitive-services-sample-data-files) for this quickstart from GitHub.

2. Select the **Import** button on your project in Language Studio.

## Train your model

To start training your model:

1. Select **Train** from the left side menu.

2. Select the model you want to train from the **Model name** dropdown. If you don’t have models already, type in the name of your model and click on **create new model**.

    :::image type="content" source="../media/train-model-1.png" alt-text="Select the model you want to train" lightbox="../media/train-model-1.png":::

3. Click on the **Train** button at the bottom of the page.

    > [!NOTE]
    > * While training, your data will be spilt into 3 parts; 80% for training, 10% for validation and 10% for testing.
    > * If the model you selected is already trained, a pop-up will appear to confirm overwriting the last model state.
    > * Training can take up to a few hours.

## Deploy your model

Generally after training a model you would review it's [evaluation details](../how-to/view-model-evaluation.md) and made adjustments if necessary. in this quickstart, you will just deploy your model, and make it available for you to try. 

>[!NOTE]
>You can only have one deployed model per project. Deploying a new model replaces any existing deployed model.

1. Select **Deploy model** from the left side menu.

2. Select the model you want to deploy, then select **Deploy model**.

## Test your model

1. Select **Test model** from the left side menu.

2. Select the model you want to test.

3. Add your text to the textbox, you can also upload a `.txt` file. 

4. Click on **Run the test**.

5. In the **Result** tab, you can see the predicted classes for your text. You can also view the JSON response under the **JSON** tab. 

    :::image type="content" source="../media/test-model-results.png" alt-text="View the test results" lightbox="../media/test-model-results.png":::

After you've tested your model, you can begin [improving the model's performance](../how-to/improve-model.md), and sending [text classification requests](../how-to/run-inference.md).

## Clean up resources

When you don't need your project anymore, you can delete it from your [projects page in Language Studio](https://language.azure.com/customText/projects/classification). Select the project you want to delete and click on **Delete**.

## Next steps

* [Send text classification requests to your model](../how-to/run-inference.md)
* [Improve your model's performance](../how-to/improve-model.md).
* [View recommended practices](../concepts/recommended-practices.md)
* [View your model's evaluation and confusion matrix](../how-to/view-model-evaluation.md).
* [Learn about the evaluation metrics](../concepts/evaluation.md)

