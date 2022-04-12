---
title: Connect to SQL databases
description: Automate workflows for SQL databases on premises or in the cloud with Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 03/30/2022
tags: connectors
---

# Connect to a SQL database from Azure Logic Apps

This article shows how to access your SQL database with the SQL Server connector in Azure Logic Apps. You can then create automated workflows that are triggered by events in your SQL database or other systems and manage your SQL data and resources.

For example, you can use actions that get, insert, and delete data along with running SQL queries and stored procedures. You can create workflow that checks for new records in a non-SQL database, does some processing work, creates new records in your SQL database using the results, and sends email alerts about the new records in your SQL database.

 The SQL Server connector supports the following SQL editions:

* [SQL Server](/sql/sql-server/sql-server-technical-documentation)
* [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md)
* [Azure SQL Managed Instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)

If you're new to Azure Logic Apps, review the following documentation:

* [What is Azure Logic Apps](../logic-apps/logic-apps-overview.md)
* [Quickstart: Create your first logic app workflow](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## Prerequisites

* An Azure account and subscription. If you don't have a subscription, [sign up for a free Azure account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* [SQL Server database](/sql/relational-databases/databases/create-a-database), [Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md), or [Azure SQL Managed Instance](../azure-sql/managed-instance/instance-create-quickstart.md).

  The SQL connector requires that your tables contain data so that SQL connector operations can return results when called. For example, if you use Azure SQL Database, you can use the included sample databases to try the SQL connector operations.

* The information required to create a SQL database connection, such as your SQL server and database names. If you're using Windows Authentication or SQL Server Authentication to authenticate access, you also need your user name and password. You can usually find this information in the connection string.

  > [!NOTE]
  >
  > If you use a SQL Server connection string that you copied directly from the Azure portal, 
  > you have to manually add your password to the connection string.

  * For a SQL database in Azure, the connection string has the following format: 

    `Server=tcp:{your-server-name}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`

    1. To find this string in the [Azure portal](https://portal.azure.com), open your database.

    1. On the database menu, under **Properties**, select **Connection strings**.

  * For an on-premises SQL server, the connection string has the following format:

    `Server={your-server-address};Database={your-database-name};User Id={your-user-name};Password={your-password};`

* The logic app workflow where you want to access your SQL database. If you want to start your workflow with a SQL trigger, you have to start with a blank workflow.

* To connect to an on-premises SQL server, the following extra requirements apply based on whether you have a Consumption logic app workflow, either in multi-tenant Azure Logic Apps or an [integration service environment (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), or if you have a Standard logic app workflow in [single-tenant Azure Logic Apps](../logic-apps/single-tenant-overview-compare.md).

  * Consumption logic app workflow

    * In multi-tenant Azure Logic Apps, you need the [on-premises data gateway](../logic-apps/logic-apps-gateway-install.md) installed on a local computer and a [data gateway resource that's already created in Azure](../logic-apps/logic-apps-gateway-connection.md).

    * In an ISE, when you have non-Windows or SQL Server Authentication connections, you don't need the on-premises data gateway and can use the ISE-versioned SQL Server connector. For Windows Authentication and SQL Server Authentication, you still have to use the [on-premises data gateway](../logic-apps/logic-apps-gateway-install.md) and a [data gateway resource that's already created in Azure](../logic-apps/logic-apps-gateway-connection.md). Also, the ISE-versioned SQL Server connector doesn't support Windows authentication, so you have to use the non-ISE SQL Server connector.

  * Standard logic app workflow

    In single-tenant Azure Logic Apps, you can use the built-in SQL Server connector, which requires a connection string. If you want to use the managed SQL Server connector, you need follow the same requirements as a Consumption logic app workflow in multi-tenant Azure Logic Apps.

## Connector technical reference

This connector is available for logic app workflows in multi-tenant Azure Logic Apps, ISEs, and single-tenant Azure Logic Apps.

* For Consumption logic app workflows in multi-tenant Azure Logic Apps, this connector is available only as a managed connector.

* For Consumption logic app workflows in an ISE, this connector is available as a managed connector and as an ISE connector that's designed to run in an ISE.

* For Standard logic app workflows in single-tenant Azure Logic Apps, this connector is available as a managed connector and as a built-in connector that's designed to run in the same process as the single-tenant Azure Logic Apps runtime. However, the built-in version differs in the following ways:

  * The built-in SQL Server connector has no triggers.

  * The built-in SQL Server connector has only one operation: **Execute Query**

For the managed SQL Server connector technical information, such as trigger and action operations, limits, and known issues, review the [SQL Server connector's reference page](/connectors/sql/), which is generated from the Swagger description.

<a name="create-connection"></a>

## Connect to your database

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

After you provide this information, continue with these steps:

* [Connect to cloud-based Azure SQL Database or Managed Instance](#connect-azure-sql-db)
* [Connect to on-premises SQL Server](#connect-sql-server)

<a name="connect-azure-sql-db"></a>

### Connect to Azure SQL Database or Managed Instance

To access an Azure SQL Managed Instance without using the on-premises data gateway or integration service environment, you have to [set up the public endpoint on the Azure SQL Managed Instance](../azure-sql/managed-instance/public-endpoint-configure.md). The public endpoint uses port 3342, so make sure that you specify this port number when you create the connection from your logic app.

The first time that you add either a [SQL trigger](#add-sql-trigger) or [SQL action](#add-sql-action), and you haven't previously created a connection to your database, you're prompted to complete these steps:

1. For **Connection name**, provide a name to use for your connection.

1. For **Authentication type**, select the authentication that's required and enabled on your database in Azure SQL Database or Azure SQL Managed Instance:

   | Authentication | Description |
   |----------------|-------------|
   | **Service principal (Azure AD application)** | - Available only for the managed SQL Server connector. <br><br>- Requires an Azure AD application and service principal. For more information, see [Create an Azure AD application and service principal that can access resources using the Azure portal](../active-directory/develop/howto-create-service-principal-portal.md). |
   | **Logic Apps Managed Identity** | - Available only for the managed SQL Server connector and ISE SQL Server connector. <br><br>- Requires the following items: <br><br>--- A valid managed identity that has [access to your database](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql.md) <br><br>--- **SQL DB Contributor** role access to the SQL Server resource <br><br>--- **Contributor** access to the resource group that includes the SQL Server resource. <br><br>For more information, see [SQL - Server-Level Roles](/sql/relational-databases/security/authentication-access/server-level-roles). |
   | [**Azure AD Integrated**](../azure-sql/database/authentication-aad-overview.md) | - Available only for the managed SQL Server connector and ISE SQL Server connector. <br><br>- Requires a valid identity in Azure Active Directory (Azure AD) that has access to your database. <br><br>For more information, see these topics: <br><br>- [Azure SQL Security Overview - Authentication](../azure-sql/database/security-overview.md#authentication) <br>- [Authorize database access to Azure SQL - Authentication and authorization](../azure-sql/database/logins-create-manage.md#authentication-and-authorization) <br>- [Azure SQL - Azure AD Integrated authentication](../azure-sql/database/authentication-aad-overview.md) |
   | [**SQL Server Authentication**](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication) | - Available only for the managed SQL Server connector and ISE SQL Server connector. <br><br>- Requires the following items: <br><br>--- A data gateway resource that's previously created in Azure for your connection, regardless whether your logic app is in multi-tenant Azure Logic Apps or an ISE. <br><br>--- A valid user name and strong password that are created and stored in your SQL Server database. For more information, see the following topics: <br><br>- [Azure SQL Security Overview - Authentication](../azure-sql/database/security-overview.md#authentication) <br>- [Authorize database access to Azure SQL - Authentication and authorization](../azure-sql/database/logins-create-manage.md#authentication-and-authorization) |

   This connection and authentication information box looks similar to the following example, which selects **Azure AD Integrated**:

   * Consumption logic app workflows

     ![Screenshot showing the Azure portal, workflow designer, and "SQL Server" cloud connection information with selected authentication type for Consumption.](./media/connectors-create-api-sqlazure/select-azure-ad-sql-cloud-consumption.png)

   * Standard logic app workflows

     ![Screenshot showing the Azure portal, workflow designer, and "SQL Server" cloud connection information with selected authentication type for Standard.](./media/connectors-create-api-sqlazure/select-azure-ad-sql-cloud-standard.png)

1. After you select **Azure AD Integrated**, select **Sign in**. Based on whether you use Azure SQL Database or Azure SQL Managed Instance, select your user credentials for authentication.

1. Select these values for your database:

   | Property | Required | Description |
   |----------|----------|-------------|
   | **Server name** | Yes | The address for your SQL server, for example, **Fabrikam-Azure-SQL.database.windows.net** |
   | **Database name** | Yes | The name for your SQL database, for example, **Fabrikam-Azure-SQL-DB** |
   | **Table name** | Yes | The table that you want to use, for example, **SalesLT.Customer** |
   ||||

   > [!TIP]
   > To provide your database and table information, you have these options:
   > 
   > * Find this information in your database's connection string. For example, in the Azure portal, find and open your database. On the database menu, select either **Connection strings** or **Properties**, where you can find the following string:
   >
   >   `Server=tcp:{your-server-address}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`
   >
   > * By default, tables in system databases are filtered out, so they might not automatically appear when you select a system database. As an alternative, you can manually enter the table name after you select **Enter custom value** from the database list.
   >

   This database information box looks similar to the following example:

   * Consumption logic app workflows

     ![Screenshot showing SQL cloud database cloud information with sample values for Consumption.](./media/connectors-create-api-sqlazure/azure-sql-database-information-consumption.png)

   * Standard logic app workflows

     ![Screenshot showing SQL cloud database information with sample values for Standard.](./media/connectors-create-api-sqlazure/azure-sql-database-information-standard.png)

1. Now, continue with the steps that you haven't completed yet in either [Add a SQL trigger](#add-sql-trigger) or [Add a SQL action](#add-sql-action).

<a name="connect-sql-server"></a>

### Connect to on-premises SQL Server

The first time that you add either a [SQL trigger](#add-sql-trigger) or [SQL action](#add-sql-action), and you haven't previously created a connection to your database, you're prompted to complete these steps:

1. For connections to your on-premises SQL server that require the on-premises data gateway, make sure that you've [completed these prerequisites](#multi-tenant-or-ise).

   Otherwise, your data gateway resource won't appear in the **Connection Gateway** list when you create your connection.

1. For **Authentication Type**, select the authentication that's required and enabled on your SQL Server:

   | Authentication | Description |
   |----------------|-------------|
   | [**SQL Server Authentication**](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication) | - Available only for the managed SQL Server connector and ISE SQL Server connector. <br><br>- Requires the following items: <br><br>--- A data gateway resource that's previously created in Azure for your connection, regardless whether your logic app is in multi-tenant Azure Logic Apps or an ISE. <br><br>--- A valid user name and strong password that are created and stored in your SQL Server. <br><br>For more information, see [SQL Server Authentication](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication). |
   | [**Windows Authentication**](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-windows-authentication) | - Available only for the managed SQL Server connector. <br><br>- Requires the following items: <br><br>--- A data gateway resource that's previously created in Azure for your connection, regardless whether your logic app is in multi-tenant Azure Logic Apps or an ISE. <br><br>--- A valid Windows user name and password to confirm your identity through your Windows account. <br><br>For more information, see [Windows Authentication](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-windows-authentication). |
   |||

1. Select or provide the following values for your SQL database:

   | Property | Required | Description |
   |----------|----------|-------------|
   | **SQL server name** | Yes | The address for your SQL server, for example, `Fabrikam-Azure-SQL.database.windows.net` |
   | **SQL database name** | Yes | The name for your SQL Server database, for example, `Fabrikam-Azure-SQL-DB` |
   | **Username** | Yes | Your user name for the SQL server and database |
   | **Password** | Yes | Your password for the SQL server and database |
   | **Subscription** |  Yes, for Windows authentication | The Azure subscription for the data gateway resource that you previously created in Azure |
   | **Connection Gateway** | Yes, for Windows authentication | The name for the data gateway resource that you previously created in Azure <br><br><br><br>**Tip**: If your gateway doesn't appear in the list, check that you correctly [set up your gateway](../logic-apps/logic-apps-gateway-connection.md). |
   |||

   > [!TIP]
   > You can find this information in your database's connection string:
   > 
   > * `Server={your-server-address}`
   > * `Database={your-database-name}`
   > * `User ID={your-user-name}`
   > * `Password={your-password}`

   This connection and authentication information box looks similar to the following example, which selects **Windows Authentication**:

   * Consumption logic app workflows

     ![Screenshot showing the Azure portal, workflow designer, and "SQL Server" on-premises connection information with selected authentication for Consumption.](./media/connectors-create-api-sqlazure/select-windows-authentication-consumption.png)

   * Consumption logic app workflows

     ![Screenshot showing the Azure portal, workflow designer, and "SQL Server" on-premises connection information with selected authentication for Standard.](./media/connectors-create-api-sqlazure/select-windows-authentication-standard.png)

1. When you're ready, select **Create**.

1. Now, continue with the steps that you haven't completed yet in either [Add a SQL trigger](#add-sql-trigger) or [Add a SQL action](#add-sql-action).

<a name="add-sql-trigger"></a>

## Add a SQL trigger

1. In the [Azure portal](https://portal.azure.com) or in Visual Studio, create a blank logic app, which opens the workflow designer. This example continues with the Azure portal.

1. On the designer, in the search box, enter `sql server`. From the triggers list, select the SQL trigger that you want. This example uses the **When an item is created** trigger.

   ![Select "When an item is created" trigger](./media/connectors-create-api-sqlazure/select-sql-server-trigger.png)

1. If you're connecting to your SQL database for the first time, you're prompted to [create your SQL database connection now](#create-connection). After you create this connection, you can continue with the next step.

1. In the trigger, specify the interval and frequency for how often the trigger checks the table.

1. To add other available properties for this trigger, open the **Add new parameter** list.

   This trigger returns only one row from the selected table, and nothing else. To perform other tasks, continue by adding either a [SQL connector action](#add-sql-action) or [another action](../connectors/apis-list.md) that performs the next task that you want in your logic app workflow.

   For example, to view the data in this row, you can add other actions that create a file that includes the fields from the returned row, and then send email alerts. To learn about other available actions for this connector, see the [connector's reference page](/connectors/sql/).

1. On the designer toolbar, select **Save**.

   Although this step automatically enables and publishes your logic app live in Azure, the only action that your logic app currently takes is to check your database based on your specified interval and frequency.

<a name="trigger-recurrence-shift-drift"></a>

## Trigger recurrence shift and drift (daylight saving time)

Recurring connection-based triggers where you need to create a connection first, such as the managed SQL Server trigger, differ from built-in triggers that run natively in Azure Logic Apps, such as the [Recurrence trigger](../connectors/connectors-native-recurrence.md). In recurring connection-based triggers, the recurrence schedule isn't the only driver that controls execution, and the time zone only determines the initial start time. Subsequent runs depend on the recurrence schedule, the last trigger execution, *and* other factors that might cause run times to drift or produce unexpected behavior. For example, unexpected behavior can include failure to maintain the specified schedule when daylight saving time (DST) starts and ends.

To make sure that the recurrence time doesn't shift when DST takes effect, manually adjust the recurrence. That way, your workflow continues to run at the expected or specified start time. Otherwise, the start time shifts one hour forward when DST starts and one hour backward when DST ends. For more information, see [Recurrence for connection-based triggers](../connectors/apis-list.md#recurrence-for-connection-based-triggers).

<a name="add-sql-action"></a>

## Add a SQL action

In this example, the logic app starts with the [Recurrence trigger](../connectors/connectors-native-recurrence.md), and calls an action that gets a row from a SQL database.

1. In the [Azure portal](https://portal.azure.com) or in Visual Studio, open your logic app in the workflow designer. This example continues the Azure portal.

1. Under the trigger or action where you want to add the SQL action, select **New step**.

   ![Add an action to your logic app](./media/connectors-create-api-sqlazure/select-new-step-logic-app.png)

   Or, to add an action between existing steps, move your mouse over the connecting arrow. Select the plus sign (**+**) that appears, and then select **Add an action**.

1. Under **Choose an action**, in the search box, enter `sql server`. From the actions list, select the SQL action that you want. This example uses the **Get row** action, which gets a single record.

   ![Select SQL "Get row" action](./media/connectors-create-api-sqlazure/select-sql-get-row-action.png)

1. If you're connecting to your SQL database for the first time, you're prompted to [create your SQL database connection now](#create-connection). After you create this connection, you can continue with the next step.

1. Select the **Table name**, which is `SalesLT.Customer` in this example. Enter the **Row ID** for the record that you want.

   ![Select table name and specify row ID](./media/connectors-create-api-sqlazure/specify-table-row-id.png)

   This action returns only one row from the selected table, nothing else. So, to view the data in this row, you might add other actions that create a file that includes the fields from the returned row, and store that file in a cloud storage account. To learn about other available actions for this connector, see the [connector's reference page](/connectors/sql/).

1. When you're done, on the designer toolbar, select **Save**.

   This step automatically enables and publishes your logic app live in Azure.

<a name="handle-bulk-data"></a>

## Handle bulk data

Sometimes, you have to work with result sets so large that the connector doesn't return all the results at the same time, or you want better control over the size and structure for your result sets. Here's some ways that you can handle such large result sets:

* To help you manage results as smaller sets, turn on *pagination*. For more information, see [Get bulk data, records, and items by using pagination](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md). For more information, see [SQL Pagination for bulk data transfer with Logic Apps](https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx).

* Create a [*stored procedure*](/sql/relational-databases/stored-procedures/stored-procedures-database-engine) that organizes the results the way that you want. The SQL connector provides many backend features that you can access by using Azure Logic Apps so that you can more easily automate business tasks that work with SQL database tables.

  When getting or inserting multiple rows, your logic app can iterate through these rows by using an [*until loop*](../logic-apps/logic-apps-control-flow-loops.md#until-loop) within these [limits](../logic-apps/logic-apps-limits-and-config.md). However, when your logic app has to work with record sets so large, for example, thousands or millions of rows, that you want to minimize the costs resulting from calls to the database.

  To organize the results in the way that you want, you can create a stored procedure that runs in your SQL instance and uses the **SELECT - ORDER BY** statement. This solution gives you more control over the size and structure of your results. Your logic app calls the stored procedure by using the SQL Server connector's **Execute stored procedure** action. For more information, see [SELECT - ORDER BY Clause](/sql/t-sql/queries/select-order-by-clause-transact-sql).

  > [!NOTE]
  > The SQL connector has a stored procedure timeout limit that's [less than 2-minutes](/connectors/sql/#known-issues-and-limitations). 
  > Some stored procedures might take longer than this limit to complete, causing a `504 Timeout` error. You can work around this problem 
  > by using a SQL completion trigger, native SQL pass-through query, a state table, and server-side jobs.
  > 
  > For this task, you can use the [Azure Elastic Job Agent](../azure-sql/database/elastic-jobs-overview.md) 
  > for [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md). For 
  > [SQL Server on premises](/sql/sql-server/sql-server-technical-documentation) 
  > and [Azure SQL Managed Instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md), 
  > you can use the [SQL Server Agent](/sql/ssms/agent/sql-server-agent). To learn more, see 
  > [Handle long-running stored procedure timeouts in the SQL connector for Azure Logic Apps](../logic-apps/handle-long-running-stored-procedures-sql-connector.md).

### Handle dynamic bulk data

When you call a stored procedure by using the SQL Server connector, the returned output is sometimes dynamic. In this scenario, follow these steps:

1. In the [Azure portal](https://portal.azure.com), open your logic app in the workflow designer.

1. View the output format by performing a test run. Copy and save your sample output.

1. In the designer, under the action where you call the stored procedure, select **New step**.

1. Under **Choose an action**, find and select the [**Parse JSON**](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action) action.

1. In the **Parse JSON** action, select **Use sample payload to generate schema**.

1. In the **Enter or paste a sample JSON payload** box, paste your sample output, and select **Done**.

   > [!NOTE]
   > If you get an error that Logic Apps can't generate a schema, check that your sample output's syntax is correctly formatted. 
   > If you still can't generate the schema, in the **Schema** box, manually enter the schema.

1. On the designer toolbar, select **Save**.

1. To reference the JSON content properties, click inside the edit boxes where you want to reference those properties so that the dynamic content list appears. In the list, under the [**Parse JSON**](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action) heading, select the data tokens for the JSON content properties that you want.

## Troubleshoot problems

<a name="connection-problems"></a>

### Connection problems

Connection problems can commonly happen, so to troubleshoot and resolve these kinds of issues, review [Solving connectivity errors to SQL Server](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server). Here are some examples:

* `A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections.`

* `(provider: Named Pipes Provider, error: 40 - Could not open a connection to SQL Server) (Microsoft SQL Server, Error: 53)`

* `(provider: TCP Provider, error: 0 - No such host is known.) (Microsoft SQL Server, Error: 11001)`

## Next steps

* Learn about other [connectors for Azure Logic Apps](../connectors/apis-list.md)
