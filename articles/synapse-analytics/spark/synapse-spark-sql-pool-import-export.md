---
title: Import and Export data between serverless Apache Spark pools and SQL pools
description: This article introduces the Synapse SQL Connector API for moving data between dedicated SQL pools and serverless Apache Spark pools.
services: synapse-analytics
author: kevxmsft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 01/22/2022
ms.author: kevx
ms.reviewer: 
---
# Transfer data between an Azure Dedicated SQL pool and Synapse Spark

The Synapse SQL Connector is an API that efficiently transfers data between serverless Apache Spark pools and dedicated SQL pools in Azure Synapse.

## Better performance than JDBC

A JDBC connector would be a bottleneck with serial data transfer.
In contrast, the Synapse SQL Connector uses Azure Storage and [PolyBase](/sql/relational-databases/polybase/polybase-guide) to transfer data in parallel and at scale.

![Connector Architecture](./media/synapse-spark-sqlpool-import-export/arch1.png)

## Authentication in Azure Synapse Analytics

Authentication works automatically with AAD after the following prerequisites.

* Add the user to [db_exporter role](/sql/relational-databases/security/authentication-access/database-level-roles#special-roles-for--and-azure-synapse) using system stored procedure [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql).
* Add the user to [Storage Blob Data Contributor role](/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) on the storage account.

The Synapse SQL Connector also supports password-based [SQL authentication](/azure/azure-sql/database/logins-create-manage#authentication-and-authorization).
In this case, the API uses an [existing external data source](/sql/t-sql/statements/create-external-data-source-transact-sql), whose [database scoped credential](/sql/t-sql/statements/create-database-scoped-credential-transact-sql) secret is the access key to an Azure Storage Account.

## API Reference

See the [Scala API reference](https://synapsesql.blob.core.windows.net/docs/1.0.0/scaladocs/com/microsoft/spark/sqlanalytics/index.html).

## Example Usage

* Create and show a `DataFrame` representing a database table in the dedicated SQL pool.

  ```scala
  import com.microsoft.spark.sqlanalytics.utils.Constants
  import com.microsoft.spark.sqlanalytics.SqlAnalyticsConnector._

  val df = spark.read.
    option(Constants.SERVER, "servername.database.windows.net").
    synapsesql("databaseName.schemaName.tablename")

  df.show
  ```

* Save the content of a `DataFrame` to a database table in the dedicated SQL pool.

  ```scala
  import com.microsoft.spark.sqlanalytics.utils.Constants
  import com.microsoft.spark.sqlanalytics.SqlAnalyticsConnector._

  val df = spark.sql("select * from tmpview")

  df.write.
    option(Constants.SERVER, "servername.database.windows.net").
    synapsesql("databaseName.schemaName.tablename")
  ```

* Use the connector API with SQL authentication with option keys `Constants.USER` and `Constants.PASSWORD`. It also requires option key `Constants.DATA_SOURCE`, specifying an external data source.

  ```scala
  import com.microsoft.spark.sqlanalytics.utils.Constants
  import com.microsoft.spark.sqlanalytics.SqlAnalyticsConnector._

  val df = spark.read.
    option(Constants.SERVER, "servername.database.windows.net").
    option(Constants.USER, "username").
    option(Constants.PASSWORD, "password").
    option(Constants.DATA_SOURCE, "datasource").
    synapsesql("databaseName.schemaName.tablename")

  df.show
  ```

* Although the API supports only Scala, we can interact with content from a `DataFrame` in PySpark by using [DataFrame.createOrReplaceTempView](https://spark.apache.org/docs/latest/api/python/reference/api/pyspark.sql.DataFrame.createOrReplaceTempView.html#pyspark.sql.DataFrame.createOrReplaceTempView) or [DataFrame.createOrReplaceGlobalTempView](https://spark.apache.org/docs/latest/api/python/reference/api/pyspark.sql.DataFrame.createOrReplaceGlobalTempView.html#pyspark.sql.DataFrame.createOrReplaceGlobalTempView).

  ```py
  %%pyspark
  df.createOrReplaceTempView("tempview")
  ```

  ```scala
  %%spark
  import com.microsoft.spark.sqlanalytics.utils.Constants
  import com.microsoft.spark.sqlanalytics.SqlAnalyticsConnector._

  val df = spark.sqlContext.sql("select * from tempview")

  df.write.
    option(Constants.SERVER, "servername.database.windows.net").
    synapsesql("databaseName.schemaName.tablename")
  ```

## Next steps

- [Create a dedicated SQL pool using the Azure portal](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)
- [Create a new Apache Spark pool using the Azure portal](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)
- [Create, develop, and maintain Synapse notebooks in Azure Synapse Analytics](../../synapse-analytics/spark/apache-spark-development-using-notebooks.md)
