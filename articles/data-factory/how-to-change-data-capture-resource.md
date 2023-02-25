---
title: Capture changed data with a change data capture resource
description: This tutorial provides step-by-step instructions on how to capture changed data from ADLS Gen2 to Azure SQL DB using a Change data capture resource.
author: n0elleli,KrishnakumarRukmangathan
ms.author: noelleli,krirukm
ms.reviewer: 
ms.service: data-factory
ms.subservice:
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/26/2023
---

# How to capture changed data from ADLS Gen2 to Azure SQL DB using a Change Data Capture (CDC) resource
[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In this tutorial, you will use the Azure Data Factory user interface (UI) to create a new Change Data Capture (CDC) resource that picks up changed data from an Azure Data Lake Storage (ADLS) Gen2 source to an Azure SQL Database. The configuration pattern in this tutorial can be modified and expanded upon. 

In this tutorial, you follow these steps:
* Create a change data capture resource.
* Monitor change data capture activity.

## Pre-requisites

* **Azure subscription.** If you don't have an Azure subscription, create a free Azure account before you begin.
* **Azure storage account.** You use ADLS storage as a source data store. If you don't have a storage account, see Create an Azure storage account for steps to create one.
* **Azure SQL Database.** You will use Azure SQL DB as a target data store. If you don’t have an Azure SQL DB, please create one in the Azure portal first before continuing the tutorial. 


## Create a change data capture artifact

1.	Navigate to the **Author** blade in your data factory. You will see a new top-level artifact under **Pipelines** called **Change data capture (preview)**. 
  
  :::image type="content" source="media/adf-cdc/change-data-capture-resource-2.png" alt-text="Screenshot of new top level artifact shown under Factory resources panel.":::

2.	To create a new **Change data capture**, hover over **Change data capture (preview)** until you see 3 dots appear. Click on the **Change data capture actions**. 

  :::image type="content" source="media/adf-cdc/change-data-capture-resource-3.png" alt-text="Screenshot of Change data capture (preview) Actions after hovering on the new top-level artifact.":::

3.	Select **New change data capture (preview)**. This will open a flyout to begin the guided process. 

  :::image type="content" source="media/adf-cdc/change-data-capture-resource-4.png" alt-text="Screenshot of a list of change data capture actions.":::
  
4.	You will then be prompted to name your CDC resource. By default, the name will be set to “adfcdc” and continue to increment up by 1. You can replace this default name with your own. 

  :::image type="content" source="media/adf-cdc/change-data-capture-resource-5.png" alt-text="Screenshot of the text box to update the name of the resource.":::

5.	 Use the drop-down selection list to choose your data source. For this tutorial, we will use **DelimitedText**. 

  :::image type="content" source="media/adf-cdc/change-data-capture-resource-6.png" alt-text="Screenshot of the guided process flyout with source options in a drop-down selection menu."::: 

6.	You will then be prompted to select a linked service. Create a new linked service or select an existing one. 

  :::image type="content" source="media/adf-cdc/change-data-capture-resource-34.png" alt-text="Updated screenshot of the selection box to choose or create a new linked service.":::
  
7.	Use the **Browse** button to select your source data folder. 

  :::image type="content" source="media/adf-cdc/change-data-capture-resource-35.png" alt-text="Updated screenshot of a folder icon to browse for a folder path.":::

8.	Once you’ve selected a folder path, click **Continue** to set your data target. 

  :::image type="content" source="media/adf-cdc/change-data-capture-resource-36.png" alt-text="Updated screenshot of the continue button in the guided process to proceed to select data targets.":::

> [!NOTE]
> You can choose to add multiple source folders with the **+** button. The other sources must also use the same linked service that you’ve already selected. 

9.	Then, select a **Target type** using the drop-down selection. For this tutorial, we will select **Azure SQL Database**. 

  :::image type="content" source="media/adf-cdc/change-data-capture-resource-10.png" alt-text="Screenshot of a drop-down selection menu of all data target types.":::

10.	You will then be prompted to select a linked service. Create a new linked service or select an existing one. 
 
  :::image type="content" source="media/adf-cdc/change-data-capture-resource-37.png" alt-text="Updated screenshot of the selection box to choose or create a new linked service to your data target.":::
 
11.	Create new **Target table(s)** or select an existing **Target table(s)**. Under **New entities** select **Edit new tables** to create new Target table(s) or Under **Existing entities** use the checkbox to select an existing Target table(s).The **Preview** button will allow you to view your table data.

  :::image type="content" source="media/adf-cdc/change-data-capture-resource-38.png" alt-text="Screenshot of the new entities tab to create new tables for your target.":::

  :::image type="content" source="media/adf-cdc/change-data-capture-resource-39.png" alt-text="Screenshot of the existing entities to choose tables for your target.":::

12.	Click **Continue** when you have finalized your selection(s). 

  :::image type="content" source="media/adf-cdc/change-data-capture-resource-40.png" alt-text="Updated screenshot of the continue button in the guided process to proceed to the next step.":::

> [!NOTE]
> You can choose multiple target tables from your Azure SQL DB. Use the check boxes to select all targets. 

13.	You will automatically land in a new change data capture tab, where you can configure your new resource. 

  :::image type="content" source="media/adf-cdc/change-data-capture-resource-41.png" alt-text="Updated screenshot of the change data capture studio.":::
 
14.	A new mapping will automatically be created for you. You can update the **Source** and **Target** selections for your mapping by using the drop-down selection lists. 

  :::image type="content" source="media/adf-cdc/change-data-capture-resource-42.png" alt-text="Updated screenshot of the source to target mapping in the change data capture studio.":::

15.	With default schema-less, once you’ve selected your tables, you should see that their columns are **Auto Mapped**. If you want to allow schema drift and not change any column mappings, proceed to **Step 20** directly. 

  :::image type="content" source="media/adf-cdc/change-data-capture-resource-43.png" alt-text="Screenshot of default Auto mapped.":::

16. If you would want to add schema and enable the column mappings, select the mapping(s), and click the **Add Schema** button. 

  :::image type="content" source="media/adf-cdc/change-data-capture-resource-44.png" alt-text="Screenshot of mapping selection and Add schema button to enable column mapping button.":::
  
> [!NOTE]
> You can switch back to default schema-less mapping by clicking the **Remove schema** button.
  
17.	Once you have clicked **Add Schema** button, you should see the columns mapped. Select the **Column mappings** button to view the column mappings.

   :::image type="content" source="media/adf-cdc/change-data-capture-resource-45.png" alt-text="Screenshot of the column mapped and Column mapping button to open column mapping page.":::

18.	Here you can view your column mappings. Use the drop-down lists to edit your column mappings for Mapping method, Source column, and Target column.

   :::image type="content" source="media/adf-cdc/change-data-capture-resource-46.png" alt-text="Screenshot of the column mapping page to allow users to editing column mappings.":::

  You can add additional column mappings using the **New mapping** button. Use the drop-down lists to select the **Mapping method**, **Source column**, and **Target** column. Also, if you want to track the delete operation for supported sink types, you can select the **Keys** column. You can click **Data Preview - Refresh** button to visualize how the data will look like at the target.
  
  :::image type="content" source="media/adf-cdc/change-data-capture-resource-47.png" alt-text="Screenshot of the Add new mapping icon to add new column mappings, select Keys column and Data preview refresh button for allowing users to visualize data at target.":::

19.	When your mapping is complete, click the back arrow to return to the main canvas.

  :::image type="content" source="media/adf-cdc/change-data-capture-resource-48.png" alt-text="Screenshot of back button to go back to table mapping page.":::

20.	You can add additional source to target mappings in one CDC artifact. Use the Edit button to add more data sources and targets. Then, click **New mapping** and use the drop-down lists to set a new source and target mapping.

   :::image type="content" source="media/adf-cdc/change-data-capture-resource-49.png" alt-text="Screenshot of the edit button to add new sources and new mapping button to set a new source to target mapping.":::

21.	Once your mapping complete, set your CDC latency using the **Set Latency** button. 

   :::image type="content" source="media/adf-cdc/change-data-capture-resource-50.png" alt-text="Updated screenshot of the set frequency button at the top of the canvas.":::
   
22.	Select the frequency of your change data capture and click **Apply** to make the changes. By default, it will be set to **15 minutes**. 

For example, if you select 30 minutes, every 30 minutes, your change data capture will process your source data and pick up any changed data since the last processed time. 

:::image type="content" source="media/adf-cdc/change-data-capture-resource-51.png" alt-text="Updated screenshot of the set frequency selection menu.":::

> [!NOTE] 
> The option to select Real-time is coming soon. For streaming data integration, which is coming soon, when streaming data sources are selected, no latency selection would be needed, the latency will be Real-time by default.

23.	Once everything has been finalized, click the **Publish All** to publish your changes. 

:::image type="content" source="media/adf-cdc/change-data-capture-resource-52.png" alt-text="Updated screenshot of the publish button at the top of the canvas.":::

> [!NOTE] 
> If you do not publish your changes, you will not be able to start your CDC resource. The start button will be greyed out. 

24.	Click **Start** to start running your **Change Data Capture**. 

  :::image type="content" source="media/adf-cdc/change-data-capture-resource-53.png" alt-text="Updated screenshot of the start button at the top of the canvas.":::
 

## Monitor your Change data capture

1.	To monitor your change data capture, navigate to the **Monitor** blade or click the monitoring icon from the CDC designer. 

  :::image type="content" source="media/adf-cdc/change-data-capture-resource-27.png" alt-text="Screenshot of the monitoring blade.":::
 
  :::image type="content" source="media/adf-cdc/change-data-capture-resource-54.png" alt-text="Updated screenshot of the monitoring button at the top of the change data capture canvas.":::

2.	Select **Change data capture** to view your CDC resources. 

  :::image type="content" source="media/adf-cdc/change-data-capture-resource-29.png" alt-text="Screenshot of the Change data capture monitoring section.":::
 
3.	Here you can see the **Source**, **Target**, **Status**, and **Last processed** time of your change data capture. 

  :::image type="content" source="media/adf-cdc/change-data-capture-resource-55.png" alt-text="Updated screenshot of an overview of the change data capture monitoring page.":::

4.	Click the name of your CDC to see more details. You can see how many changes (insert/update/delete) were read and written and other diagnostic information. 

  :::image type="content" source="media/adf-cdc/change-data-capture-resource-56.png" alt-text="Updated screenshot of the detailed monitoring of a selected change data capture.":::

> [!NOTE] 
> If you have multiple mappings set up in your Change data capture, each mapping will show as a different color. Click on the bar to see specific details for each mapping or use the Diagnostics at the bottom of the screen. 

  :::image type="content" source="media/adf-cdc/change-data-capture-resource-57.png" alt-text="Updated screenshot of the detailed monitoring page of a change data capture with multiple sources to target mappings.":::
  
  :::image type="content" source="media/adf-cdc/change-data-capture-resource-58.png" alt-text="Updated screenshot of a detailed breakdown of each mapping in the change data capture artifact.":::
  
  
## Next steps
- [Learn more about the change data capture resource](concepts-change-data-capture-resource.md)
