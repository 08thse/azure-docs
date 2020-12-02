---
title: 'Register and scan an on-premises SQL server'
description: This tutorial describes how to scan on-prem SQL server using a self-hosted IR. 
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/18/2020
# Customer intent: As a data steward or catalog administrator, I need to understand how to scan data into the catalog.
---

# Register and scan an on-premises SQL server

This article outlines how to register a SQL server data source in Purview and set up a scan on it.

## Supported Capabilities

The SQL server on-premises data source supports the following functionality:

- **Full and incremental scans** to capture metadata and classification in an on-premises network or a SQL server installed on an Azure VM.

- **Lineage** between data assets for ADF copy/dataflow activities

SQL server on-premises data source supports:

- every version of SQL from SQL server 2019 back to SQL server 2000

- Authentication method: SQL authentication

## Prerequisites

- The features in this article require a Purview account created after September 15, 2020.

- Set up a [self-hosted integration runtime](manage-integration-runtimes.md) to scan the data source.

### Feature Flag

Registration and scanning of a SQL server on-premises data source is available behind a feature flag. Append the following to your URL: `?feature.ext.datasource={%22sqlServer%22:%22true%22}`. The full URL will look like `https://web.babylon.azure.com/?feature.ext.datasource={%22sqlServer%22:%22true%22}`.

## Register a SQL server data source

1. Navigate to your Purview catalog.

2. Select **Manage your data** tile on the home page.

   :::image type="content" source="media/register-scan-on-premises-sql-server/babylon-home-page.png" alt-text="Purview home page.":::

3. Under Sources and scanning in the left navigation, select **Integration runtimes**. Make sure a self-hosted integration runtime is set up. If it is not set up, follow the steps mentioned [here](manage-integration-runtimes.md) to create a self-hosted integration runtime for scanning on an on-premises or Azure VM that has access to your on-premises network.

4. Select **Data sources** under the Sources and scanning section. Select **New** to register a new data source. Select **SQL server**, then **Continue**.

   :::image type="content" source="media/register-scan-on-premises-sql-server/set-up-sql-data-source.png" alt-text="Set up the SQL data source.":::

5. Provide a friendly name and server endpoint and then select **Finish** to register the data source. If, for example, your SQL server FQDN is **foobar.database.windows.net**, then enter *foobar* as the server endpoint.

## Setting up authentication for a scan

SQL authentication is the only supported authentication method for SQL server on-premises.

The SQL identity must have access to the primary database. This location is where `sys.databases` is stored. The Purview scanner needs to enumerate `sys.databases` in order to find all the SQL DB instances in the server.

### Using an existing server administrator

If you plan to use an existing server admin (sa) user to scan your on-premises SQL server, ensure the following:

1. `sa` is not Windows authentication type.

2. The server level user you are planning to use must have server roles of public and sysadmin. You can verify this by navigating to SQL Server Management Studio (SSMS), connecting to the server, navigating to security, selecting the login you are planning to use, right-clicking **Properties** and then selecting **Server roles**.

   :::image type="content" source="media/register-scan-on-premises-sql-server/server-level-login.png" alt-text="Server level login.":::

3. The databases are mapped to a user that has at least db_datareader level access on each database.

   :::image type="content" source="media/register-scan-on-premises-sql-server/user-mapping-sa.png" alt-text="user mapping for sa.":::

### Creating a new login and user

If you would like to create a new login and user to be able to scan your SQL server, follow the steps below:

1. Navigate to SQL Server Management Studio (SSMS), connect to the server, navigate to security, right-click on login and create New login. Make sure to select SQL authentication.

   :::image type="content" source="media/register-scan-on-premises-sql-server/create-new-login-user.png" alt-text="Create new login and user.":::

2. Select Server roles on the left navigation and select both public and sysadmin.

3. Select User mapping on the left navigation and select all the databases in the map.

   :::image type="content" source="media/register-scan-on-premises-sql-server/user-mapping.png" alt-text="user mapping.":::

4. Click OK to save.

5. Navigate again to the user you created, by right clicking and selecting **Properties**. Enter a new password and confirm it. Select the 'Specify old password' and enter the old password. **It is required to change your password as soon as you create a new login.**

   :::image type="content" source="media/register-scan-on-premises-sql-server/change-password.png" alt-text="change password.":::

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## Next steps

- [Browse the Azure Purview Data catalog](how-to-browse-catalog.md)
- [Search the Azure Purview Data Catalog](how-to-search-catalog.md)
