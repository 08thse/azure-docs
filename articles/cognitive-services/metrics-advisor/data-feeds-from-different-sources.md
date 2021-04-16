---
title: Connect different data sources
titleSuffix: Azure Cognitive Services
description: add different data feeds to Metrics Advisor
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 4/17/2021
ms.author: mbullwin
---


# How-to: Connect different data sources

Use this article to find the settings and requirements for connecting different types of data sources to Metrics Advisor. Make sure to read how to [Onboard your data](how-tos/onboard-your-data.md) to learn about the key concepts for using your data with Metrics Advisor. 

## Supported authentication types

| Authentication types | Description |
| ---------------------|-------------|
|**Basic** | You will need to be able to provide basic parameters for accessing data sources. For example a connection string or a password. Data feed admins are able to view these credentials. |
| **Azure Managed Identity** | [Managed identities](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) for Azure resources is a feature of Azure Active Directory. It provides Azure services with an automatically managed identity in Azure AD. You can use the identity to authenticate to any service that supports Azure AD authentication.|
| **Azure SQL Connection String**| Store your AzureSQL connection string as a **credential entity** in Metrics Advisor, and use it directly each time when onboarding metrics data. Only admins of the credential entity are able to view these credentials, but enables authorized viewers to create data feeds without needing to know details for the credentials. |
| **Data Lake Gen2 Shared Key**| Store your data lake account key as a **credential entity** in Metrics Advisor and use it directly each time when onboarding metrics data. Only admins of the Credential entity are able to view these credentials, but enables authorized viewers to create data feed without needing to know the credential details.|
| **Service principal**| Store your [Service Principal](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) as a **credential entity** in Metrics Advisor and use it directly each time when onboarding metrics data. Only admins of Credential entity are able to view the credentials, but enables authorized viewers to create data feed without needing to know the credential details.|
| **Service principal from key vault**|Store your [Service Principal in a Key Vault](https://docs.microsoft.com/azure-stack/user/azure-stack-key-vault-store-credentials) as a **credential entity** in Metrics Advisor and use it directly each time when onboarding metrics data. Only admins of a **credential entity** are able to view the credentials, but also leave viewers able to create data feed without needing to know detailed credentials. |

## Create a credential entity to manage your credential in secure

You can create a **credential entity** to store credential related information, and use it for authenticating to your data sources. You can share the credential entity to others and enable them to connect to your data sources without sharing the real credentials. It can be created in 'Adding data feed' tab or 'Credential entity' tab. After creating a credential entity for a specific authentication type, you can just choose one credential entity you created when adding new datafeed, and it will be really convenient when creating multiple data feeds. The procedure of creating and using a credential entity is shown below:

1. Click '+' to create a new credential entity in 'Adding data feed' tab  (you can also create one in 'Credential entity feed' tab ).

   ![create credential entity](media/create-credential-entity.png)
 
2. Set the credential entity name, description (if needed) and credential type (equals to *authentication types*).

   ![set credential entity](media/set-credential-entity.png)
 
3. After creating a credential entity, you can choose it when specifying authentication type.

   ![choose credential entity](media/choose-credential-entity.png)
 
## Data sources supported and corresponding authentication types

| Data sources | Authentication Types |
|-------------| ---------------------|
|[**Azure Application Insights**](#appinsights)|  Basic |
|[**Azure Blob Storage (JSON)**](#blob) | Basic<br>ManagedIdentity |
|[**Azure Cosmos DB (SQL)**](#cosmosdb) | Basic |
|[**Azure Data Explorer (Kusto)**](#kusto) | Basic<br>Managed Identity<br>Service principal<br>Service principal from key vault |
|[**Azure Data Lake Storage Gen2**](#adl) | Basic<br>Data Lake Gen2 Shared Key<br>Service principal<br>Service principal from key vault |
|[**Azure Log Analytics**](#log)| Basic<br>Service principal<br>Service principal from key vault |
|[**Azure SQL Database / SQL Server**](#sql) | Basic<br>Managed Identity<br>Service principal<br>Service principal from key vault<br>Azure SQL Connection String |
|[**Azure Table Storage**](#table) | Basic | 
|[**ElasticSearch**](#es) | Basic |
|[**Http request**](#http) | Basic | 
|[**InfluxDB (InfluxQL)**](#influxdb) | Basic |
|[**MongoDB**](#mongodb) | Basic |
|[**MySQL**](#mysql) | Basic |
|[**PostgreSQL**](#pgsql)| Basic|
|[**Local files(CSV)**](#csv)| Basic|

The following sections specify the parameters required for all authentication types within different data source scenarios. 

## <span id="appinsights">Azure Application Insights</span>

* **Application ID**: This is used to identify this application when using the Application Insights API. To get the Application ID, do the following:

   1. From your Application Insights resource, click API Access.
   
      ![Get application ID from your Application Insights resource](media/portal-app-insights-appid.png)

   2. Copy the Application ID generated into **Application ID** field in Metrics Advisor. 

* **API Key**: API keys are used by applications outside the browser to access this resource. To get the API key, do the following:

   1. From the Application Insights resource, click **API Access**.

   2. Click **Create API Key**.

   3. Enter a short description, check the **Read telemetry** option, and click the **Generate key** button.

      ![Get API key in Azure Portal](media/portal-app-insights-appid-apikey.png)

       > [!WARNING]
       > Copy this **API key** and save it because this key will never be shown to you again. If you lose this key, you have to create a new one.

   4. Copy the API key to the **API key** field in Metrics Advisor.

* **Query**: Azure Application Insights logs are built on Azure Data Explorer, and Azure Monitor log queries use a version of the same Kusto query language. The [Kusto query language documentation](https://docs.microsoft.com/azure/data-explorer/kusto/query/) has all of the details for the language and should be your primary resource for writing a query against Application Insights. 

    Sample query:

    ``` Kusto
    [TableName] | where [TimestampColumn] >= @IntervalStart and [TimestampColumn] < @IntervalEnd;
    ```
    Besides, you can read [Tutorial: Write a valid query](tutorial/write-a-valid-query.md) for more specific examples.
  
## <span id="blob">Azure Blob Storage (JSON)</span>

* **Connection String**: There are two Authentication types for Azure Blob Storage(JSON), one is **Basic**, the other is **Managed Identity**.

    * **Basic** : See [Configure Azure Storage connection strings](https://docs.microsoft.com/azure/storage/common/storage-configure-connection-string#configure-a-connection-string-for-an-azure-storage-account) for information on retrieving this string. Also, you can just go to Azure portal for your Azure Blob Storage resource, and find connection string directly in **Settings > Access keys** section.
    
    * **Managed Identity**: Managed identities for Azure resources can authorize access to blob and queue data using Azure AD credentials from applications running in Azure virtual machines (VMs), function apps, virtual machine scale sets, and other services. 
    
    You can create managed identity in Azure portal for your Azure Blob Storage resource, and choose **role assignments** in **Access Control(IAM)** section, then click **add** to create. A suggested role type is: Storage Blob Data Reader.
    
    ![MI blob](media/MI-blob.png)
    

* **Container**: Metrics Advisor expects time series data stored as Blob files (one Blob per timestamp) under a single container. This is the container name field.

* **Blob Template**: Metrics Advisor uses path to find the json file in your Blob storage. This is an example of a Blob file template, which is used to find the json file in your Blob storage: `%Y/%m/FileName_%Y-%m-%d-%h-%M.json`. "%Y/%m" is the path, if you have "%d" in your path, you can add after "%m". If your JSON file is named by date, you could also use `%Y-%m-%d-%h-%M.json`.

   The following parameters are supported:
    * `%Y` is the year formatted as `yyyy`
    * `%m` is the month formatted as `MM`
    * `%d` is the day formatted as `dd`
    * `%h` is the hour formatted as `HH`
    * `%M` is the minute formatted as `mm`
  
  For example, in the following dataset, the blob template should be "%Y/%m/%d/00/JsonFormatV2.json".
  
  ![blob template](media/blob-template.png)
  

* **JSON format version**: Defines the data schema in the JSON files. Currently Metrics Advisor supports two versions, you can choose one to fill in the field:
  
  * **v1** (Default value)

      Only the metrics *Name* and *Value* are accepted. For example:
    
      ``` JSON
      {"count":11, "revenue":1.23}
      ```

  * **v2**

      The metrics *Dimensions* and *timestamp* are also accepted. For example:
      
      ``` JSON
      [
        {"date": "2018-01-01T00:00:00Z", "market":"en-us", "count":11, "revenue":1.23},
        {"date": "2018-01-01T00:00:00Z", "market":"zh-cn", "count":22, "revenue":4.56}
      ]
      ```

    Only one timestamp is allowed per JSON file. 

## <span id="cosmosdb">Azure Cosmos DB (SQL)</span>

* **Connection String**: The connection string to access your Azure Cosmos DB. This can be found in the Cosmos DB resource in Azure Portal, in **Keys**. Also, you can find more information in [Secure access to data in Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data).
* **Database**: The database to query against. This can be found in the **Browse** page under **Containers** section in Azure portal.
* **Collection ID**: The collection ID to query against. This can be found in the **Browse** page under **Containers** section in Azure portal.
* **SQL Query**: A SQL query to get and formulate data into multi-dimensional time series data. You can use the `@IntervalStart` and `@IntervalEnd` variables in your query. They should be formatted: `yyyy-MM-ddTHH:mm:ssZ`.

    Sample query:
    
    ```SQL
    SELECT [TimestampColumn], [DimensionColumn], [MetricColumn] FROM [TableName] WHERE [TimestampColumn] >= @IntervalStart and [TimestampColumn] < @IntervalEnd    
    ```

    Besides, you can read [Tutorial: Write a valid query](tutorial/write-a-valid-query.md) for more specific examples.

## <span id="kusto">Azure Data Explorer (Kusto)</span>

* **Connection String**: There are four Authentication types for Azure Data Explorer (Kusto), they are **Basic**, **Service Principal**, **Service Principal From KeyVault** and **Managed Identity**.
    
    * **Basic** : Metrics Advisor supports accessing Azure Data Explorer(Kusto) by using Azure AD application authentication. You will need to create and register an Azure AD application and then authorize it to access an Azure Data Explorer database, see detail in [Create an AAD app registration in Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/provision-azure-ad-app) documentation.
        Here is an example of connection string:
        
        ```
        Data Source=<Server>;Initial Catalog=<Database>;AAD Federated Security=True;Application Client Id=<Application Client Id>;Application Key=<Application Key>;Authority Id=<TenantId>
        ```

    * **Service Principal** : A service principal is a concrete instance created from the application object and inherits certain properties from that application object. The service principal object defines what the app can actually do in the specific tenant, who can access the app, and what resources the app can access.
    
        First, you will need to create and register an Azure AD application and then authorize it to access an Azure Data Explorer database, see detail in [Create an AAD app registration in Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/provision-azure-ad-app) documentation.

        Then, you can go through [Manage Azure Data Explorer database permissions](https://docs.microsoft.com/azure/data-explorer/manage-database-permissions) to know about Service Principal and set service principals. 

        Also, you need to **create a credential entity** in Metric Advisor, so that you can choose that entity whe adding data feed for Service Principal authentication type. 
        
        Here is an example of connection string:
        
        ```
        Data Source=<Server>;Initial Catalog=<Database>
        ```

    * **Service Principal From Key Vault**: Key Vault helps to safeguard cryptographic keys and secrets that cloud apps and services use. By using Key Vault, you can encrypt keys and secrets. You should create a service principal first, and then store the service principal inside Key Vault.  You can go through [Store service principal credentials in Azure Stack Hub Key Vault](https://docs.microsoft.com/azure-stack/user/azure-stack-key-vault-store-credentials) to follow detailed procedure to set service principal from key vault. 
        Here is an example of connection string: 
        ```
        Data Source=<Server>;Initial Catalog=<Database>
        ```

    * **Managed Identity**: Managed identities for Azure resources can authorize access to blob and queue data using Azure AD credentials from applications running in Azure virtual machines (VMs), function apps, virtual machine scale sets, and other services. By using managed identities for Azure resources together with Azure AD authentication, you can avoid storing credentials with your applications that run in the cloud. Learn how to [authorize with a managed identity](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-msi#enable-managed-identities-on-a-vm). 
    
        You can create managed identity in Azure portal for your Azure Data Explorer (Kusto), choose **Persmissions** section, and click **add** to create. The suggested role type is: admin / viewer.
        
        ![MI kusto](media/MI-kusto.png)

        Here is an example of connection string: 
        ```
        Data Source=<Server>;Initial Catalog=<Database>
        ```

        Besides, you can read [Tutorial: Write a valid query](tutorial/write-a-valid-query.md) for more specific examples.

* **Query**: See [Kusto Query Language](https://docs.microsoft.com/azure/data-explorer/kusto/query) to get and formulate data into multi-dimensional time series data. You can use the `@IntervalStart` and `@IntervalEnd` variables in your query. They should be formatted: `yyyy-MM-ddTHH:mm:ssZ`.

    Sample query:
    
    ``` Kusto
   [TableName] | where [TimestampColumn] >= datetime(@IntervalStart) and [TimestampColumn] < datetime(@IntervalEnd);    
   ```

    Besides, you can read [Tutorial: Write a valid query](tutorial/write-a-valid-query.md) for more specific examples.

## <span id="adl">Azure Data Lake Storage Gen2</span>

* **Account Name**: There are four Authentication types for Azure Data Lake Storage Gen2, they are **Basic**, **Azure Data Lake Storage Gen2 Shared Key**, **Service Principal** and **Service Principal From KeyVault**.
    
    * **Basic**: The **Account Name** of your Azure Data Lake Storage Gen2. This can be found in your Azure Storage Account (Azure Data Lake Storage Gen2) resource in **Access keys**. 

    * **Azure Data Lake Storage Gen2 Shared Key**: First, you should specify the account key to access your Azure Data Lake Storage Gen2 （the same as Account Key in *Basic* authentication type）. This could be found in Azure Storage Account (Azure Data Lake Storage Gen2) resource in **Access keys** setting. Then you should create a credential entity for *Azure Data Lake Storage Gen2 Shared Key* type and fill the account key in. 
    The account name is the same as *Basic* authentication type.
    
    * **Service Principal**: A service principal is a concrete instance created from the application object and inherits certain properties from that application object. A service principal is created in each tenant where the application is used and references the globally unique app object. The service principal object defines what the app can actually do in the specific tenant, who can access the app, and what resources the app can access.
    
        First, you will need to create and register an Azure AD application and then authorize it to access an Azure Data Explorer database, see detail in [Create an AAD app registration in Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/provision-azure-ad-app) documentation.

        Then, you can go through [Manage Azure Data Explorer database permissions](https://docs.microsoft.com/azure/data-explorer/manage-database-permissions) to know about Service Principal and set service principals. 

        Also, you need to **create a credential entity** in Metric Advisor, so that you can choose that entity whe adding data feed for Service Principal authentication type. 
        
        The account name is the same as **Basic** authentication type.
    
    * **Service Principal From Key Vault** authentication type: Key Vault helps to safeguard cryptographic keys and secrets that cloud apps and services use. By using Key Vault, you can encrypt keys and secrets. You should create a service principal first, and then store the service principal inside Key Vault.  You can go through [Store service principal credentials in Azure Stack Hub Key Vault](https://docs.microsoft.com/azure-stack/user/azure-stack-key-vault-store-credentials) to follow detailed procedure to set service principal from key vault. 
    The account name is the same as *Basic* authentication type.
   

* **Account Key**(only *Basic* needs): Please specify the account key to access your Azure Data Lake Storage Gen2. This could be found in Azure Storage Account (Azure Data Lake Storage Gen2) resource in **Access keys** setting.

* **File System Name (Container)**: Metrics Advisor will expect your time series data stored as Blob files (one Blob per timestamp) under a single container. This is the container name field. This can be found in your Azure storage account (Azure Data Lake Storage Gen2)  instance, and click **'Containers'** in **'Data Lake Storage'** section, then you will see the container name.

* **Directory Template**:
This is the directory template of the Blob file.
    The following parameters are supported:
    * `%Y` is the year formatted as `yyyy`
    * `%m` is the month formatted as `MM`
    * `%d` is the day formatted as `dd`
    * `%h` is the hour formatted as `HH`
    * `%M` is the minute formatted as `mm`
 
    Query sample for a daily metric: `%Y/%m/%d`.

    Query sample for an hourly metric: `%Y/%m/%d/%h`.
    


* **File Template**:
Metrics Advisor uses path to find the json file in your Blob storage. This is an example of a Blob file template, which is used to find the json file in your Blob storage: `%Y/%m/FileName_%Y-%m-%d-%h-%M.json`.    `%Y/%m` is the path, if you have `%d` in your path, you can add after `%m`. 

    The following parameters are supported:
  * `%Y` is the year formatted as `yyyy`
  * `%m` is the month formatted as `MM`
  * `%d` is the day formatted as `dd`
  * `%h` is the hour formatted as `HH`
  * `%M` is the minute formatted as `mm`

  Currently Metrics Advisor supports the data schema in the JSON files as follow. For example:

    ``` JSON
    [
      {"date": "2018-01-01T00:00:00Z", "market":"en-us", "count":11, "revenue":1.23},
      {"date": "2018-01-01T00:00:00Z", "market":"zh-cn", "count":22, "revenue":4.56}
    ]
    ```
<!--
## <span id="eventhubs">Azure Event Hubs</span>

* **Connection String**: This can be found in 'Shared access policies' in your Event Hubs instance. Also for the 'EntityPath', it could be found by clicking into your Event Hubs instance and clicking at 'Event Hubs' in 'Entities' blade. Items that listed can be input as EntityPath. 

* **Consumer Group**: A [consumer group](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#consumer-groups) is a view (state, position, or offset) of an entire event hub.
Event Hubs use the latest offset of a consumer group to consume (subscribe from) the data from data source. Therefore a dedicated consumer group should be created for one data feed in your Metrics Advisor instance.

* **Timestamp**: Metrics Advisor uses the Event Hubs timestamp as the event timestamp if the user data source does not contain a timestamp field.
The timestamp field must match one of these two formats:

    * "YYYY-MM-DDTHH:MM:SSZ" format;

    * Number of seconds or milliseconds from the epoch of 1970-01-01T00:00:00Z.

    No matter which timestamp field it left aligns to granularity.For example, if timestamp is "2019-01-01T00:03:00Z", granularity is 5 minutes, then Metrics Advisor aligns the timestamp to "2019-01-01T00:00:00Z". If the event timestamp is "2019-01-01T00:10:00Z",  Metrics Advisor uses the timestamp directly without any alignment.
-->

## <span id="log">Azure Log Analytics</span>
There are three Authentication types for Azure Log Analytics, they are **Basic**, **Service Principal** and **Service Principal From KeyVault**.
* **Basic**: You will need to fill in **Tenant Id**, **Client Id**, **Client Secret**, **Workspace ID**.
   To get **Tenant ID**, **Client ID**, **Client Secret**, please refer to [Register app or web API](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app).
   * **Tenant Id**: Specify the tenant id to access your Log Analytics.
   * **Client Id**: Specify the client id to access your Log Analytics.
   * **Client Secret**: Specify the client secret to access your Log Analytics.
   * **Workspace ID**: Specify the workspace Id of Log Analytics. For **Wordkspace ID**, you can find it in Azure portal.

    ![workspace id](media/workspace-id.png)
    
* **Service Principal**: A service principal is a concrete instance created from the application object and inherits certain properties from that application object. A service principal is created in each tenant where the application is used and references the globally unique app object. The service principal object defines what the app can actually do in the specific tenant, who can access the app, and what resources the app can access.
    
     First, you will need to create and register an Azure AD application and then authorize it to access an Azure Data Explorer database, see detail in [Create an AAD app registration in Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/provision-azure-ad-app) documentation.

     Then, you can go through [Manage Azure Data Explorer database permissions](https://docs.microsoft.com/azure/data-explorer/manage-database-permissions) to know about Service Principal and set service principals. 

     Also, you need to **create a credential entity** in Metric Advisor, so that you can choose that entity whe adding data feed for Service Principal authentication type. 
        
* **Service Principal From Key Vault** authentication type: Key Vault helps to safeguard cryptographic keys and secrets that cloud apps and services use. By using Key Vault, you can encrypt keys and secrets. You should create a service principal first, and then store the service principal inside Key Vault.  You can go through [Store service principal credentials in Azure Stack Hub Key Vault](https://docs.microsoft.com/azure-stack/user/azure-stack-key-vault-store-credentials) to follow detailed procedure to set service principal from key vault. 

* **Query**: Specify the query of Log Analytics. For more details please refer to [Log queries in Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/logs/log-query-overview)

    Sample query:

    ```
    [TableName]
    | where [TimestampColumn] >= @IntervalStart and [TimestampColumn] < @IntervalEnd
    | summarize [count_per_dimension]=count() by [Dimension]
    ```

    Besides, you can read [Tutorial: Write a valid query](tutorial/write-a-valid-query.md) for more specific examples.    

## <span id="sql">Azure SQL Database | SQL Server</span>

* **Connection String**: There are five Authentication types for Azure SQL Database | SQL Server, they are **Basic**, **Managed Identity**, **Azure SQL Connection String**, **Service Principal** and **Service Principal From KeyVault**.
    
    * **Basic**: Metrics Advisor accepts an [ADO.NET Style Connection String](/dotnet/framework/data/adonet/connection-string-syntax) for sql server data source.
    Here is an example of connection string: 
    
        ```
        Data Source=<Server>;Initial Catalog=<db-name>;User Id=<user-name>;Password=<password>
        ```
    
    * **Managed Identity** : Managed identities for Azure resources can authorize access to blob and queue data using Azure AD credentials from applications running in Azure virtual machines (VMs), function apps, virtual machine scale sets, and other services. By using managed identities for Azure resources together with Azure AD authentication, you can avoid storing credentials with your applications that run in the cloud. 
    To enable your managed entity, you can refer to following steps:
      1. Enabling a system-assigned managed identity is a one-click experience. In Auzre portal for your Metrics Advisor workspace, set the status as **on** in **RESOURCE MANAGEMENT > Identity**.
      2. In Azure portal for your data source, click **set admin** in **Settings > Active Directory admin**, this is to give MI access to specified users, and the suggested role type is: admin / viewer.
      3. Then you should create a contained user in database. First, start SQL Server Management Studio, in the **Connect to Server** dialog, Enter your **server name** in the Server name field. Then in the Authentication field, select **Active Directory - Universal with MFA support**. In the User name field, enter the name of the Azure AD account that you set as the server administrator, then click **Options**. In the Connect to database field, enter the name of the non-system database you want to configure. Then click **Connect**, and finally complete the sign-in process.
      4. The last step is to enable MI in Metrics Advisor. In the **Object Explorer**, expand the **Databases** folder. Right-click on a user database and click **New query**. In the query window, you should enter the following line, and click Execute in the toolbar:
    
          ``` SQL
          CREATE USER [MI Name] FROM EXTERNAL PROVIDER

          ALTER ROLE db_datareader ADD MEMBER [MI Name]
          ```
       
     The [MI Name] is the workspace name in Metrics Advisor. Also, you can learn more detail in this document: [Authorize with a managed identity](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-msi#enable-managed-identities-on-a-vm). 
        Here is an example of connection string: 

        ```
        Data Source=<Server>;Initial Catalog=<Database>
        ```
    
    * **Azure SQL Connection String**: A service principal is a concrete instance created from the application object and inherits certain properties from that application object. A service principal is created in each tenant where the application is used and references the globally unique app object. The service principal object defines what the app can actually do in the specific tenant, who can access the app, and what resources the app can access.
    You can go through [Application and service principal objects in Azure Active Directory](../../active-directory/develop/app-objects-and-service-principalsto.md) to know about Service Principal and create one. Also, your connection string could be found in Azure SQL Server resource in **Settings > Connection strings** section.
        Here is an example of connection string: 
        
        ```
        Data Source=<Server>;Initial Catalog=<Database>
        ```
  

    * **Service Principal**: A service principal is a concrete instance created from the application object and inherits certain properties from that application object. A service principal is created in each tenant where the application is used and references the globally unique app object. The service principal object defines what the app can actually do in the specific tenant, who can access the app, and what resources the app can access.
    
        First, you will need to create and register an Azure AD application and then authorize it to access an Azure Data Explorer database, see detail in [Create an AAD app registration in Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/provision-azure-ad-app) documentation.

        Then, you can go through [Manage Azure Data Explorer database permissions](https://docs.microsoft.com/azure/data-explorer/manage-database-permissions) to know about Service Principal and set service principals. 

        Also, you need to **create a credential entity** in Metric Advisor, so that you can choose that entity whe adding data feed for Service Principal authentication type. 
        Here is an example of connection string: 
        
        ```
        Data Source=<Server>;Initial Catalog=<Database>
        ```
  
    * **Service Principal From Key Vault**: Key Vault helps to safeguard cryptographic keys and secrets that cloud apps and services use. By using Key Vault, you can encrypt keys and secrets. You should create a service principal first, and then store the service principal inside Key Vault.  You can go through [Store service principal credentials in Azure Stack Hub Key Vault](https://docs.microsoft.com/azure-stack/user/azure-stack-key-vault-store-credentials) to follow detailed procedure to set service principal from key vault. Also, your connection string could be found in Azure SQL Server resource in **Settings > Connection strings** section.
        Here is an example of connection string: 
        
        ```
        Data Source=<Server>;Initial Catalog=<Database>
        ```

* **Query**: A SQL query to get and formulate data into multi-dimensional time series data. You can use `@IntervalStart` and `@IntervalEnd` in your query to help with getting expected metrics value in an interval. They should be formatted: `yyyy-MM-ddTHH:mm:ssZ`.


    Sample query:
    
    ```SQL
    SELECT [TimestampColumn], [DimensionColumn], [MetricColumn] FROM [TableName] WHERE [TimestampColumn] >= @IntervalStart and [TimestampColumn] < @IntervalEnd    
    ```
    
## <span id="table">Azure Table Storage</span>

* **Connection String**: Please create an SAS (shared access signature) URL and fill in here. The most straightforward way to generate a SAS URL is using the Azure Portal. By using the Azure portal, you can navigate graphically. To create an SAS URL via the Azure portal, first, navigate to the storage account you’d like to access under the **Settings section** then click **Shared access signature**. 
Check allowed services and allowed resource types checkboxes, then click the **Generate SAS and connection string** button at the bottom. Table service SAS URL is what you need to copy and fill in the text box in the Metrics Advisor.

![azure table generate sas](media/azure-table-generate-sas.png)

* **Table Name**: Specify a table to query against. This can be found in your Azure Storage Account instance. Click **Tables** in the **Table Service** section.

* **Query**: You can use `@IntervalStart` and `@IntervalEnd` in your query to help with getting expected metrics value in an interval. They should be formatted: `yyyy-MM-ddTHH:mm:ssZ`.

    Sample query:
    
    ``` mssql
    PartitionKey ge '@IntervalStart' and PartitionKey lt '@IntervalEnd'
    ```

    Besides, you can read [Tutorial: Write a valid query](tutorial/write-a-valid-query.md) for more specific examples.

## <span id="es">Elasticsearch</span>

* **Host**:Specify the master host of Elasticsearch Cluster.
* **Port**:Specify the master port of Elasticsearch Cluster.
* **Authorization Header**(optional):Specify the authorization header value of Elasticsearch Cluster. See more information in [Elastic Document](https://www.elastic.co/guide/en/elasticsearch/reference/current/http-clients.html).
* **Query**: Specify the query to get data for a single interval. You can use `@IntervalStart` and `@IntervalEnd` in your query to help with getting expected metrics value in an interval. They should be formatted: `yyyy-MM-ddTHH:mm:ssZ`.

    Sample query:
    
    ``` SQL
    SELECT [TimestampColumn], [DimensionColumn], [MetricColumn] FROM [TableName] WHERE [TimestampColumn] >= @IntervalStart and [TimestampColumn] < @IntervalEnd
    ```

    Besides, you can read [Tutorial: Write a valid query](tutorial/write-a-valid-query.md) for more specific examples.


## <span id="http">HTTP request</span>

* **Request URL**: A HTTP url which can return a JSON. 
    The following parameters are supported:
  * `%Y` is the year formatted as `yyyy`
  * `%m` is the month formatted as `MM`
  * `%d` is the day formatted as `dd`
  * `%h` is the hour formatted as `HH`
  * `%M` is the minute formatted as `mm`

    For example: `http://microsoft.com/ProjectA/%Y/%m/X_%Y-%m-%d-%h-%M`.
* **Request HTTP method**: Use GET or POST.
* **Request header**: Could add basic authentication. 
* **Request payload**: Only JSON payload is supported. Placeholder `@IntervalStart` is supported in the payload. The response should be in the following JSON format: [{"timestamp": "2018-01-01T00:00:00Z", "market":"en-us", "count":11, "revenue":1.23}, {"timestamp": "2018-01-01T00:00:00Z", "market":"zh-cn", "count":22, "revenue":4.56}].(e.g. when data of 2020-06-21T00:00:00Z is ingested, @IntervalStart = 2020-06-21T00:00:00.0000000+00:00)

    Sample query:
    
    ``` JSON
    {
       "[Timestamp]":"@IntervalStart"
    }
    ```

    Besides, you can read [Tutorial: Write a valid query](tutorial/write-a-valid-query.md) for more specific examples.

## <span id="influxdb">InfluxDB (InfluxQL)</span>

* **Connection String**: The connection string to access your InfluxDB.
* **Database**: The database to query against.
* **Query**: A query to get and formulate data into multi-dimensional time series data for ingestion.

    Sample query:

    ``` SQL
    SELECT [TimestampColumn], [DimensionColumn], [MetricColumn] FROM [TableName] WHERE [TimestampColumn] >= @IntervalStart and [TimestampColumn] < @IntervalEnd
    ```
    
Besides, you can read [Tutorial: Write a valid query](tutorial/write-a-valid-query.md) for more specific examples.    

* **User name**: This is optional for authentication. 
* **Password**: This is optional for authentication. 

## <span id="mongodb">MongoDB</span>

* **Connection String**: The connection string to access your MongoDB.
* **Database**: The database to query against.
* **Query**: A command to get and formulate data into multi-dimensional time series data for ingestion. We recommend the command is verified on [db.runCommand()](https://docs.mongodb.com/manual/reference/method/db.runCommand/index.html).

    Sample query:

    ``` MongoDB
    {"find": "[TableName]","filter": { [Timestamp]: { $gte: ISODate(@IntervalStart) , $lt: ISODate(@IntervalEnd) }},"singleBatch": true}
    ```
    

## <span id="mysql">MySQL</span>

* **Connection String**: The connection string to access your MySQL DB.
* **Query**: A query to get and formulate data into multi-dimensional time series data for ingestion.

    Sample query:

    ``` SQL
    SELECT [TimestampColumn], [DimensionColumn], [MetricColumn] FROM [TableName] WHERE [TimestampColumn] >= @IntervalStart and [TimestampColumn]< @IntervalEnd
    ```

    Besides, you can read [Tutorial: Write a valid query](tutorial/write-a-valid-query.md) for more specific examples.

## <span id="pgsql">PostgreSQL</span>

* **Connection String**: The connection string to access your PostgreSQL DB.
* **Query**: A query to get and formulate data into multi-dimensional time series data for ingestion.

    Sample query:

    ``` SQL
    SELECT [TimestampColumn], [DimensionColumn], [MetricColumn] FROM [TableName] WHERE [TimestampColumn] >= @IntervalStart and [TimestampColumn] < @IntervalEnd
    ```
    Besides, you can read [Tutorial: Write a valid query](tutorial/write-a-valid-query.md) for more specific examples.
    
## <span id="csv">Local files(CSV)</span>

> [!NOTE]
> This feature is only used for quick system evaluation focusing on anomaly detection. It only accepts static data from a local CSV and perform anomaly detection on single time series data. However, for full product experience analyzing on multi-dimensional metrics including real-time data ingestion, anomaly notification, root cause analysis, cross-metric incident analysis, please use other supported data sources.

**Requirements on data in CSV:**
1. Have at least one column, which represents as measures to be analyzed. For better and quicker user experience, we recommend you try a CSV file containing 2 columns : (1) Timestamp column (2) Metric Column. (Timestamp format : 2021-03-30T00:00:00Z, note that the 'seconds' part is best to be ':00Z') And the time granularity between every record should be the same.
2. Timestamp column is optional, if there's no timestamp, Metrics Advisor will use timestamp starting from today 00:00:00(UTC) and map each measure in the row at a one-hour interval. If there is timestamp column in CSV and you want to keep it, please make sure the data time period follow this rule [historical data processing window].
3. There is no re-ordering or gap-filling happening during data ingestion, please make sure your data in CSV is ordered by timestamp **ASC**.
 
## Next steps

* While waiting for your metric data to be ingested into the system, read about [how to manage data feed configurations](how-tos/manage-data-feeds.md).
* When your metric data is ingested, you can [Configure metrics and fine tune detecting configuration](how-tos/configure-metrics.md).
