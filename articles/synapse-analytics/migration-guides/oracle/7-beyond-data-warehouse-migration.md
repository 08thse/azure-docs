---
title: "Beyond Oracle migration, implement a modern data warehouse in Microsoft Azure"
description: Learn how a Oracle migration to Azure Synapse Analytics lets you integrate your data warehouse with the Microsoft Azure analytical ecosystem.
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

# Beyond Oracle migration, implement a modern data warehouse in Microsoft Azure

This article is part seven of a seven-part series that provides guidance on how to migrate from Oracle to Azure Synapse Analytics. The focus of this article is best practices for implementing modern data warehouses.

## Beyond data warehouse migration to Azure

A key reason to migrate your existing data warehouse to Azure Synapse Analytics is to utilize a globally secure, scalable, low-cost, cloud-native, pay-as-you-use analytical database. With Azure Synapse, you can integrate your migrated data warehouse with the complete Microsoft Azure analytical ecosystem to take advantage of other Microsoft technologies and modernize your migrated data warehouse. Those technologies include:

- [Azure Data Lake Storage](/services/storage/data-lake-storage) (ADLS) for cost effective data ingestion, staging, cleansing, and transformation. Data Lake Storage can free up the data warehouse capacity occupied by fast-growing staging tables.

- [Azure Data Factory](../../../data-factory/introduction.md) for collaborative IT and self-service data integration with [connectors](../../../data-factory/connector-overview.md) to cloud and on-premises data sources and streaming data.

- The [Common Data Model](/common-data-model/) to share consistent trusted data across multiple technologies, including:
  - Azure Synapse
  - Azure Synapse Spark
  - Azure HDInsight
  - Power BI
  - Adobe Customer Experience Platform
  - Azure IoT
  - Microsoft ISV Partners

- Microsoft [data science technologies](/azure/architecture/data-science-process/platforms-and-tools), including:
  - Azure Machine Learning studio
  - Azure Machine Learning
  - Azure Synapse Spark (Spark as a service)
  - Jupyter Notebooks
  - RStudio
  - ML.NET
  - .NET for Apache Spark, which lets data scientists use Azure Synapse data to train machine learning models at scale.
  
- [Azure HDInsight](../../../hdinsight/index.yml) to process large amounts of data, and to join big data with Azure Synapse data by creating a logical data warehouse using PolyBase.

- [Azure Event Hubs](../../../event-hubs/event-hubs-about.md), [Azure Stream Analytics](../../../stream-analytics/stream-analytics-introduction.md), and [Apache Kafka](/azure/databricks/spark/latest/structured-streaming/kafka) to integrate live streaming data from Azure Synapse.

The growth of big data has led to an acute demand for [machine learning](../../machine-learning/what-is-machine-learning.md) to enable custom-built, trained machine learning models for use in Azure Synapse. Machine learning models enable in-database analytics to run at scale in-batch, on an event-driven basis and on-demand. The ability to take advantage of in-database analytics in Azure Synapse from multiple BI tools and applications also guarantees consistent predictions and recommendations.

In addition, you can integrate Azure Synapse with Microsoft partner tools on Azure to shorten time to value.

Let's take a closer look at how you can take advantage of technologies in the Microsoft analytical ecosystem to modernize your data warehouse after you've migrated to Azure Synapse.

## Offload data staging and ETL processing to Data Lake Storage and Data Factory

Digital transformation has created a key challenge for enterprises. A torrent of new data is being generated and captured for analysis, and much of this data finds its way into data warehouses. A good example is transaction data created by opening online transactional processing (OLTP) systems to service access from mobile devices. OLTP systems are the main sources of data to data warehouses. With customers now driving the transaction rate rather than employees, the volume of data in data warehouse staging tables has been growing rapidly.

With the rapid influx of data into the enterprise, along with new sources of data like Internet of Things (IoT), companies must find ways to scale up data integration ETL processing. One method is to offload ingestion, data cleansing, transformation, and integration to a data lake and process data at scale there, as part of a data warehouse modernization program.

Once you've migrated your data warehouse to Azure Synapse, Microsoft can modernize your ETL processing by ingesting and staging data in Data Lake Storage. You can then clean, transform, and integrate your data at scale using Data Factory before loading it into Azure Synapse in parallel using PolyBase.

For ELT strategies, consider offloading ELT processing to Data Lake Storage to easily scale as your data volume or frequency grows.

### Microsoft Azure Data Factory

[Data Factory](https://azure.microsoft.com/services/data-factory/) is a pay-as-you-use, hybrid data integration service for highly scalable ETL and ELT processing. Data Factory provides a web-based user interface to build data integration pipelines with no code. With Data Factory, you can:

- Build scalable data integration pipelines code-free. Easily acquire data at scale. Pay only for what you use, and connect to on-premises, cloud, and SaaS-based data sources.

- Ingest, move, clean, transform, integrate, and analyze cloud and on-premises data at scale. Take automatic action, such as a recommendation or alert.

- Seamlessly author, monitor, and manage pipelines that span data stores both on-premises and in the cloud.

- Enable pay-as-you-go scale-out in alignment with customer growth.

You can use these features without writing any code. However, adding custom code to Data Factory pipelines is also supported. This screenshot shows an example Data Factory pipeline.

:::image type="content" source="../media/7-beyond-data-warehouse-migration/azure-data-factory-pipeline.png" border="true" alt-text="Screenshot of an example of an Azure Data Factory pipeline.":::

>[!TIP]
>Data Factory allows you to build scalable data integration pipelines without code.

Implement Data Factory pipeline development from any of several places, including:

- Microsoft Azure portal

- Microsoft Azure PowerShell

- Programmatically from .NET and Python using a multi-language SDK

- Azure Resource Manager (ARM) templates

- REST APIs

>[!TIP]
>Data Factory can connect to on-premises, cloud, and SaaS data.

Developers and data scientists who prefer to write code can easily author Data Factory pipelines in Java, Python, and .NET using the software development kits (SDKs) available for those programming languages. Data Factory pipelines can also be hybrid since they can connect, ingest, clean, transform, and analyze data in on-premises data centers, Microsoft Azure, other clouds, and SaaS offerings.

Once you develop Data Factory pipelines to integrate and analyze data, deploy those pipelines globally and schedule them to run in batch, invoke them on demand as a service, or run them in real-time on an event-driven basis. A Data Factory pipeline can also run on one or more execution engines and monitor pipeline execution to ensure performance and track errors.

>[!TIP]
>Pipelines called data factories control the integration and analysis of data. Data Factory is enterprise-class data integration software aimed at IT professionals with a data wrangling facility for business users.

#### Use cases

Data Factory supports multiple use cases.

- Prepare, integrate, and enrich data from cloud and on-premises data sources to populate your migrated data warehouse and data marts on Microsoft Azure Synapse.

- Prepare, integrate, and enrich data from cloud and on-premises data sources to produce training data for use in machine learning model development and in retraining analytical models.

- Orchestrate data preparation and analytics to create predictive and prescriptive analytical pipelines for processing and analyzing data in batch, such as sentiment analytics. Either act on the results of the analysis or populate your data warehouse with the results.

- Prepare, integrate, and enrich data for data-driven business applications running on the Azure cloud on top of operational data stores like Azure Cosmos DB.

>[!TIP]
>Build training data sets in data science to develop machine learning models.

#### Data sources

Data Factory lets you use [connectors](../../../data-factory/connector-overview.md) from both cloud and on-premises data sources. Agent software, known as a *self-hosted integration runtime*, securely accesses on-premises data sources and supports secure, scalable data transfer.

#### Transform data using Azure Data Factory

Within a Data Factory pipeline, you can ingest, clean, transform, integrate, and analyze any type of data from these sources. Data can be structured, semi-structured such as JSON or Avro, or unstructured.

Professional ETL developers can use Data Factory mapping data flows to filter, split, join many types, lookup, pivot, unpivot, sort, union, and aggregate data without writing any code. In addition, Data Factory supports surrogate keys, multiple write processing options such as insert, upsert, update, table recreation, and table truncation, and several types of target data stores&mdash;also known as sinks. ETL developers can also create aggregations, including time-series aggregations that require a window to be placed on data columns.

>[!TIP]
>Professional ETL developers can use Azure Data Factory mapping data flows to clean, transform, and integrate data without the need to write code.

You can run mapping data flows that transform data as activities in a Data Factory pipeline. Include multiple mapping data flows in a single pipeline, if necessary. Break up challenging data transformation and integration tasks into smaller mapping dataflows that can be combined to handle the complexity and custom code added, if necessary. In addition to this functionality, Data Factory mapping data flows include the ability to:

- Define expressions to clean and transform data, compute aggregations, and enrich data. For example, these expressions can perform feature engineering on a date field to break it into multiple fields to create training data during machine learning model development. You can construct expressions from a rich set of functions that include mathematical, temporal, split, merge, string concatenation, conditions, pattern match, replace, and many other functions.

- Automatically handle schema drift so that data transformation pipelines can avoid being impacted by schema changes in data sources. This ability is especially important for streaming IoT data, where schema changes can happen without notice if devices are upgraded or when readings are missed by gateway devices collecting IoT data.

- Partition data to enable transformations to run in parallel at scale.

- Inspect data to view the metadata of a stream you're transforming.

>[!TIP]
>Data Factory supports the ability to automatically detect and manage schema changes in inbound data, such as in streaming data.

This screenshot shows an example Data Factory mapping data flow.

:::image type="content" source="../media/6-microsoft-3rd-party-migration-tools/azure-data-factory-mapping-dataflows.png" border="true" alt-text="Screenshot of an example of an Azure Data Factory mapping dataflow." lightbox="../media/6-microsoft-3rd-party-migration-tools/azure-data-factory-mapping-dataflows-lrg.png":::

Data engineers can profile data quality and view the results of individual data transforms by switching on a debug capability during development.

>[!TIP]
>Data Factory can also partition data to enable ETL processing to run at scale.

Extend Data Factory transformational and analytical functionality by adding a linked service that contains your own code into a pipeline. For example, an Azure Synapse Spark pool notebook containing Python code could use a trained model to score the data integrated by a mapping data flow.

Store integrated data and any results from analytics included in a Data Factory pipeline in one or more data stores, such as Data Lake Storage, Azure Synapse, or Azure HDInsight hive tables. You can also invoke other activities to act on insights produced by a Data Factory analytical pipeline.

>[!TIP]
>Data Factory pipelines are extensible since Data Factory allows you to write your own code and run it as part of a pipeline.

#### Utilize Spark to scale data integration

Internally, Data Factory uses Azure Synapse Spark pools&mdash;Spark-as-a-service&mdash;at run time to clean and integrate data on the Azure cloud. You can clean, integrate, and analyze high-volume and very high-velocity data such as click-stream data at scale. Microsoft intends to execute Data Factory pipelines on other Spark distributions. In addition to executing ETL jobs on Spark, Data Factory can invoke Pig scripts and Hive queries to access and transform data stored in Azure HDInsight.

#### Link self-service data prep and Data Factory ETL processing using wrangling data flows

Data wrangling lets business users&mdash;also known as citizen data integrators and data engineers&mdash;make use of the platform to visually discover, explore, and prepare data at scale without writing code. This Data Factory capability is easy to use and is similar to Microsoft Excel Power Query or Microsoft Power BI dataflows, where self-service business users use a spreadsheet-style UI with drop-down transforms to prepare and integrate data. This screenshot shows an example Data Factory wrangling data flow.

:::image type="content" source="../media/6-microsoft-3rd-party-migration-tools/azure-data-factory-wrangling-dataflows.png" border="true" alt-text="Screenshot of an example of Azure Data Factory wrangling dataflows.":::

In contrast to Excel and Power BI, Data Factory [wrangling data flows](../../../data-factory/wrangling-tutorial.md) uses Power Query to generate M code and translate it into a massively parallel in-memory Spark job for cloud-scale execution. The combination of mapping data flows and wrangling data flows lets professional ETL developers and business users collaborate to prepare, integrate, and analyze data for a common business purpose. The preceding Data Factory mapping data flow diagram shows how both Data Factory and Azure Synapse Spark pool notebooks can be combined in the same Data Factory pipeline, allowing IT and business to be aware of what each has created. Mapping data flows and wrangling data flows can then be available for reuse to maximize productivity and consistency and minimize reinvention.

>[!TIP]
>Data Factory supports wrangling data flows in addition to mapping data flows, which means that business users and IT can work together on a common platform to integrate data.

#### Link data and analytics in analytical pipelines

In addition to cleaning and transforming data, Data Factory can combine data integration and analytics in the same pipeline. Use Data Factory to create both data integration and analytical pipelines&mdash;the latter being an extension of the former. Drop an analytical model into a pipeline so that clean, integrated data can be stored to provide predictions or recommendations. Act on this information immediately or store it in your data warehouse to provide new insights and recommendations that can be viewed in BI tools.

Models developed code-free with Azure Machine Learning studio, or with the Azure Machine Learning SDK using Azure Synapse Spark pool notebooks or using R in RStudio, can be invoked as a service from within a Data Factory pipeline to batch score your data. Analysis happens at scale by executing Spark machine learning pipelines on Azure Synapse Spark pool notebooks.

Store integrated data and any results from analytics included in a Data Factory pipeline in one or more data stores, such as Data Lake Storage, Azure Synapse, or Azure HDInsight (Hive tables). Invoke other activities to act on insights produced by a Data Factory analytical pipeline.

## Use a lake database to share consistent trusted data

A key objective in any data integration setup is the ability to integrate data once and reuse it everywhere, not just in a data warehouse&mdash;for example, in data science. Reuse avoids reinvention and ensures consistent, commonly understood data that everyone can trust.

>[!TIP]
>Microsoft has created a lake database to describe core data entities to be shared across the enterprise.

To achieve this goal, establish a set of common data names and definitions describing logical data entities that need to be shared across the enterprise&mdash;such as customer, account, product, supplier, orders, payments, returns, and so forth. IT and business professionals can then use data integration software to create these common data assets and store them to maximize their reuse to drive consistency everywhere.

>[!TIP]
>Data Lake Storage is shared storage that underpins Microsoft Azure Synapse, Azure Machine Learning, Azure Synapse Spark, and Azure HDInsight.

Data assets can be shared and reused because Microsoft has created a [lake database](../../database-designer/concepts-lake-database.md), which is a common language that represents commonly used concepts and activities across a business. Azure Synapse Analytics provides industry-specific database templates to help standardize data in the lake. [Lake database templates](../../database-designer/concepts-database-templates.md) provide schemas for predefined business areas, enabling data to be loaded into a lake database in a structured way. The power comes when data integration software is used to create lake database common data assets, resulting in self-describing trusted data that can be consumed by applications and analytical systems. You can create a lake database in Data Lake Storage by using Azure Data Factory, and consume it with Power BI, Azure Synapse Spark, Azure Synapse, and Azure Machine Learning. This diagram shows a lake database used in Azure Synapse Analytics.

:::image type="content" source="../media/7-beyond-data-warehouse-migration/azure-synapse-analytics-lake-database.png" border="true" alt-text="Screenshot showing how a lake database can be used in Azure Synapse Analytics.":::

>[!TIP]
>Integrate data to create lake database logical entities in shared storage to maximize the reuse of common data assets.

## Integration with Microsoft data science technologies on Azure

Another key requirement in modernizing your migrated data warehouse is to integrate it with Microsoft and third-party data science technologies on Azure to produce insights for competitive advantage. Let's look at what Microsoft offers in terms of machine learning and data science technologies and see how they can be used with Azure Synapse in a modern data warehouse environment.

### Microsoft technologies for data science on Azure

Microsoft offers a range of technologies to build predictive analytical models using machine learning, analyze unstructured data using deep learning, and perform other kinds of advanced analytics. These technologies include:

- Azure Machine Learning studio

- Azure Machine Learning

- Azure Synapse Spark pool notebooks

- ML.NET (API, CLI, or ML.NET Model Builder for Visual Studio)

- .NET for Apache Spark

Data scientists can use RStudio (R) and Jupyter Notebooks (Python) to develop analytical models, or they can use frameworks such as Keras or TensorFlow.

>[!TIP]
>Develop machine learning models using a no/low-code approach or from a range of programming languages like Python, R, and .NET.

#### Azure Machine Learning studio

Azure Machine Learning studio is a fully managed cloud service that lets you easily build, deploy, and share predictive analytics via a drag-and-drop, web-based user interface. This screenshot shows an Azure Machine Learning studio user interface.

:::image type="content" source="../media/7-beyond-data-warehouse-migration/azure-ml-studio-ui.png" border="true" alt-text="Screenshot showing predictive analysis in the Azure Machine Learning studio user interface.":::

#### Azure Machine Learning

Azure Machine Learning provides a software development kit (SDK) and services for Python to quickly prepare data, as well as train and deploy machine learning models. Use Azure Machine Learning in Azure notebooks using Jupyter Notebook, and utilize open-source frameworks, such as PyTorch, TensorFlow, scikit-learn, or Spark MLlib&mdash;Azure Spark's machine learning library. Azure Machine Learning provides an AutoML capability that automatically identifies the most accurate algorithms to expedite model development. You can also use it to build machine learning pipelines that manage end-to-end workflow, programmatically scale on the cloud, and deploy models both to the cloud and the edge. Azure Machine Learning uses logical containers called workspaces, which can be either created manually from the Azure portal or created programmatically. These workspaces keep compute targets, experiments, data stores, trained machine learning models, Docker images, and deployed services all in one place to enable teams to work together. Use Azure Machine Learning from Visual Studio with a Visual Studio for AI extension.

>[!TIP]
>Azure Machine Learning provides an SDK for developing machine learning models using several open-source frameworks.

>[!TIP]
>Organize and manage related data stores, experiments, trained models, Docker images, and deployed services in workspaces.

#### Azure Synapse Spark pool notebooks

[Azure Synapse Spark pool notebooks](../../spark/apache-spark-development-using-notebooks.md?msclkid=cbe4b8ebcff511eca068920ea4bf16b9) is an Azure-optimized Apache Spark service that:

- Allows data engineers to build and execute scalable data preparation jobs using Azure Data Factory.

- Allows data scientists to build and execute machine learning models at scale using notebooks written in languages such as Scala, R, Python, Java, and SQL, and to visualize results.

>[!TIP]
>Azure Synapse Spark is a dynamically scalable Spark-as-a-service from Microsoft, offering scalable execution of data preparation, model development, and deployed model execution.

Jobs running in Azure Synapse Spark pool notebooks can retrieve, process, and analyze data at scale from Azure Blob Storage, Data Lake Storage, Azure Synapse, Azure HDInsight, and streaming data services such as Kafka.

Autoscaling and auto-termination are also supported to reduce total cost of ownership (TCO). Data scientists can use the MLflow open-source framework to manage the machine learning lifecycle.

>[!TIP]
>Azure Synapse Spark can access data in a range of Microsoft analytical ecosystem data stores on Azure.

#### ML.NET

ML.NET is an open-source and cross-platform machine learning framework (Windows, Linux, macOS), created by Microsoft for .NET developers so that they can use existing tools&mdash;like ML.NET Model Builder for Visual Studio&mdash;to develop custom machine learning models and integrate them into .NET applications.

>[!TIP]
>Microsoft has extended its machine learning capability to .NET developers.

#### .NET for Apache Spark

.NET for Apache Spark aims to make Spark accessible to .NET developers across all Spark APIs. It takes Spark support beyond R, Scala, Python, and Java to .NET. While initially only available on Apache Spark on HDInsight, Microsoft intends to make .NET for Apache Spark available on Azure Synapse Spark pool notebook.

### Use Azure Synapse Analytics with your data warehouse

Combine machine learning models with Azure Synapse by:

- Using machine learning models in batch mode or in real-time to produce new insights, and add them to what you already know in Azure Synapse.

- Using the data in Azure Synapse to develop and train new predictive models for deployment elsewhere, such as in other applications.

- Deploying machine learning models, including models trained elsewhere, in Azure Synapse to analyze data in the data warehouse and drive new business value.

>[!TIP]
>Train, test, evaluate, and execute machine learning models at scale on Azure Synapse Spark pool notebook by using data in Azure Synapse.

Data scientists can use RStudio, Jupyter Notebooks, and Azure Synapse Spark pool notebooks together with Azure Machine Learning to develop machine learning models that run at scale on Azure Synapse Spark pool notebooks using data in Azure Synapse. For example, they could create an unsupervised model to segment customers for use in driving different marketing campaigns. Use supervised machine learning to train a model to predict a specific outcome, such as predicting a customer's propensity to churn, or recommending the next best offer for a customer to try to increase their value. This diagram shows how Azure Synapse Analytics can be used for Azure Machine Learning.

:::image type="content" source="../media/7-beyond-data-warehouse-migration/azure-synapse-train-predict.png" border="true" alt-text="Screenshot of an Azure Synapse Analytics train and predict model.":::

In addition, you can ingest big data&mdash;such as social network data or review website data&mdash;into Azure Data Lake, then prepare and analyze it at scale on Azure Synapse Spark pool notebook, using natural language processing to score sentiment about your products or your brand. Add these scores to your data warehouse to understand the impact of&mdash;for example&mdash;negative sentiment on product sales, and to use big data analytics to add to what you already know in your data warehouse.

>[!TIP]
>Produce new insights using machine learning on Azure in batch or in real-time and add to what you know in your data warehouse.

## Integrate live streaming data into Azure Synapse Analytics

When analyzing data in a modern data warehouse, you must be able to analyze streaming data in real-time and join it with historical data in your data warehouse. An example would be combining IoT data with product or asset data.

>[!TIP]
>Integrate your data warehouse with streaming data from IoT devices or clickstream.

Once you've successfully migrated your data warehouse to Azure Synapse, you can introduce this capability as part of a data warehouse modernization exercise by taking advantage of extra functionality in Azure Synapse.

>[!TIP]
>Ingest streaming data into Data Lake Storage from Azure Event Hubs or Kafka, and access it from Azure Synapse using PolyBase external tables.

To do so, ingest streaming data via Azure Event Hubs or other technologies, such as Kafka, using Azure Data Factory (or using an existing ETL tool if it supports the streaming data sources). Store the data in Data Lake Storage (ADLS). Next, create an external table in Azure Synapse using PolyBase and point it at the data being streamed into Azure Data Lake. Your migrated data warehouse will now contain new tables that provide access to real-time streaming data. Query this external table as if the data was in the data warehouse via standard T-SQL from any BI tool that has access to Azure Synapse. You can also join this data to other tables containing historical data and create views that join live streaming data to historical data to make it easier for business users to access. In the following diagram, a real-time data warehouse on Azure Synapse Analytics is integrated with streaming data in ADLS.

:::image type="content" source="../media/7-beyond-data-warehouse-migration/azure-datalake-streaming-data.png" border="true" alt-text="Screenshot of Azure Synapse Analytics with streaming data in an Azure Data Lake.":::

## Create a logical data warehouse using PolyBase

With PolyBase, you can create a logical data warehouse to simplify user access to multiple analytical data stores. Many companies have adopted "workload optimized" analytical data stores over the last several years in addition to their data warehouses. Examples of these platforms on Azure include:

- ADLS with Azure Synapse Spark pool notebook (Spark-as-a-service), for big data analytics.

- Azure HDInsight (Hadoop as-a-service), also for big data analytics.

- NoSQL Graph databases for graph analysis, which could be done in Azure Cosmos DB.

- Azure Event Hubs and Azure Stream Analytics, for real-time analysis of data in motion.

>[!TIP]
>PolyBase simplifies access to multiple underlying analytical data stores on Azure to simplify access for business users.

You may have some non-Microsoft equivalents. You may also have a master data management (MDM) system that needs to be accessed for consistent trusted data on customers, suppliers, products, assets, and more.

These additional analytical platforms have emerged because of the explosion of new data sources&mdash;both inside and outside the enterprises&mdash;that business users want to capture and analyze. Examples include:

- Machine generated data, such as IoT sensor data and clickstream data.

- Human generated data, such as social network data, review web site data, customer inbound email, images, and video.

- Other external data, such as open government data and weather data.

This data is over and above the structured transaction data and main data sources that typically feed data warehouses. These new data sources include semi-structured data (like JSON, XML, or Avro) or unstructured data (like text, voice, image, or video), which is more complex to process and analyze. This data could be very high volume, high velocity, or both.

As a result, the need for new kinds of more complex analysis has emerged, such as natural language processing, graph analysis, deep learning, streaming analytics, or complex analysis of large volumes of structured data. These new kinds of analysis typically don't happen in a data warehouse, so it's not surprising to see different analytical platforms for different types of analytical workloads, as shown in the following diagram.

:::image type="content" source="../media/7-beyond-data-warehouse-migration/analytical-workload-platforms.png" border="true" alt-text="Screenshot of different analytical platforms for different types of analytical workloads in Azure Synapse Analytics.":::

>[!TIP]
>The ability to make data in multiple analytical data stores look like it's all in one system and join it to Azure Synapse is known as a logical data warehouse architecture.

Since these platforms produce new insights, it's normal to see a requirement to combine these insights with what you already know in Azure Synapse. PolyBase makes it possible.

By leveraging PolyBase data virtualization inside Azure Synapse, you can implement a logical data warehouse. Join data in Azure Synapse to data in other Azure and on-premises analytical data stores&mdash;like Azure HDInsight or Azure Cosmos DB&mdash;or to streaming data flowing into ADLS from Azure Stream Analytics and Event Hubs. Users access external tables in Azure Synapse, unaware that the data they're accessing is stored in multiple underlying analytical systems. This diagram shows the complex data warehouse structure accessed through comparatively simpler but still powerful user interface methods.

:::image type="content" source="../media/7-beyond-data-warehouse-migration/complex-data-warehouse-structure.png" alt-text="Screenshot showing an example of a complex data warehouse structure accessed through user interface methods.":::

The diagram shows how other technologies in the Microsoft analytical ecosystem can be combined with the capability of Azure Synapse logical data warehouse architecture. For example, data can be ingested into ADLS and curated using Azure Data Factory to create trusted data products that represent Microsoft [lake database](../../database-designer/concepts-lake-database.md) logical data entities. This trusted, commonly understood data can then be consumed and reused in different analytical environments such as Azure Synapse, Azure Synapse Spark pool notebooks, or Azure Cosmos DB. All insights produced in these environments are accessible via a logical data warehouse data virtualization layer made possible by PolyBase.

>[!TIP]
>A logical data warehouse architecture simplifies business user access to data and adds new value to what you already know in your data warehouse.

## Conclusions

Once you migrate your data warehouse to Azure Synapse, you can take advantage of other technologies in the Microsoft analytical ecosystem. You don't only modernize your data warehouse, but combine insights produced in other Azure analytical data stores into an integrated analytical architecture.

Broaden your ETL processing to ingest data of any type into ADLS. Prepare and integrate it at scale using Azure Data Factory to produce trusted, commonly understood data assets that can be consumed by your data warehouse and accessed by data scientists and other applications. Build real-time and batch-oriented analytical pipelines and create machine learning models to run in batch, in real-time on streaming data, and on-demand as a service.

Use PolyBase and `COPY INTO` to go beyond your data warehouse. Simplify access to insights from multiple underlying analytical platforms on Azure by creating holistic integrated views in a logical data warehouse. Easily access streaming, big data, and traditional data warehouse insights from BI tools and applications to drive new value in your business.

>[!TIP]
>Migrating your data warehouse to Azure Synapse lets you make use of a rich Microsoft analytical ecosystem running on Azure.

## Next steps

To learn more about migrating to a dedicated SQL pool, see [Migrate a data warehouse to a dedicated SQL pool in Azure Synapse Analytics](../migrate-to-synapse-analytics-guide.md).