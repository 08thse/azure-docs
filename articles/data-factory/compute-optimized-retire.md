---
title: Compute optimized retirement
description: Data flow compute optimized option being retired
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: tutorial
ms.date: 06/09/2021
---

# Retirement of data flow compute optimized option

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory data flows provide a low-code mechanism to transform data in ETL jobs at scale using a graphical design paradigm. Data flows execute on the Azure Data Factory serverless Integration Runtime facility. The scalable nature of Azure Data Factory Integration Runtimes enabled three different compute options for the Azure Databricks Spark environment that is utilized to execute data flows at scale: Memory Optimized, General Purpose, and Compute Optimized. Memory Optimized and General Purpose are the recommended classes of data flow compute to use with your Integration Runtime for production workloads. Because Compute Optimized will often not suffice for common use cases with data flows, Microsoft is deprecating the Compute Optimized option for Integration Runtimes with data flows effective August 31, 2021. Existing data flows and Integration Runtimes that you have in your Azure Data Factory will continue with normal Microsoft Azure support until August 31, 2024. 

## Comparison between different compute options 

| Category              | Data store                                                   | [Copy activity](../copy-activity-overview.md)  (source/sink) | [Mapping Data Flow](../concepts-data-flow-overview.md) (source/sink) | [Lookup Activity](../control-flow-lookup-activity.md) | [Get Metadata Activity](../control-flow-get-metadata-activity.md)/[Validation Activity](../control-flow-validation-activity.md) | [Delete Activity](../delete-activity.md) |
| :-------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- | ------------------------------------------------------------ | :----------------------------------------------------------- |

| Compute Option | Performance |
| ---------------------------- |
| General Purpose Data Flows | Best performing runtime for data flows when working with large datasets and many calculations |
| Memory Optimized Data Flows | Good for general use cases in production workloads 
| Compute Optimized Data Flows | Not recommended for production workloads 

## Migration steps 

Your Compute Optimized data flows will continue to work in pipelines as-is. However, new Azure Integration Runtimes and data flow activities will not be able to use Compute Optimized. When creating a new data flow activity, first create a new Azure Integration Runtime with “General Purpose” or “Memory Optimized” as the compute type. Second, set your data flow activity using either of those compute types.

 
![Compute types](media/data-flow/compute-types.png)
 

[Please find more detailed information at the data flows FAQ here](https://docs.microsoft.com/en-us/azure/data-factory/frequently-asked-questions#mapping-data-flows)  
Microsoft Q&A 

Questions in tag: azure-data-factory - Microsoft Q&A 
