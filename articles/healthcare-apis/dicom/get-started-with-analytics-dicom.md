---
title: Get Started using DICOM Data in Analytics Workloads
description: This guide demonstrates how to use Azure Data Factory and Microsoft Fabric to perform analytics on DICOM data.
services: healthcare-apis
author: mmitrik
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 07/14/2023
ms.author: mmitrik
---

# Get Started using DICOM Data in Analytics Workloads

This article details how to get started using DICOM data in analytics workloads with Azure Data Factory and Microsoft Fabric.

## Prerequisites
Before getting started, ensure you have done the following:

* Deploy an instance of the [DICOM Service](deploy-dicom-services-in-azure.md).
* Create a [storage account with Azure Data lake Storage Gen2 (ADLS Gen2) capabilities](../../storage/blobs/create-data-lake-storage-account.md) being sure to enable the hierarchical namespace. 
    * Create a container to store DICOM metadata.  
* Create an instance of [Azure Data Factory (ADF)](../../data-factory/quickstart-create-data-factory.md).
    * Ensure that a [system assigned managed identity](../../data-factory/data-factory-service-identity.md) has been enabled.
    * Ensure that a Git repository has been configured for the data factory in order to load custom pipeline templates. This can be configured when provisioning or later in the **Manage** tab of the Azure Data Factory Studio.
* Create a [Lakehouse](https://learn.microsoft.com/fabric/data-engineering/tutorial-build-lakehouse) in Microsoft Fabric.
* Add role assignments to the ADF system assigned managed identity for the DICOM Service and the ADLS Gen2 storage account.
    * Add the **DICOM Data Reader** role to grant permission to the DICOM service.
    * Add the **Storage Blob Data Contributor** role to grant permission to the ADLS Gen2 account.
Azure Data Factory
This section details the steps for configuring an ADF pipeline that can write DICOM attributes for SOP instances, series, and studies into additional formats like Delta Tables. Open the Azure Data Factory studio to begin.

## Configure an Azure Data Factory pipeline for the DICOM service

In this example, an Azure Data Factory [pipeline](../../data-factory/concepts-pipelines-activities.md) will be used to write DICOM attributes for instances, series, and studies into a storage account in a [Delta table](https://delta.io/) format.  

![Screenshot of Azure Data Factory Studio Launch studio button]()
:::image type="content" source="media/data-factory-launch-studio.png" alt-text="View of Launch studio button in the Azure portal." lightbox="media/data-factory-launch-studio.png":::

### Create linked services
Azure Data Factory pipelines read from data sources and write to data sinks, typically other Azure services. These connections to other services are managed as linked services. The pipeline in this example will read data from a DICOM service and write its output to a storage account, so a linked service must be created for each. 

#### Create linked service for the DICOM service
1. In the Azure Data Factory Studio, select **Manage**  from the navigation menu. Under **Connections** select **Linked services** and then select **New**.

:::image type="content" source="media/data-factory-linked-services.png" alt-text="View of Linked services screen in Azure Data Factory." lightbox="media/data-factory-linked-services.png":::

2. On the New linked service panel, search for "REST".  Select the **REST** tile and then **Continue**.

:::image type="content" source="media/data-factory-rest.png" alt-text="New Linked services panel with REST tile selected." lightbox="media/data-factory-rest.png":::

3. Enter a **Name** and **Description** for the linked service.  

:::image type="content" source="media/data-factory-linked-service-dicom.png" alt-text="New linked service panel with DICOM service details." lightbox="media/data-factory-linked-service-dicom.png":::

4. In the **Base URL** field, enter the Service URL for your DICOM service.  For example, a DICOM service named `contosoclinic` in the `contosohealth` workspace will have the Service URL `https://contosohealth-contosoclinic.dicom.azurehealthcareapis.com`.

5. For Authentication type, select **System Assigned Managed Identity**.

6. For **AAD resource**, enter `https://dicom.healthcareapis.azure.com`.  Note, this URL is the same for all DICOM service instances.

7. After populating the required fields, select **Test connection** to ensure the identity's roles are correctly configured.  

8. When the connection test is successful, select **Create**.

#### Create linked service for Azure Data Lake Storage Gen2
1. In the Azure Data Factory Studio, select **Manage**  from the navigation menu. Under **Connections** select **Linked services** and then select **New**.

2. On the New linked service panel, search for "Azure Data Lake Storage Gen2".  Select the **REST** tile and then **Continue**.

:::image type="content" source="media/data-factory-adlsg2.png" alt-text="New Linked services panel with Azure Data Lake Storage Gen2 tile selected." lightbox="media/data-factory-adlsg2.png":::

3. Enter a **Name** and **Description** for the linked service.

:::image type="content" source="media/data-factory-linked-service-adlsg2.png" alt-text="New Linked services panel with Azure Data Lake Storage Gen2 details." lightbox="media/data-factory-linked-service-adlsg2.png":::

4. For Authentication type, select **System Assigned Managed Identity**.

5. Enter the storage account details by entering the URL to the storage account manually or by selecting the Azure subscription and storage account from dropdowns.  

6. After populating the required fields, select **Test connection** to ensure the identity's roles are correctly configured.

7. When the connection test is successful, select **Create**.

### Create a pipeline for DICOM data
Azure Data Factory pipelines are a collection of activities that perform a task, like copying DICOM metadata to Delta tables. This section details the creation of a pipeline that regularly synchronizes DICOM data to Delta tables as data is added to, updated, and deleted from a DICOM service.

1.  Select **Author** from the navigation menu.  In the **Factory Resources** pane, select the plus (+) to add a new resource.  Select **Pipeline** and then **Template gallery** from the menu.  

:::image type="content" source="media/data-factory-create-pipeline-menu.png" alt-text="New Pipeline from Template Gallery." lightbox="media/data-factory-create-pipeline-menu.png":::

2. In the Template gallery, search for "DICOM".  Select the **Copy DICOM Metadata Changes to ADLS Gen2 in Delta Format** tile and then **Continue**.

:::image type="content" source="media/data-factory-gallery-dicom.png" alt-text="DICOM template selected in Template gallery." lightbox="media/data-factory-gallery-dicom.png":::

3. In the **Inputs** section, select the linked services previously created for the DICOM service and Azure Data Lake Storage Gen2 account.

:::image type="content" source="media/data-factory-create-pipeline.png" alt-text="Create pipeline view with linked services selected." lightbox="media/data-factory-create-pipeline.png":::

4. Select **Use this template** to create the new pipeline.  

### 