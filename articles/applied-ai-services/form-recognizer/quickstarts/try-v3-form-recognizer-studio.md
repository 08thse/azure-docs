---
title: "Quickstart: Form Recognizer Studio | Preview"
titleSuffix: Azure Applied AI Services
description: Form and document processing, data extraction, and analysis using Form Recognizer Studio (preview)
author: sanjeev3
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 09/14/2021
ms.author: sajagtap
---

# Get started: Form Recognizer Studio | Preview

>[!NOTE]
> Form Recognizer Studio is currently in public preview. some features may not be supported or have limited capabilities. 

[Form Recognizer Studio preview](https://formrecognizer.appliedai.azure.com/) is an online tool for visually exploring, understanding, and integrating features from the Form Recognizer service in your applications. Get started with exploring the pre-trained models with sample documents or your own. Ceate projects to build custom form models and reference the models in your applications using the [Python SDK preview](try-v3-python-sdk.md) and other quickstarts.

## Prerequisites

* An active [**Azure account**](https://azure.microsoft.com/free/cognitive-services/).  If you don't have one, you can [**create a free account**](https://azure.microsoft.com/free/).
* A [**Form Recognizer**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) or [**Cognitive Services multi-service**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) resource.

## Additional steps for custom projects

In addition to the Azure account and a Form Recognizer or Cognitive Services resource, you'll need:

### Azure Blob Storage container
A **standard performance** [**Azure Blob Storage account**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). You'll create containers to store and organize your blob data within your storage account. If you don't know how to create an Azure storage account with a container, following these quickstarts:

  * [**Create a storage account**](/azure/storage/common/storage-account-create). When creating your storage account, make sure to select **Standard** performance in the **Instance details → Performance** field.
  * [**Create a container**](/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container). When creating your container, set the **Public access level** field to **Container** (anonymous read access for containers and blobs) in the **New Container** window.

### Sample documents set

1. Go to the [Azure portal](https://ms.portal.azure.com/#home) and navigate as follows:  **Your storage account** → **Data storage** → **Containers**

   :::image type="content" source="media/sas-tokens/data-storage-menu.png" alt-text="Screenshot: Data storage menu in the Azure portal.":::

1. Select a **container** from the list.

1. Select **Upload** from the menu at the top of the page.

    :::image type="content" source="media/sas-tokens/container-upload-button.png" alt-text="Screenshot: container upload button in the Azure portal.":::

1. The **Upload blob** window will appear.

1. Select your file(s) to upload.

    :::image type="content" source="media/sas-tokens/upload-blob-window.png" alt-text="Screenshot: upload blob window in the Azure portal.":::

> [!NOTE]
> By default, the Studio will use form documents that are located at the root of your container. However, you can use data organized in folders if specified in the Custom form project creation steps. *See* [**Organize your data in subfolders**](/azure/applied-ai-services/form-recognizer/build-training-data-set#organize-your-data-in-subfolders-optional)

## Sign into the Form Recognizer Studio preview

After you have completed the prequisites, navigate to the [Form Recognizer Studio preview](https://formrecognizer.appliedai.azure.com).

1. Select a Form Recognizer service feature from the Studio home page.

1. Select your Azure subscription, resource group, and resource. (You can change the resources anytime in "Settings" in the top menu.)

1. Review and confirm your selections.

![Form Recognizer Studio Getting Started example](../media/quickstarts/form-recognizer-studio-get-started.gif)

## Layout

In the Layout view:

1. Select the Analyze command to run Layout analysis on the sample document or try your document by using the Add command.

1. Observe the highlighted extracted text, the table icons showing the extracted table locations, and highlighted selection marks.

1. Use the controls at the bottom of the screen to zoom in and out and rotate the document view.

1. Show and hide the text, tables, and selection marks layers to focus on each one of them at a tme.

1. In the output section's Result tab, browse the JSON output to understand the service response format. Copy and download to jumpstart integration.

![Form Recognizer Layout example](../media/quickstarts/layout-get-started.gif)

## Prebuilt models

In the Prebuilt view:

1. From the Studio home, select one of the prebuilt model. In this example, we are using the Invoice model.

1. Select the Analyze command to run analysis on the sample document or try your invoice by using the Add command.

1. In the visualization section, observe the highlighted fields and values and invoice line items. All extracted text and tables are also shown.

1. In the output section's Fields tab, note the listed fields and values, and select the line items to view in a table-like format.

1. In the output section's Result tab, browse the JSON output to understand the service response format. Copy and download to jumpstart integration.

![Form Recognizer Prebuilt example](../media/quickstarts/prebuilt-get-started.gif)

## Custom models

To create custom models, you start with confguring your project:

1. From the Studio home, select the Custom form project to open the Custom form home page.

1. Use the "Create a project" command to start the new project configuration wizard.

1. Enter project details, select the Azure subscription and resource, and the Azure Blob storage container with your data.

1. Review and submit your settings to create the project.


![Form Recognizer Cuzstom project Getting Started example](../media/quickstarts/1-custom-model-get-started.gif)

After the project creation step, in the custom model phase:

1. From the labeling view, start defining the labels and their types that you are interested in extracting.

1. Assign the labels to the corresponding content by selecting it and the applicable label from the drop-down list or by clicking the same label in the labels pane.

1. Label four more documents to get at least five documents labeled.

1. Select the Train command and enter model name and description to start training your custom model.

1. Once the model is ready, use the Test command to validate the mode with your test docuemnts and observe the results.

1. Iterate on the steps to improve your model.

![Form Recognizer Custom project basic workflow](../media/quickstarts/2-custom-model-basic-steps.gif)

In addition, view all your models using the Models tab on the left. From the list view, select model(s) to perform the following actions:

1. Test the model from the list view.

1. Use the Delete command to delete models that are not required.

1. Download model details for offline viewing.

1. Select multiple models and compose them into a new model to be used in your applications.


## Next steps

[Get started with the Form Recognizer Studio prview](https://formrecognizer.appliedai.azure.com).
