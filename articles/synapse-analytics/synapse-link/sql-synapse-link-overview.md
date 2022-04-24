---
title: What is Synapse Link for SQL? (Preview)
description: Learn about Synapse Link for SQL, the benefit it offers and price
services: synapse-analytics 
author: SnehaGunda
ms.service: synapse-analytics 
ms.topic: conceptual
ms.subservice: synapse-link
ms.date: 04/18/2022
ms.author: sngun
ms.reviewer: sngun
---

# What is Synapse Link for SQL? (Preview)

Azure Synapse Link for SQL enables near real time analytics over operational data in Azure SQL Database or SQL Server 2022. With a seamless integration between operational stores including Azure SQL Database and SQL Server 2022 and Azure Synapse Analytics, Azure Synapse Link for SQL enables you to run analytics, business intelligence and machine learning scenarios on your operational data with minimum impact on source databases with a new change feed technology.

> [!IMPORTANT]
> Synapse Link for Azure SQL Database is currently in PREVIEW.
> See the [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) for legal terms that apply to Azure features that are in beta, preview, or otherwise not yet released into general availability.

The following image shows the Azure Synapse Link integration with Azure SQL DB, SQL Server 2022 and Azure Synapse Analytics:

:::image type="content" source="../media/sql-synapse-link-overview/synapse-link-sql-architecture.png" alt-text="Synapse Link for SQL architecture image.":::

## Benefit

Azure Synapse Link for SQL provides fully managed and turnkey experience for you to land operational data in Azure Synapse dedicated SQL pool. It does this by continuously replicating the data from Azure SQL DB or SQL Server 2022 with full consistency. By using Azure Synapse Link for SQL, you can get the following benefits:

* **Minimum impact on operational workload**
With the new change feed technology in Azure SQL Database and SQL Server 2022, Synapse link for SQL can automatically extract incremental changes from Azure SQL DB or SQL Server 2022. It then replicates to Azure Synapse dedicated SQL pool without impacting the operational workload.

* **Reduced complexity with No ETL jobs to manage**
After going through a few clicks including selecting operational database and tables, updates made to the operational data in Azure SQL DB or SQL Server 2022 are visible in the Azure Synapse dedicated SQL pool. They're available in near real-time with no ETL or data integration logic. You can focus on analytical and reporting logic against operational data via all the capabilities within Azure Synapse Analytics.

* **Near real-time insights into your operational data**
You can now get rich insights by analyzing operational data in Azure SQL DB or SQL Server 2022 in near real-time to enable new business scenarios including operational BI reporting, real time scoring and personalization, or supply chain forecasting etc. via Synapse link for SQL.

## Next steps

* [Synapse Link for Azure SQL Database (Preview)](sql-database-synapse-link.md).
* [Synapse Link for SQL Server 2022 (Preview)](sql-server-2022-synapse-link.md).
* How to [Configure Synapse Link for SQL Server 2022 (Preview)](connect-synapse-link-sql-server-2022.md).
* How to [Configure Synapse Link for Azure SQL Database (Preview)](connect-synapse-link-sql-database.md).
