---
title: Migrate Azure SQL Database to availability zone support 
description: Learn how to migrate your Azure SQL Database to availability zone support.
author: rsetlem
ms.service: sql
ms.topic: conceptual
ms.date: 05/08/2023
ms.author: anaharris 
ms.reviewer: anaharris
ms.custom: references_regions, subject-reliability
---

# Migrate Azure SQL Database to availability zone support
 
This guide describes how to migrate [Azure SQL Database](/azure/azure-sql/)  from non-availability zone support to availability support.

Enabling zone redundancy for Azure SQL Database guarantees high availability as the database utilizes Azure Availability Zones to replicate data across multiple physical locations within an Azure region. By selecting zone redundancy, you can make your databases and elastic pools resilient to a larger set of failures, such as catastrophic datacenter outages, without any changes of the application logic.  

## Prerequisites

Before migrating to availability zone support, refer to the following table to ensure that your Azure SQL Database is in a supported service tier and deployment model. Make sure that your tier and model is offered in a [region that supports availability zones].

| Service tier | Deployment model | Zone redundancy availability |
|-----|-----|-----|
| Premium | Single database or elastic pool. | [All regions that support availability zones](availability-zones-service-support.md#azure-regions-with-availability-zone-support)|
| Business Critical | Single database or elastic pool. | [All regions that support availability zones](availability-zones-service-support.md#azure-regions-with-availability-zone-support) |
| General Purpose | Single database or elastic pool.  | [Selected regions that support availability zones](/azure/azure-sql/database/high-availability-sla?view=azuresql&tabs=azure-powershell#general-purpose-service-tier-zone-redundant-availability)|
| Hyperscale | Single database. |[All regions that support availability zones](availability-zones-service-support.md#azure-regions-with-availability-zone-support) |


## Downtime requirements

Migration for Premium, Business Critical, and General Purpose service tier is an online operation with a brief disconnect towards the end to finish the migration process. If you have implemented [retry logic for standard transient errors](/azure/azure-sql/database/troubleshoot-common-connectivity-issues?view=azuresql#retry-logic-for-transient-errors), you won't notice the failover. 

For Hyperscale service tier, zone redundancy support can only be specified during database creation and can't be modified once the resource is provisioned. If you wish to move to availability zone support, you'll need to transfer the data by means of database copy, point-in-time restore, or geo-replica. If the target database is in a different region than the source or if the database backup storage redundancy for the target differs from the source database, then downtime will be proportional to the size of the data operation.  

## Option 1: Migration (Premium, Business Critical, and General Purpose)

Follow the steps below to perform migration for a single database or an elastic pool.

### Migrate a single database

# [Azure portal](#tab/portal)

1. Go to the [Azure portal](https://portal.azure.com) to find your database. Search for and select **SQL databases**.

1. Select the database that you want to migrate.

1. Under **Settings** select **Compute + Storage**. 

1. Select **Yes** for **Would you like to make this database zone redundant?**.

1. Select **Apply**.

1. Wait to receive an operation completion notice in **Notifications** in the top menu of the Azure portal. 

1. To verify that zone redundancy is enabled, select **Overview** and then select **Properties**.

1. Under the **Availability** section, confirm that zone redundancy is set to **Enabled**.   

# [PowerShell](#tab/powershell)

Open PowerShell as Administrator and run the following command (replace the placeholders in "<>" with your resource names):

```powershell
   Set-AzSqlDatabase -ResourceGroupName “<Resource_Group_Name>” -ServerName “<Server_Name>” -DatabaseName “<Database_Name>” -ZoneRedundant 
```

# [Azure CLI](#tab/cli)


Use Azure CLI to run the following command (replace the placeholders in "<>" with your resource names):

```azurecli
    az sql db update --resource-group “<Resource_Group_Name>” --server “<Server_Name>” --name “<Database_Name>” --zone-redundant 
```

# [ARM](#tab/arm)

To enable zone redundancy, see [Databases - Create Or Update in ARM](/rest/api/sql/2022-05-01-preview/databases/create-or-update?tabs=HTTP). 

---

### Migrate an elastic pool


>[!IMPORTANT]
>Enabling zone redundancy support for elastic pools will make all databases within the pool zone redundant.


# [Azure portal](#tab/portal)

1. Go to the [Azure portal](https://portal.azure.com) to find and select the elastic pool that you want to migrate.

1. Select **Settings**, and then select **Configure**. 

1. Select **Yes** for **Would you like to make this elastic pool zone redundant?**.

1. Select **Save**.

1. Wait to receive an operation completion notice in **Notifications** in the top menu of the Azure portal. 

1. To verify that zone redundancy is enabled, select **Configure** and then select **Pool settings**.

1. The zone redundant option should be set to **Yes**.   


# [PowerShell](#tab/powershell)


Open PowerShell as Administrator and run the following command (replace the placeholders in "<>" with your resource names):

```powershell
    Set-AzSqlElasticPool -ResourceGroupName “<Resource_Group_Name>” -ServerName “<Server_Name>” -ElasticPoolName “<Elastic_Pool_Name>” -ZoneRedundant 
```


# [Azure CLI](#tab/cli)

Use Azure CLI to run the following command (replace the placeholders in "<>" with your resource names):

```azurecli
    az sql elastic-pool update --resource-group “<Resource_Group_Name>” --server “<Server_Name>” --name “<Elastic_Pool_Name>” --zone-redundant 
```


# [ARM](#tab/arm)

To enable zone redundancy, see [Elastic Pools - Create Or Update in ARM](/rest/api/sql/2022-05-01-preview/elastic-pools/create-or-update?tabs=HTTP). 

---

## Option 2: Redeployment (Hyperscale)

For the Hyperscale service tier, zone redundancy support can only be specified during database creation and can't be modified once the database is provisioned. If you wish to gain zone redundancy support, you'll need to perform a data transfer from your existing Hyperscale service tier single database. To perform the transfer and enable the zone redundancy option, a clone must be created using database copy, point-in-time restore, or geo-replica.  

### Redeployment considerations

- There are two modes of redeployment (Online and Offline): 

    - **Database copy and point-in-time restore methods (Offline mode)** create a transactionally consistent database at a certain point in time. As a result, any data changes performed after the copy or restore operation have been initiated won't be available on the copied or restored database.
    
    - **Geo replica method (Online mode)** is a mode of redeployment wherein any data changes from source will be synchronized to target.  

- Connection string for the application must be updated to point to the zone redundant database. 

### Redeploy a single database

# [Database copy](#tab/database)

To create a database copy and enable zone redundancy, use one of the following methods:

- Copy with the Azure portal. Follow the instructions in [copy a transactionally consistent copy of a database in Azure SQL Database](/azure/azure-sql/database/database-copy?tabs=azure-powershell&view=azuresql#copy-using-the-azure-portal) and enable zone redundancy under **Compute + Storage**

- Copy with PowerShell. Use the `-ZoneRedundant` switch to [copy a transactionally consistent copy of a database in Azure SQL Database](/azure/azure-sql/database/database-copy?tabs=azure-powershell&view=azuresql#copy-using-powershell-or-the-azure-cli).

- Copy with Azure CLI. Use the `--zone-redundant` switch to [copy a transactionally consistent copy of a database in Azure SQL Database](/azure/azure-sql/database/database-copy?tabs=azure-cli&view=azuresql#copy-using-powershell-or-the-azure-cli).
 

# [Point-in-time restore](#tab/point)

To create a point-in-time database restore and enable zone redundancy, use one of the following methods:

- Restore with the Azure portal. Follow the instructions in [Point-in-time restore](/azure/azure-sql/database/recovery-using-backups?view=azuresql&tabs=azure-portal#point-in-time-restore) and enable zone redundancy under **Compute + Storage**

- Restore with PowerShell. Use the `-ZoneRedundant` switch to [create a point-in-time restore](/azure/azure-sql/database/recovery-using-backups?view=azuresql&tabs=powershell#point-in-time-restore).

- Restore with Azure CLI. Use the `--zone-redundant` switch to [create a point-in-time restore](/azure/azure-sql/database/recovery-using-backups?view=azuresql&tabs=azure-cli#point-in-time-restore).

# [Geo-replica](#tab/geo)

To create a geo-replica of the database:

1. Use one of the following methods:

    - Create with the Azure portal. Follow the instructions in [Configure active geo-replication and failover (Azure SQL Database)](/azure/azure-sql/database/active-geo-replication-configure-portal?view=azuresql&tabs=portal) and enable zone redundancy under **Compute + Storage**

    - Create with Azure CLI. Use the `--zone-redundant` switch to [configure active geo-replication and failover (Azure SQL Database)](/azure/azure-sql/database/active-geo-replication-configure-portal?view=azuresql&tabs=azure-cli).

1. The replica will be seeded, and the time taken for seeding the data will depend upon size of source database. You can monitor the status of seeding in the Azure portal or by running the following TSQL queries on the replica database:

    ```tsql
        select * from sys.dm_geo_replication_link_status 
        select * from sys.dm_operation_status 
    ```
1. Once the database seeding is finished, perform a planned (no data loss) failover to make the zone redundant target database as primary.  

1. Since the server name for the zone redundant database will be different, connection strings for the application must be updated.  

1. To clean up, consider removing the original non-zone redundant database from the geo replica relationship. You can choose to delete it.  

## Next steps

> [!div class="nextstepaction"]
> [Azure services and regions that support availability zones](availability-zones-service-support.md)