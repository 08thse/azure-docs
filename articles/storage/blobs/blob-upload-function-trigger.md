---
title: Upload and process a file with Azure Functions and Blob Storage
description: Learn how to upload an image to Azure Blob Storage and analyze its content using Azure Functions and Cognitive Services
author: alexwolfmsft
ms.author: alexwolf
ms.service: storage
ms.topic: tutorial
ms.date: 3/11/2022
---

# Tutorial: Upload and process a file with Azure Functions and Blob Storage

In this tutorial, you'll learn how to upload an image to Azure Blob Storage and process it using Azure Functions and Computer Vision. You will also learn how to implement Azure Function triggers and bindings as part of this process.  Together, these services will analyze an uploaded image that contains text, extract the text out of it, and then store the text in a database row for later analysis or other purposes.

Azure Blob Storage is Microsoft's massively scalable object storage solution for the cloud. Blob storage is designed for storing images and documents, streaming media files, managing backup and archive data, and much more.  You can read more about Blob Storage on the [overview page]("/storage/blobs/storage-blobs-introduction).

Azure Functions is a serverless computer solution that allows you to write and run small blocks of code as highly scalable, serverless, event driven functions. You can read more about Azure Functions on the [overview page]("/azure-functions/functions-overview).


In this tutorial, you learn how to:

> [!div class="checklist"]
> * Upload images and files to Blob Storage
> * Use an Azure Function event trigger to process data uploaded to Blob Storage
> * Use Cognitive Services to analyze an image
> * Write data to Table Storage using Azure Function output bindings


## Prerequisites

- An Azure account with an active subscription. [Create an account for free](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Visual Studio 2022](https://visualstudio.microsoft.com) or [Visual Studio Code](https://code.visualstudio.com) installed.
 

## 1) Create the Storage Account and Container
The first step is to create the Storage Account that will hold the uploaded blob data, which in this scenario will be images that contain text. A Storage Account offers several different services, but this tutorial utilizes Blob Storage and Table Storage.

### [Azure portal](#tab/azure-portal)

Sign in to the [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.StorageAccount).

In the search bar at the top of the portal, search for *Storage* and select the result labeled **Storage Accounts**.

On the **Storage accounts** page, select **+ Create** in the top left.

On the **Create a storage account** page, enter the following values:

 1) **Subscription**: Choose your desired Subscription.
 2) **Resource Group**: Select **Create new** and enter a name of `msdocs-storage-function`, and then choose **OK**.
 3) **Storage account name**: Enter a value of `msdocsstoragefunction`.
 4) **Region**: Select the region that is closest to you.
 5) **Performance**: Choose **Standard**.
 6) **Redundancy**: Leave the default value selected.
 
:::image type="content" source="./media/blob-upload-storage-function/portal-storage-create-small.png" alt-text="A screenshot showing how create a Storage Account in Azure."  lightbox="media/blob-upload-storage-function/portal-storage-create.png":::
 
Select **Review + Create** at the bottom. Azure will take a moment validate the information you entered.  Once the  settings are validated, choose **Create** and Azure will begin provisioning the Storage Account, which make take a moment.

After the Storage Account is provisioned, select **Go to Resource**. We need to create a Storage Container inside of it to hold our uploaded images for analysis. 

On the left navigation, choose **Containers**.

:::image type="content" source="./media/blob-upload-storage-function/portal-storage-containers-small.png" lightbox="./media/blob-upload-storage-function/portal-storage-containers.png" alt-text="A screenshot showing how to navigate to the Storage Account containers." :::

On the **Containers** page, select **+ Container** at the top. In the slide out panel, enter a **Name** of *ImageAnalysis*, and then leave the **Public access level** at **Private (no anonymous access)**.  Then click **Create**.

:::image type="content" source="./media/blob-upload-storage-function/portal-container-create.png" alt-text="A screenshot showing how to create a new Storage Container." :::

You should see your new container populate in the list of containers.

### [Azure CLI](#tab/azure-cli)

Azure CLI commands can be run in the [Azure Cloud Shell](https://shell.azure.com) or on a workstation with the [Azure CLI installed](/cli/azure/install-azure-cli).

To create the Storage Account and Container, we can run the CLI commands seen below.

```azurecli-interactive
az group create --location eastus --name msdocs-storage-function \

az storage account create --name msdocsstorageaccount -resource-group msdocs-storage-function -l eastus --sku Standard_LRS \

az storage container create --name imageanalysis --account-name msdocsstorageaccount --resource-group msdocs-storage-function
```

You may need to wait a few moments for Azure to provision these resources.

---

## 2) Create the Computer Vision Account
Next let's create the Computer Vision service account that will process our uploaded files.  Computer Vision is part of Azure Cognitive services and offers a variety of features for extracting data out of images.  You can [learn more about Computer Vision on the overview page](/services/cognitive-services/computer-vision/#overview).

### [Azure portal](#tab/azure-portal)

In the search bar at the top of the portal, search for *Computer* and select the result labeled **Computer vision**.

On the **Computer vision** page, select **+ Create** in the top left.

On the **Create Computer Vision** page, enter the following values:

 1) **Subscription**: Choose your desired Subscription.
 1) **Resource Group**: Use the `msdocs-storage-function` resource group you created earlier..
 1) **Region**: Select the region that is closest to you.
 1) **Name**: Enter in a name of `msdocscomputervision`.
 1) **Pricing Tier**: Choose **Free** if it is available, otherwise choose **Standard S1**.
 1) Check the **Responsible AI Notice** box if you agree to the terms

:::image type="content" lightbox="./media/blob-upload-storage-function/computer-vision-create.png" source="./media/blob-upload-storage-function/computer-vision-create-small.png" alt-text="A screenshot showing how to create a new Computer Vision service." :::
 
Select **Review + Create** at the bottom. Azure will take a moment validate the information you entered.  Once the  settings are validated, choose **Create** and Azure will begin provisioning the the Computer Vision service, which may take a moment.

When the operation has completed, click **Go to Resource**.

Next, we need to find the secret key and endpoint URL for the Computer Vision service to use in our Azure Function app. On the **Computer Vision** overview page, select **Keys and Endpoint**.

 On the **Keys and EndPoint** page, copy the **Key 1** value and the **EndPoint** values and paste them somewhere to use for later.

:::image type="content" source="./media/blob-upload-storage-function/computer-vision-endpoints.png" alt-text="A screenshot showing how to retrieves the Keys and URL Endpoint for a Computer Vision service." :::

### [Azure CLI](#tab/azure-cli)

To create the Computer Vision service, we can run the CLI command below.

```azurecli-interactive
az cognitiveservices account create \
    --name msdocs-process-image \
    --resource-group msdocs-storage-function \
    --kind ComputerVision \
    --sku F1 \
    --location eastus2 \
    --yes
```

You may need to wait a few moments for Azure to provision these resources.

Once the Computer Vision service is created, you can retrieve the secret keys and URL endpoint using the commands below.

```azurelci-interactive
    az cognitiveservices account keys list \
    --name msdocs-process-image \
    --resource-group msdocs-storage-function  \ 

    az cognitiveservices account list \
    --name msdocs-process-image \
     --resource-group msdocs-storage-function --query "[].properties.endpoint"   
```

---
 

## 3) Download and configure the sample project
The code for the Azure Function used in this tutorial can be found in [this Github repository](https://github.com/Azure-Samples/msdocs-storage-bind-function-service/tree/main/dotnet). You can also clone the project using the command below.

```terminal
git clone https://github.com/Azure-Samples/msdocs-storage-bind-function-service.git \
cd msdocs-storage-bind-function-service/dotnet
```

The sample project code accomplishes the following tasks:

- Retrieves environment variables to connect to the Storage Account and Computer Vision service
- Accepts the uploaded file as a blob parameter
- Analyzes the blob using the Computer Vision service
- Sends the analyzed image text to a new table row using output bindings

Once you have downloaded and opened the project, there are a few essential concepts to understand in the main `Run` method shown below. The Azure function utilizes Trigger and Output bindings, which are applied using attributes on the `Run` method signature. 

The `Table` attribute uses two parameters.  The first parameter specifies the name of the table to write the parsed image text value returned by the function. The second Connection parameter pulls a Table Storage connection string from the environment variables so that our Azure function has access to it. 

The `BlobTrigger` attribute is used to bind our function to the upload event in Blob Storage, and supplies that uploaded blob to the `Run` function.  The Blob Trigger has two parameters of its own - one for the name of the Blob Container to monitor for uploads, and one for the Connection String of our Storage Account again.


```csharp
// Azure Function name and output Binding to Table Storage
[FunctionName("ProcessImageUpload")]
[return: Table("ImageText", Connection = "StorageConnection")]
// Trigger binding runs when an image is uploaded to the blob container below
public async Task<ImageContent> Run([BlobTrigger("testblobs/{name}", Connection = "StorageConnection")]Stream myBlob, string name, ILogger log)
{
    // Get connection configurations
    string subscriptionKey = Environment.GetEnvironmentVariable("ComputerVisionKey");
    string endpoint = Environment.GetEnvironmentVariable("ComputerVisionEndpoint");
    string imgUrl = $"https://{ Environment.GetEnvironmentVariable("StorageAccountName")}.blob.core.windows.net/imageanalysis/{name}";

    ComputerVisionClient client = new ComputerVisionClient(new ApiKeyServiceClientCredentials(subscriptionKey)) { Endpoint = endpoint };

    // Get the analyzed image contents
    var textContext = await AnalyzeImageContent(client, imgUrl);

    return new ImageContent { PartitionKey = "Images", RowKey = Guid.NewGuid().ToString(), Text = textContext };
}

public class ImageContent
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Text { get; set; }
}
```

This code also retrieves essential configuration values from environment variables, such as the Storage Account connection string and Computer Vision key. We'll add these Environment variables to our Azure Function environment after it's deployed.

The `ProcessImage` function also utilizes a second method called `AnalyzeImage`, seen below.  This code uses the URL Endpoint and Key of our Computer Vision account to make a request to that server to process our image.  The request will return all of the text discovered in the image, which will then be written to Table Storage using the output binding on the `Run` method.

```csharp
static async Task<string> ReadFileUrl(ComputerVisionClient client, string urlFile)
{
    // Analyze the file using Computer Vision Client
    var textHeaders = await client.ReadAsync(urlFile);
    string operationLocation = textHeaders.OperationLocation;
    Thread.Sleep(2000);
    
    // Complete code omitted for brevity, view in sample project
    
    return text.ToString();
}
```

### Running locally

If you'd like to run the project locally, you can populate the environment variables using the local.settings.json file. Inside of this file, fill in the placeholder values with the values you saved earlier when creating the Azure resources.

Although the Azure Function code will run locally, it will still connect to the live services out on Azure, rather than using any local emulators.

```javascript
{
    "IsEncrypted": false,
    "Values": {
      "AzureWebJobsStorage": "UseDevelopmentStorage=true",
      "FUNCTIONS_WORKER_RUNTIME": "dotnet",
      "StorageConnection": "your-storage-account-connection-string",
      "StorageAccountName": "your-storage-account-name",
      "ComputerVisionKey": "your-computer-vision-key",
      "ComputerVisionEndPoint":  "your-computer-vision-endpoint"
    }
}
```

## 4) Deploy the code to Azure Functions

We are now ready to deploy our application to Azure using Visual Studio.  We can also create the Azure Functions app in Azure at the same time as part of the deployment process.

To begin, right click on the **ProcessImage** project node and select Publish.

On the **Publish** dialog screen, select Azure and choose **Next**.

:::image type="content" source="./media/blob-upload-storage-function/visual-studio-publish-target.png" alt-text="A screenshot showing how to select Azure as the deployment target." :::
 
Select **Azure Function App (Windows)** or **Azure Function App (Linux)** on the next screen, and then choose **Next** again.

:::image type="content" source="./media/blob-upload-storage-function/visual-studio-publish-specific-target.png" alt-text="A screenshot showing how to choose Azure Functions as a specific deployment target." :::

On the **Functions instance** step, make sure to choose the subscription you'd like to deploy to. Next, click on the green **+** symbol on the right side of the dialog.

A new dialog will open.  Enter the following values for your new Function App.

- **Name**: Enter *msdocsprocessimage* or something similar.
- **Subscription Name**: Choose whatever subscription you'd like to use.
- **Resource Group**: Choose the **** resource group you created earlier.
- **Plan Type**: Select **Consumption**.
- **Location**: Choose the region closest to you.
- **Azure Storage**: Select the Storage Account you created earlier.

:::image type="content" source="./media/blob-upload-storage-function/visual-studio-create-function-app.png" alt-text="A screenshot showing how to create a new Function App in Azure." :::

Once you have filled in all of those values, click **Create**. Visual Studio and Azure will begin provisioning the requested resources, which may take a few moments to complete.

Once the process has finished, click **Finish** to close out the dialog workflow.

The final step to deploy our Azure Function is to click **Publish** in the upper right of the screen. This may also takes a few moments to complete.  Once it finishes, your application will be running out on Azure.

## 5) Connect the Services

Our Azure Function was deployed successfully, but it cannot connect to our Storage Account and Computer Vision services yet. To enable this, we need to add the correct keys and connection strings to the configuration settings of the Azure Functions app.

At the top of the Azure Portal, search for *function* and select **Function App** from the results.

On the **Function App** screen, select the Function App you created in Visual Studio.

On the **Function App** overview page, click **Configuration** on the left navigation.  This will open up a page where we can manage various types of configuration settings for our app.  For now, we are interested in **Application Settings** section.

We need to add three settings for our Storage Account connection string, the Computer Vision secret key, and the Computer Vision endpoint.

On the **Application settings** tab, select **+ New application setting**. In the flyout that appears, enter the following values:

- **Name**: Enter a value of *ComputerVisionKey*.
- **Value**: Paste in the Computer Vision key you saved from earlier.

Click **OK** to add this setting to your app.

:::image type="content" source="./media/blob-upload-storage-function/function-app-settings.png" alt-text="A screenshot showing how to add a new application setting to an Azure Function." :::

Next, let's repeat this process for the endpoint of our Computer Vision service, using the following values:

- **Name**: Enter a value of *ComputerVisionEndpoint*.
- **Value**: Paste in the endpoint URL you saved from earlier.

Repeat this step again for the Storage Account connection, using the following values:

- **Name**: Enter a value of *StorageConnection*.
- **Value**: Paste in the access key you saved from earlier.

Finally, repeat this process one more time for the Endpoint URL of our Computer Vision service, using the following values:

- **Name**: Enter a value of *StorageAccountName*.
- **Value**: Enter in the name of the Storage Account you created.

After you have added these application settings, make sure to click **Save** at the top of the configuration page.  When the save completes, you can hit **Refresh** as well to make sure the settings are picked up.

All of the required Environment variables to connect our Azure function to different services are now in place.


## 6) Upload an image to Blob Storage

We are now ready to test out our application! Let's upload a blob to our container, and then verify that the text in the image was saved to Table Storage.

First, at the top of the Azure portal, search for *Storage* and click on **Storage Account**.  On the **Storage Account** page, click on the account you created earlier.

Next, click **Containers** on the left nav, and then navigate into the **ImageAnalysis** container we created earlier.  From here we can upload a test image right inside the browser. 

:::image type="content" source="./media/blob-upload-storage-function/storage-container-browse.png" alt-text="A screenshot showing how to navigate to a Store Container." :::

You can find a few sample images included in the **images** folder at the root of the downloadable sample project, or you can use one of your own.

At the top of the **ImageAnalysis** page, select  **Upload**.  In the flyout that opens, select the folder icon on the right to open up a file browser.  Choose the image you'd like to upload, and then click **Upload**.

:::image type="content" source="./media/blob-upload-storage-function/storage-container-upload.png" alt-text="A screenshot showing how to upload a blob to an Azure Storage Container." :::

The file should appear inside of your blob container. Next we need to verify that the upload triggered our Azure Function, and that our the text in our image was analyzed and saved to table storage properly.

Using the breadcrumbs at the top of the page, navigate up one level in your Storage Account.  Locate and select **Storage browser** on the left nav, and then click on **Tables**.

We should see the **ImageText** table we created earlier.  Click on this Table to navigate to preview the data rows inside of it.  You should see an entry for the processed image text of our upload.  You can verify this using either the Timestamp, or by viewing the content of the **Text** column.

:::image type="content" source="./media/blob-upload-storage-function/storage-table.png" alt-text="A screenshot showing a text entry in Azure Table Storage." :::

Congratulations! You succeeded in processing an image that was uploaded to Blob Storage using Azure Functions and Computer Vision.


## Clean up resources

If you're not going to continue to use this application, delete
resources with the following steps:

1. From the left-hand menu...
1. ...click Delete, type...and then click Delete


## Next steps

Advance to the next article to learn how to create...

