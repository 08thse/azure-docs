---
title: "Data migration, ETL, and load for Oracle migrations"
description: Learn how to plan your data migration from Oracle to Azure Synapse Analytics to minimize the risk and impact on users. 
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.custom:
ms.devlang:
ms.topic: conceptual
author: ajagadish-24
ms.author: ajagadish
ms.reviewer: wiassaf
ms.date: 06/30/2022
---

# Data migration, ETL, and load for Oracle migrations

This article is part two of a seven-part series that provides guidance on how to migrate from Oracle to Azure Synapse Analytics. The focus of this article is best practices for ETL and load migration.

## Data migration considerations

There are many factors to consider when migrating data, ETL, and loads from a legacy Oracle data warehouse and data marts to Azure Synapse. This article applies specifically to migrations from an existing Oracle environment.

### Initial decisions about data migration from Oracle

When planning a migration from an Oracle environment, consider the following data-related questions:

- Should unused table structures be migrated?

- What's the best migration approach to minimize risk and impact for users?

- When migrating data marts: stay physical or go virtual?

The next sections discuss these points within the context of a migration from Oracle.

#### Migrate unused tables?

>[!TIP]
>In legacy systems, it's not unusual for tables to become redundant over time&mdash;these don't need to be migrated in most cases.

It makes sense to only migrate tables that are in use. Tables that aren't active can be archived rather than migrated, so that the data is available if needed in the future. It's best to use system metadata and log files rather than documentation to determine which tables are in use, because documentation can be out of date.

Oracle system catalog tables and logs contain information that can be used to determine when a given table was last accessed&mdash;which in turn can be used to decide whether or not a table is a candidate for migration.

If you've licensed the [Oracle Diagnostic Pack](https://www.oracle.com/technetwork/database/enterprise-edition/overview/diagnostic-pack-11g-datasheet-1-129197.pdf), then you have access to Active Session History, which you can use to determine when a table was last accessed.

Here's an example query that looks for the usage of a specific table within a given time window:

```sql
SELECT du.username,
    s.sql_text,
    MAX(ash.sample_time) AS last_access ,
    sp.object_owner ,
    sp.object_name ,
    sp.object_alias as aliased_as ,
    sp.object_type ,
    COUNT(*) AS access_count 
FROM v$active_session_history ash         
    JOIN v$sql s ON ash.force_matching_signature = s.force_matching_signature
    LEFT JOIN v$sql_plan sp ON s.sql_id = sp.sql_id
    JOIN DBA_USERS du ON ash.user_id = du.USER_ID
WHERE ash.session_type = 'FOREGROUND'
    AND ash.SQL_ID IS NOT NULL
    AND sp.object_name IS NOT NULL
    AND ash.user_id <> 0
GROUP BY du.username,
    s.sql_text,
    sp.object_owner,
    sp.object_name,
    sp.object_alias,
    sp.object_type 
ORDER BY 3 DESC;
```

This query may take a while to run if you have been running numerous queries.

#### What's the best migration approach to minimize risk and impact on users?

> [!TIP]
> Migrate the existing model as-is initially, even if a change to the data model is planned in the future.

This question comes up frequently since companies may want to lower the impact of changes on the data warehouse data model to improve agility. Companies often see an opportunity to further modernize or transform their data during an ETL migration. This approach carries a higher risk because it changes multiple factors simultaneously, making it difficult to compare the outcomes of the old system vs the new. Making data model changes here could also impact upstream or downstream ETL jobs to other systems. Because of that risk, it's better to redesign on this scale after the data warehouse migration.

Even if a data model is intentionally changed as part of the overall migration, it's good practice to migrate the existing model as-is to Azure Synapse, rather than do any re-engineering on the new platform. This approach has the advantage of minimizing the effect on existing production systems, while also benefiting from the performance and elastic scalability of the Azure platform for one-off re-engineering tasks.

#### Data mart migration: stay physical or go virtual?

> [!TIP]
> Virtualizing data marts can save on storage and processing resources.

In legacy Oracle data warehouse environments, it's common practice to create many data marts that are structured to provide good performance for ad hoc self-service queries and reports for a given department or business function within an organization. A data mart typically consists of a subset of the data warehouse that contains aggregated versions of the data in a form that enables users to easily query that data with fast response times via user-friendly query tools such as Oracle BI EE, Microsoft Power BI, Tableau, or MicroStrategy. This form is generally a dimensional data model, and one use of data marts is to expose the data in a usable form even if the underlying warehouse data model is something different, such as a data vault.

You can use separate data marts for individual business units within an organization to implement robust data security regimes. Restrict access to specific data marts that are relevant to users, and eliminate, obfuscate, or anonymize sensitive data.

If these data marts are implemented as physical tables, they'll require extra storage resources and processing to build and refresh them regularly. Also, the data in the mart will only be as up to date as the last refresh operation, and so may be unsuitable for highly volatile data dashboards.

> [!TIP]
> The performance and scalability of Azure Synapse enables virtualization without sacrificing performance.

With the advent of lower-cost scalable MPP architectures, such as Azure Synapse, and their inherent performance characteristics, you can provide data mart functionality without having to instantiate the mart as a set of physical tables. This functionality is achieved by effectively virtualizing the data marts via SQL views onto the main data warehouse, or via a virtualization layer using features such as views in Azure or third-party virtualization products such as **Denodo**. This approach simplifies or eliminates the need for extra storage and aggregation processing and reduces the overall number of database objects to be migrated.

There's another potential benefit of this approach. By implementing the aggregation and join logic within a virtualization layer, and presenting external reporting tools via a virtualized view, the processing required to create these views is pushed down into the data warehouse, which is generally the best place to run joins, aggregations, and other related operations on large data volumes.

The primary drivers for implementing a virtual data mart over a physical data mart are:

- More agility: a virtual data mart is easier to change than physical tables and the associated ETL processes.

- Lower total cost of ownership: a virtualized implementation requires fewer data stores and copies of data.

- Elimination of ETL jobs to migrate and simplify data warehouse architecture in a virtualized environment.

- Performance: although physical data marts have historically performed better, virtualization products now implement intelligent caching techniques to mitigate this difference.

### Data migration from Oracle

#### Understand your data

As part of migration planning, you should understand in detail the volume of data to be migrated since that can impact decisions about the migration approach. Use system metadata to determine the physical space taken up by the raw data within the tables to be migrated. In this context, raw data means the amount of space used by the data rows within a table, excluding overhead such as indexes and compression. The largest fact tables will typically comprise more than 95% of the data.

This query will give you the total database size in Oracle:

```sql
SELECT
  ( SELECT SUM(bytes)/1024/1024/1024 data_size 
    FROM sys.dba_data_files ) +
  ( SELECT NVL(sum(bytes),0)/1024/1024/1024 temp_size 
    FROM sys.dba_temp_files ) +
  ( SELECT SUM(bytes)/1024/1024/1024 redo_size 
    FROM sys.v_$log ) +
  ( SELECT SUM(BLOCK_SIZE*FILE_SIZE_BLKS)/1024/1024/1024 controlfile_size 
    FROM v$controlfile ) "Size in GB"
FROM dual
```

The database size equals the size of (data files + temp files + online/offline redo log files + control files). Overall DB size includes used space and free space.

Here's an example query that will give a breakdown of disk space used by table data and indexes:

```sql
SELECT
   owner, "Type", table_name "Name", TRUNC(sum(bytes)/1024/1024) Meg
FROM
  ( SELECT segment_name table_name, owner, bytes, 'Table' as "Type" 
    FROM dba_segments 
    WHERE segment_type in  ('TABLE','TABLE PARTITION','TABLE SUBPARTITION' )
UNION ALL
    SELECT i.table_name, i.owner, s.bytes, 'Index' as "Type"
    FROM dba_indexes i, dba_segments s
    WHERE s.segment_name = i.index_name
    AND   s.owner = i.owner
    AND   s.segment_type in ('INDEX','INDEX PARTITION','INDEX SUBPARTITION')
UNION ALL
    SELECT l.table_name, l.owner, s.bytes, 'LOB' as "Type"
    FROM dba_lobs l, dba_segments s
    WHERE s.segment_name = l.segment_name
    AND   s.owner = l.owner
    AND   s.segment_type IN ('LOBSEGMENT','LOB PARTITION','LOB SUBPARTITION')
UNION ALL
    SELECT l.table_name, l.owner, s.bytes, 'LOB Index' as "Type"
    FROM dba_lobs l, dba_segments s
    WHERE s.segment_name = l.index_name
    AND   s.owner = l.owner
    AND   s.segment_type = 'LOBINDEX')
    WHERE owner in UPPER('&owner')
GROUP BY table_name, owner, "Type"
HAVING SUM(bytes)/1024/1024 > 10  /* Ignore really small tables */
ORDER BY SUM(bytes) desc;
```

In addition, the Microsoft database migration team provides many resources, including an asset called [Oracle Inventory Script Artifacts](https://datamigration.microsoft.com/scenario/oracle-to-sqldw?step=1) that can be found on [GitHub](https://github.com/Microsoft/DataMigrationTeam/tree/master/Oracle%20Inventory%20Script%20Artifacts). This tool includes a PL/SQL query that accesses Oracle system tables and provides a count of objects by schema type, object type, and status. It also provides a rough estimate of raw data in each schema and the sizing of tables in each schema, with results stored in a CSV format. An included calculator spreadsheet takes the CSV as input and provides sizing data.

You can get an accurate number for the volume of data to be migrated for a given table by extracting a representative sample of the data&mdash;for example, one million rows&mdash;to an uncompressed delimited flat ASCII data file. Then, use the size of that file to get an average raw data size per row of that table. Finally, multiply that average size by the total number of rows in the full table to give a raw data size for the table. Use that raw data size in your planning.

#### Oracle data type mapping

> [!TIP]
> Assess the impact of unsupported data types as part of the preparation phase.

Some Oracle data types aren't directly supported in Azure Synapse. The following table shows these data types, together with the recommended approach for mapping them.

| **Oracle data type** | **Azure Synapse data type** |
|-|-|
| BFILE | Not supported. Map to VARBINARY (MAX). |
| BINARY_FLOAT | Not supported. Map to FLOAT. |
| BINARY_DOUBLE | Not supported. Map to DOUBLE. |
| BLOB | Not directly supported. Replace with VARBINARY(MAX). |
| CHAR | CHAR |
| CLOB | Not directly supported. Replace with VARCHAR(MAX). |
| DATE | DATE in Oracle can also contain time information. Depending on usage map to DATE or TIMESTAMP. |
| DECIMAL | DECIMAL |
| DOUBLE | PRECISION DOUBLE |
| FLOAT | FLOAT |
| INTEGER | INT |
| INTERVAL YEAR TO MONTH | INTERVAL data types aren't supported. Use date comparison functions, such as DATEDIFF or DATEADD, for date calculations. |
| INTERVAL DAY TO SECOND | INTERVAL data types aren't supported. Use date comparison functions, such as DATEDIFF or DATEADD, for date calculations. |
| LONG | Not supported. Map to VARCHAR(MAX). |
| LONG RAW | Not supported. Map to VARBINARY(MAX). |
| NCHAR | NCHAR |
| NVARCHAR2 | NVARCHAR |
| NUMBER | NUMBER |
| NCLOB | Not directly supported. Replace with NVARCHAR(MAX). |
| NUMERIC | NUMERIC |
| ORD media data types | Not supported. |
| RAW | Not supported. Map to VARBINARY. |
| REAL | REAL |
| ROWID | Not supported. Map to GUID, which is similar. |
| SDO Geospatial data types | Not supported |
| SMALLINT | SMALLINT |
| TIMESTAMP | DATETIME2 or the CURRENT_TIMESTAMP() function |
| TIMESTAMP WITH LOCAL TIME ZONE | Not supported. Map to DATETIMEOFFSET. |
| TIMESTAMP WITH TIME ZONE | Not supported because TIME is stored using wall-clock time without a time zone offset. |
| URIType | Not supported. Store in a VARCHAR. |
| UROWID | Not supported. Map to GUID, which is similar. |
| VARCHAR | VARCHAR |
| VARCHAR2 | VARCHAR |
| XMLType | Not supported. Store XML data in a VARCHAR. |
| User-defined types: Oracle allows the definition of user-defined objects that can contain a series of individual fields, each with their own definition and default values. These user-defined objects can then be referenced within a table definition in the same way as built-in data types (for example, NUMBER or VARCHAR).    | Azure Synapse doesn't currently support this feature. If the data to be migrated includes user-defined data types, they must be either "flattened" into a conventional table definition, or normalized to a separate table in the case of arrays of data.   |

#### Use SQL queries to find data types

By querying the Oracle static data dictionary view DBA_TAB_COLUMNS, you can determine which data types are in use in a schema and whether or not any of these data types need to be changed. Use SQL queries to find the columns with data types that don't map directly to data types in Azure Synapse Analytics, and also to count the number of occurrences of each data type in any Oracle schema that don't map directly. Using the results from these queries in combination with the data type comparison table, you can determine which data types need to be changed in a Synapse environment.

To find the columns with data types that don't map to data types in Azure Synapse Analytics, use the following query replacing \<owner_name\> with the relevant owner of your schema:

```sql
SELECT owner, table_name, column_name, data_type
FROM dba_tab_columns
WHERE owner in ('<owner_name>')
AND data_type NOT IN 
    ('BINARY_DOUBLE', 'BINARY_FLOAT', 'CHAR', 'DATE', 'DECIMAL', 'FLOAT', 'LONG', 'LONG RAW', 'NCHAR', 'NUMERIC', 'NUMBER', 'NVARCHAR2', 'SMALLINT', 'RAW', 'REAL', 'VARCHAR2', 'XML_TYPE') 
ORDER BY 1,2,3;
```

To count the number of non-mappable data types, use the following query:

```sql 
SELECT data_type, count(*) 
FROM dba_tab_columns 
WHERE data_type NOT IN 
    ('BINARY_DOUBLE', 'BINARY_FLOAT', 'CHAR', 'DATE', 'DECIMAL', 'FLOAT', 'LONG', 'LONG RAW', 'NCHAR', 'NUMERIC', 'NUMBER', 'NVARCHAR2', 'SMALLINT', 'RAW', 'REAL', 'VARCHAR2', 'XML_TYPE') 
GROUP BY data_type 
ORDER BY data_type;
```

Microsoft offers SQL Server Migration Assistant (SSMA) to automate migration of data warehouses from legacy Oracle environments, including the mapping of data types. As well, you can use Azure Migration Services to help with the migration process. Third-party vendors also offer tools and services to automate migration. If Oracle Data Integrator or a third-party ETL tool, such as Informatica or Talend, is already in use in the Oracle environment, they can implement any required data transformations. The next section explores migration of existing ETL processes.

## ETL migration considerations

### Initial decisions about Oracle ETL migration

> [!TIP]
> Plan the approach to ETL migration ahead of time and leverage Azure facilities where appropriate.

For ETL/ELT processing, legacy Oracle data warehouses may use custom-built scripts, Oracle Data Integrator (ODI), or a third-party ETL tool such as Informatica or Talend. Sometimes, there's a combination of approaches that has evolved over time. When planning a migration to Azure Synapse, you need to determine the best way to implement the required ETL/ELT processing in the new environment, while minimizing the cost and risk involved.

The following sections discuss migration options and make recommendations for various use cases. This flowchart summarizes one approach:

:::image type="content" source="../media/2-etl-load-migration-considerations/migration-options-flowchart.png" border="true" alt-text="Flowchart of migration options and recommendations.":::

The first step is always to build an inventory of ETL/ELT processes that need to be migrated. With the standard "built-in" Azure features, some existing processes may not need to move. For planning purposes, it's important to understand the scale of the migration to be performed.

In the flowchart, decision 1 depends on whether you're migrating to a completely Azure-native environment. If so, we recommend that you re-engineer the ETL processing using [Pipelines and activities in Azure Data Factory](../../../data-factory/concepts-pipelines-activities.md?msclkid=b6ea2be4cfda11ec929ac33e6e00db98&tabs=data-factory) or [Azure Synapse Pipelines](../../get-started-pipelines.md?msclkid=b6e99db9cfda11ecbaba18ca59d5c95c). If you're not moving to a completely Azure-native environment, then decision 2 depends on whether an existing third-party ETL tool is already in use.

In the Oracle environment, some (or all) of the ETL processing may be performed by custom scripts using Oracle-specific utilities such as SQL\*Developer, SQL\*Loader or Data Pump. The approach in this case is to re-engineer using ADF.

If Oracle Data Integrator (ODI) or a third-party ETL tool such as Informatica or Talend is already in use&mdash;especially if there's a large investment in skills or several existing workflows and schedules use that tool&mdash;then decision 3 is whether the tool can efficiently support Azure Synapse as a target environment. Ideally, the tool will include "native" connectors that can use Azure facilities like PolyBase or [COPY INTO](/sql/t-sql/statements/copy-into-transact-sql), for the most efficient data loading. There's a way to call an external process, such as PolyBase or COPY INTO, and pass in the appropriate parameters. In this case, use existing skills and workflows, with Azure Synapse as the new target environment.

If you're using ODI for ELT processing, then ODI Knowledge Modules would be needed for Azure Synapse Analytics. If these modules aren't available to you in your organization, but you have ODI, then you can use ODI to generate flat files that can be moved to Azure and ingested into Azure Data Lake Storage Gen2 for loading into Azure Synapse Analytics.

> [!TIP]
> Consider running ETL tools in Azure to leverage performance, scalability, and cost benefits.

If you decide to retain an existing third-party ETL tool, you can run that tool within the Azure environment (rather than on an existing on-premises ETL server), and have Azure Data Factory handle the overall orchestration of the existing workflows. So, decision 4 is whether to leave the existing tool running as-is or move it into the Azure environment to achieve cost, performance, and scalability benefits.

### Re-engineer existing Oracle-specific scripts

> [!TIP]
> The inventory of ETL tasks to be migrated should include scripts and stored procedures.

If some or all of the existing Oracle warehouse ETL/ELT processing is handled by custom scripts that utilize Oracle-specific utilities, such as SQL\*Plus, SQL\*Developer, SQL\*Loader, or Data Pump, then you'll need to recode these scripts for the new Azure Synapse environment. Similarly, if ETL processes have been implemented using stored procedures in Oracle, then these processes will also have to be recoded.

Some elements of the ETL process are easy to migrate, for example, by simple bulk data load into a staging table from an external file. It may even be possible to automate those parts of the process, for example, by using Azure Synapse COPY INTO or PolyBase instead of SQL\*Loader. Other parts of the process that contain arbitrary complex SQL and/or stored procedures will take more time to re-engineer.

> [!TIP]
> Use EXPLAIN to find SQL incompatibilities.

One way of testing Oracle SQL for compatibility with Azure Synapse is to capture some representative SQL statements from a join of 
Oracle v\$active\_session_history and v\$SQL to get the sql_text, then prefix those queries with EXPLAIN. Assuming a like-for-like migrated data model in Azure Synapse, run those EXPLAIN statements in Azure Synapse. Any incompatible SQL will give an error. You can use this information to determine the scale of the re-coding task.

> [!TIP]
> Partners offer products and skills to assist in re-engineering Oracle-specific code.

In the worst case, manual re-coding may be necessary. However, there are products and services available from Microsoft partners to assist with re-engineering Oracle-specific code. For example, [Ispirer](https://www.ispirer.com/products/Oracle-to-azure-sql-data-warehouse-migration) offers tools and services to migrate Oracle SQL and stored procedures to Azure Synapse.

### Use existing third-party ETL tools

> [!TIP]
> Leverage investment in existing third-party tools to reduce cost and risk.

In many cases, the existing legacy data warehouse system will already be populated and maintained by a third-party ETL product, such as Informatica or Talend. The Oracle community frequently uses several popular ETL products. See [Azure Synapse Analytics data integration partners](../../partner/data-integration.md) for a list of current Microsoft data integration partners for Azure Synapse.

The following paragraphs discuss the most popular ETL tools currently in use with Oracle warehouses. You can run all of these products within a VM in Azure, and use them to read and write Azure databases and files.

#### Ab Initio

Ab Initio is a Business Intelligence platform comprised of six data processing products: Co\>Operating System, The Component Library, Graphical Development Environment, Enterprise Meta\>Environment, Data Profiler, and Conduct\>It. 

As a powerful GUI-based, parallel processing tool for ETL data management and analysis, Ab Initio can run in a VM within Azure and can read/write Azure Storage for files and SQL Server databases via the 'Input Table', 'Output Table', and 'Run SQL' Ab Initio components.

#### Qlik

[Qlik CloudBeam](https://www.attunity.com/products/cloudbeam/attunity-cloudbeam-azure/) for Azure Synapse enables automated and optimized data loading from many enterprise databases into Microsoft Azure Synapse&mdash;quickly, easily, and affordably. CloudBeam is available in the Microsoft Azure Marketplace.

[Qlik Replicate for Microsoft Migrations](https://www.qlik.com/us/products/technology/microsoft) is for Microsoft customers who want to migrate data from popular commercial and open-source databases to the Microsoft Data Platform, including Oracle to Azure Synapse.

Qlik benefits for Azure Synapse include:

- Continuous database to Azure Synapse loading

- Quick transfer speeds with guaranteed delivery

- Intuitive administration and scheduling

- Data integrity assurance by way of check mechanisms

- Monitoring for peace-of-mind, control, and auditing

- Industry-standard SSL encryption for security

#### Informatica

[Informatica](https://www.informatica.com/gb/) has three offerings, two of which are available in Azure Marketplace.

- [Informatica Fast Clone](https://docs.informatica.com/data-replication/fast-clone/10-0/installation-guide/installation-overview/fast-clone-installation-overview.html) is a high-performance data cloning tool for unloading and moving Oracle data quickly to other databases and warehouse appliances in a heterogeneous environment. Fast Clone supports the following unload methods: 

    - **Direct path unload** reads data directly from Oracle data files, without using the Oracle Call Interface (OCI). This method is much faster than the conventional path unload method. However, you can't use the direct path unload method to run unload jobs from a system that is remote from the Oracle source, perform SQL JOIN operations on two or more tables, nor unload data from views or cluster tables. 

    - **Conventional path unload** uses the OCI to retrieve metadata and data from the Oracle source. This method is slower than the direct path unload method, but it doesn't have the limitations of the direct path unload method. 

    If you use the direct path unload method, you must install Fast Clone on the Oracle source system and grant some system and object permissions to the Fast Clone user. 

- **Informatica Intelligent Cloud Services for Azure** offers a best-in-class solution for self-service data migration, integration, and management capabilities. You can quickly and reliably import and export petabytes of data to Azure from various sources. Informatica Intelligent Cloud Services for Azure provides native, high volume, high-performance connectivity to Azure Synapse, SQL Database, Blob Storage, Data Lake Store, and Azure Cosmos DB. 

- **Informatica PowerCenter** is a metadata-driven data integration platform that jump starts and accelerates data integration projects in order to deliver data to the business faster than manual hand coding. It serves as the foundation for your data integration investments.

#### Hitachi Vantara Pentaho

[Hitachi Vantara Pentaho](https://www.ashnik.com/pentaho-cloud-deployment-with-microsoft-azure/) provides data integration, OLAP services, reporting, information dashboards, data mining, and extract, transform, load (ETL) capabilities.

Pentaho Data Integration (PDI) provides ETL capabilities that facilitate capturing, cleansing, and storing data using a uniform and consistent format that's accessible and relevant to end users and IoT technologies.

PDI is commonly used for:

- Data migration between different databases and applications

- Loading huge data sets into databases to take full advantage of cloud, clustered, and massively parallel processing environments

- Data cleansing, with steps ranging from simple to extremely complex transformations

- Data integration, including the ability to use real-time ETL as a data source for Pentaho Reporting

- Data warehouse population with built-in support for slowly changing dimensions and surrogate key creation

PDI is available in Azure Marketplace and has connectors available for Azure services such as HDInsight.

#### Talend

[Talend Cloud](https://www.talend.com/solutions/information-technology/azure-cloud-integration/) is a unified, comprehensive, and highly scalable integration platform as-a-service (iPaaS) that makes it easy to collect, govern, transform, and share data. Within a single interface, you can use Big Data integration, Data Preparation, API Services, and Data Stewardship applications to provide trusted, governed data across your organization. Talend offers more than 900 connectors and components, built-in data quality, and native support for the latest big data and cloud technologies, in addition to software development lifecycle (SDLC) support for enterprises, at a predictable price.

With just a few clicks, you can deploy the remote engine to run integration tasks natively with your Azure account from cloud to cloud, on-premises to cloud, or cloud to on-premises, completely within your environment for enhanced performance and security.

Talend can also [use Azure tools such as PolyBase](https://www.talend.com/blog/2017/02/08/leverage-load-data-microsoft-azure-sql-data-warehouse-using-polybase-talend-etl/) to guarantee the most efficient data loading into Azure Synapse.

:::image type="content" source="../media/2-etl-load-migration-considerations/polybase-data-loading.png" border="true" alt-text="Diagram showing how PolyBase can be used for data loading into Azure Synapse.":::

#### WhereScape

[WhereScape RED](https://www.wherescape.com) automation software is an integrated development environment that provides automation to streamline workflows and eliminate hand-coding. You can cut the time to develop, deploy, and operate data infrastructure, such as data warehouses, data vaults, data marts, and data lakes, by as much as 80%.

WhereScape automation is tailored for use with Microsoft SQL Server, Microsoft Azure SQL Database, Microsoft Azure Synapse, and Microsoft Analytics Platforms System (PDW).

## Data loading from Oracle 

### Choices available when loading data from Oracle

When it comes to migrating data from an Oracle data warehouse, there are some basic questions that need to be resolved. You'll need to decide how the data will be physically moved from the existing on-premises Oracle environment into Azure Synapse in the cloud, and which tools will be used to perform the transfer and load. Consider the following questions, which are discussed in the next sections.

- Will you extract the data to files, or move it directly via a network connection?

- Will you orchestrate the process from the source system, or from the Azure target environment?

- Which tools will you use to automate and manage the process?

#### Transfer data via files or network connection?

> [!TIP]
> Understand the data volumes to be migrated and the available network bandwidth, since these factors influence the migration approach decision.

Once the database tables to be migrated have been created in Azure Synapse, you can move the data that populates those tables out of the legacy Oracle system and into the new environment. There are two basic approaches:

- **File Extract**: Extract the data from the Oracle tables to flat delimited files, normally in CSV format. There are several ways:

    - Use standard Oracle SQLPlus, SQL Developer, or SQLcl
    - Use Oracle Data Integrator (ODI) to generate flat files
    - Use Azure Data Factory Oracle Connector parallel copy to unload Oracle tables in parallel to enable loading of data by partitions
    - Use third-party tooling such as [Informatica Fast Clone](https://docs.informatica.com/data-replication/fast-clone/10-0/installation-guide/installation-overview/fast-clone-installation-overview.html) direct path (fastest method) or conventional path unload
    - Use a third-party ETL tool such as Informatica or Talend

    Further information on some of these approaches is covered in the appendices.

    This approach requires space to land the extracted data files. The space could be local to the Oracle source database (if sufficient storage is available), or remote in Azure Blob Storage. The best performance is achieved when a file is written locally, since that avoids network overhead.

    To minimize storage and network transfer requirements, it's good practice to compress the extracted data files using a utility like gzip.

    Once extracted, the flat files can be moved into Azure Blob Storage. Microsoft provides various options to move large volumes of data, including AzCopy for moving files across the network into Azure Storage, Azure ExpressRoute for moving bulk data over a private network connection, and Azure Data Box for files moving to a physical storage device that's then shipped to an Azure data center for loading. For more information, see [data transfer](/azure/architecture/data-guide/scenarios/data-transfer.md).

- **Direct extract and load across network**: The target Azure environment sends a data extract request, normally via a SQL command, to the legacy Oracle system to extract the data. The results are sent across the network and loaded directly into Azure Synapse, with no need to land the data into intermediate files. The limiting factor in this scenario is normally the bandwidth of the network connection between the Oracle database and the Azure environment. For very large data volumes, this approach may not be practical.

There's also a hybrid approach that uses both methods. For example, you can use the direct network extract approach for smaller dimension tables and samples of the larger fact tables to quickly provide a test environment in Azure Synapse. For large-volume historical fact tables, you can use the file extract and transfer approach using Azure Data Box.

#### Orchestrate from Oracle or Azure?

The recommended approach when moving to Azure Synapse is to orchestrate the data extract and loading from the Azure environment using [SQL Server Migration Assistant](/sql/ssma/oracle/sql-server-migration-assistant-for-oracle-oracletosql.md) (SSMA) or [Azure Data Factory](../../../data-factory/concepts-pipelines-activities.md?msclkid=b6ea2be4cfda11ec929ac33e6e00db98&tabs=data-factory), and associated utilities, such as PolyBase or COPY INTO, for the most efficient data loading. This approach leverages Azure capabilities and provides an easy method to build reusable data loading pipelines.

Other benefits of this approach include reduced impact on the Oracle system during the data load process, since the management and loading process is running in Azure, and the ability to automate the process by using metadata-driven data load pipelines.

#### Which tools can be used?

The task of data transformation and movement is the basic function of all ETL products, such as ODI or Informatica, and also more modern data warehouse automation products such as WhereScape. If one of these products is already in use in the existing Oracle environment, then you may be able to use the existing ETL tool to simplify data migration from Oracle to Azure Synapse. This approach assumes that the ETL tool supports Azure Synapse as a target environment. 

Even if an existing ETL tool isn't in place, consider using a tool to simplify the migration task. Tools such as [Attunity Replicate](https://www.attunity.com/products/replicate/) are designed to simplify the task of data migration.

Finally, if using an ETL tool, consider running that tool within the Azure environment to benefit from Azure cloud performance, scalability, and cost. This approach also frees up resources in the Oracle data center.

## Summary

To summarize, our recommendations for migrating data and associated ETL processes from Oracle to Azure Synapse are:

- Plan ahead to ensure a successful migration exercise.

- Build a detailed inventory of data and processes to be migrated as soon as possible.

- Use system metadata and log files to get an accurate understanding of data and process usage. Don't rely on documentation since it may be out of date.

- Understand the data volumes to be migrated, and the network bandwidth between the on-premises data center and Azure cloud environments.

- Consider using an Oracle instance in an Azure VM as a stepping stone to offload migration from the legacy Oracle environment.

- Leverage standard "built-in" Azure features to minimize the migration workload.

- Identify and understand the most efficient tools for data extraction and loading in both Oracle and Azure environments. Use the appropriate tools in each phase of the process.

- Use Azure facilities, such as Azure Data Factory, to orchestrate and automate the migration process while minimizing impact on the Oracle system.

## Appendices: Example techniques for extracting Oracle data

Many techniques can be used to extract Oracle data when migrating from Oracle to Azure Synapse Analytics. More details on Oracle SQL Developer and Azure Data Factory Oracle Connector are included below:

### Oracle SQL Developer Data Extract

SQL Developer GUI has an export option that allows export to many formats, including CSV, using a simple GUI-based method:

:::image type="content" source="../media/2-etl-load-migration-considerations/oracle-sql-developer-export-option.png" border="true" alt-text="Screenshot of the SQL Developer GUI export wizard.":::

Other export options include JSON and XML. There's also an option in the GUI that allows you to build a "cart" that consists of a set of table names. You can then apply the export to the entire set:

:::image type="content" source="../media/2-etl-load-migration-considerations/oracle-sql-developer-export-option-2.png" border="true" alt-text="Screenshot of the SQL Developer GUI cart option.":::

This Oracle data export capability is also available via Oracle SQL Developer command line option. It can be automated as part of a shell script.

For relatively small tables, this option could be useful if there are any problems getting a direct connection extract working.

### Using Azure Data Factory Oracle Connector parallel copy 

You can also use the Oracle Connector in Azure Data Factory (ADF) to unload large Oracle tables in parallel, in order to enable loading of data by partitions. The Azure Data Factory Oracle connector provides built-in data partitioning to copy data from Oracle in parallel. You can find data partitioning options on the Source tab of the copy activity.

:::image type="content" source="../media/2-etl-load-migration-considerations/azure-data-factory-source-tab.png" border="true" alt-text="Screenshot of Azure Data Factory Oracle partition options in the source tab.":::

When you enable partitioned copy, ADF runs parallel queries against your Oracle source to load data by partitions. The parallel degree is controlled by the [parallel copies](../../../data-factory/copy-activity-performance.md#parallel-copy) setting on the copy activity. For example, if you set parallelCopies to "eight", ADF concurrently generates and runs eight queries based on your specified partition option and settings, and each query retrieves a portion of data from your Oracle database.

If you use ADF, we recommended that you enable parallel copy with data partitioning, especially when you plan to migrate large amounts of data from your Oracle database. The following are suggested configurations for different scenarios. When you copy data into file-based data store, writing to a folder as multiple files (only specify folder name) is faster than writing to a single file.

| **Scenario** | **Suggested settings** |
|--|--|
| Full load from large table, with physical partitions | **Partition option**: Physical partitions of table.<br><br> During execution, ADF automatically detects the physical partitions, and copies data by partitions. |
| Full load from large table, without physical partitions, while with an integer column for data partitioning | **Partition options**: Dynamic range partition.<br><br> **Partition column**: Specify the column used to partition data. If not specified, the primary key column is used. |
| Load a large amount of data by using a custom query, with physical partitions | **Partition option:** Physical partitions of table.<br> **Query**: SELECT \* FROM \<TABLENAME\> PARTITION(\"?AdfTabularPartitionName\") WHERE \<your_additional_where_clause\><br><br> **Partition name**: Specify the partition name(s) to copy data from. If not specified, ADF automatically detects the physical partitions on the table you specified in the Oracle dataset.<br><br> During execution, ADF replaces ?AdfTabularPartitionName with the actual partition name, and sends to Oracle. |
| Load a large amount of data by using a custom query, without physical partitions, while with an integer column for data partitioning | **Partition options**: Dynamic range partition.<br> **Query**: SELECT \* FROM \<TABLENAME\> WHERE ?AdfRangePartitionColumnName \<= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName \>= ?AdfRangePartitionLowbound AND \<your_additional_where_clause\><br><br> **Partition column**: Specify the column used to partition data. You can partition against the column with integer data type.<br><br> **Partition upper bound** and **partition lower bound**: Specify if you want to filter against partition column to retrieve data only between the lower and upper range.<br><br> During execution, ADF replaces ?AdfRangePartitionColumnName, ?AdfRangePartitionUpbound, and ?AdfRangePartitionLowbound with the actual column name and value ranges for each partition, and sends to Oracle.<br> For example, if your partition column \"ID\" is set with the lower bound as 1 and the upper bound as 80, with parallel copy set as 4, ADF retrieves data by four partitions. Their IDs are between \[1, 20\], \[21, 40\], \[41, 60\], and \[61, 80\], respectively. |

More information on ADF copy activity performance and scalability can be found in the [Copy activity performance and scalability guide](../../../data-factory/copy-activity-performance.md).

## Next steps

To learn about security access operations, see the next article in this series: [Security, access, and operations for Oracle migrations](3-security-access-operations.md).