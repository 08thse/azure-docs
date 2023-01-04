---
title: Change Data Capture Artifact
titleSuffix: Azure Data Factory
description: Learn more about the Change Data Capture artifact in Azure Data Factory.
author: n0elleli
ms.author: noelleli
ms.reviewer:
ms.service: data-factory
ms.subservice: data-movement
ms.custom:
ms.topic: conceptual
ms.date: 01/04/2023
---

# Overview

[!INCLUDE[appliesto-adf-asa-md]]

Adapting to the cloud-first big data world can be incredibly challenging for data engineers who are responsible for building complex data integration and ETL pipelines. 

Azure Data Factory is introducing a new mechanism to make the life of a data engineer easier. 

By automatically detecting data changes at the source without requiring complex designing or coding, ADF is making it a breeze to scale these processes. Change Data Capture will now exist as a **new native top-level artifact** in the Azure Data Factory studio where data engineers can quickly configure continuously running jobs to process big data at scale with extreme efficiency. 

The new Change Data Capture object in ADF allows for full fidelity change data capture that continuously runs in near real time through a guided configuration experience. 

:::image type="content" source="media/adf-cdc/adf-cdc-artifact-1.png" alt-text="New top-level artifact in Factory Resources panel.":::

## Supported data sources

* Avro
* Azure Cosmos DB (SQL API)
* Azure SQL Database
* Delimited Text
* JSON
* ORC
* Parquet
* SQL Server
* XML

## Supported targets

* Avro
* Azure SQL Database
* Azure Synapse Analytics
* Delimited Text
* Delta
* JSON
* ORC
* Parquet

## Known limitations
* Continuous streaming is coming soon.
* Allow schema drift is coming soon.


## Next steps
- [Learn how to set up a change data capture artifact](how-to-change-data-capture-artifact.md).

