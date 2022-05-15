---
title: Track your Microsoft Sentinel migration with a workbook | Microsoft Docs
description: Learn how to track your migration with a workbook, how to customize and manage the workbook, and how to use the workbook tabs to manage data connectors, analytics, incidents, playbooks, automation rules, UEBA, and data.
author: limwainstein
ms.author: lwainstein
ms.topic: how-to
ms.date: 05/03/2022
ms.custom: ignite-fall-2021
---

# Track your Microsoft Sentinel migration with a workbook

You can track migration process using generic tools such as Microsoft Project, Microsoft Excel, Teams, Azure DevOps and more. These tools aren’t specific to SIEM migration tracking. To help you with tracking, we provide a dedicated migration tracker workbook in Microsoft Sentinel. 

The workbook helps you to:
- Visualize migration progress
- Deploy and track data sources
- Deploy and monitor analytics rules and incidents
- Deploy and utilize workbooks
- Deploy and perform automation
- Deploy and customize user and entity behavioral analytics (UEBA)

This article describes how to track your migration with a workbook, how to customize and manage the workbook, and how to use the workbook tabs to manage data connectors, analytics, incidents, playbooks, automation rules, UEBA, and data. Learn more about how to use [Azure Monitor workbooks](monitor-your-data).

## Customize the workbook

Add your migration project planning to the workbook:
- Start with a watchlist template to define an action, action category, priority, status, blocked status, update comments, and completion date. 
- After Microsoft Sentinel deploys the watchlist, it builds the workbook to customize tracking columns.

:::image type="content" source="media/migration-track/migration-track-customize.png" alt-text="Screenshot of the Microsoft Sentinel Deployment Tracker, showing an example of a customized deployment." lightbox="media/migration-track/migration-track-customize.png":::

## Update workbook actions

As the deployment progresses, you can update existing actions or add new actions as you identify new use cases or set new requirements.

:::image type="content" source="media/migration-track/migration-track-update.png" alt-text="Screenshot of the Microsoft Sentinel Edit watchlist items screen, showing an example list of watchlist items." lightbox="media/migration-track/migration-track-update.png":::

## View deployment progress

In the bottom half of each tab, you can view a snapshot of the deployment progress that displays the deployment status and includes quantitative data for each tab. Information includes:
- Tables reporting data
- Number of tables reporting data
- Number of enabled rules vs. undeployed rules
- Recommended workbooks deployed
- Total number of workbooks deployed
- Total number of playbooks deployed

## Manage data connectors

To monitor deployed resources and deploy new connectors, select **Data Connectors > Monitor**. The **Monitor** view lists:
- Current ingestion trends
- Tables reporting data
- How much data each table is reporting
- Endpoints reporting with MMA
- Endpoints reporting with AMA
- Endpoints reporting with both agents
- Data connector health (changes and failures)

:::image type="content" source="media/migration-track/migration-track-data-connectors.png" alt-text="Screenshot of the Microsoft Sentinel Data Connectors tab Monitor view." lightbox="media/migration-track/migration-track-data-connectors.png":::

To configure a connector, select **Data Connectors > Configure**, select the button with the name of the connector you want to configure, and configure the connector in the connector status screen that opens. If you cannot a connector you need, open the connector gallery or solution gallery to deploy what is needed with a click of a button. 

:::image type="content" source="media/migration-track/migration-track-configure-data-connectors.png" alt-text="Screenshot of the Microsoft Sentinel Deployment Tracker Data Connectors tab Configure view and a specific connector view." lightbox="media/migration-track/migration-track-configure-data-connectors.png":::

## Manage analytics and incidents

Select **Analytics** to view all deployed rule templates and lists, including which rules are currently in use as well as how often the rules generate incidents. 

:::image type="content" source="media/migration-track/migration-track-analytics.png" alt-text="Screenshot of the Microsoft Sentinel Deployment Tracker Analytics tab." lightbox="media/migration-track/migration-track-analytics.png":::

If you need more coverage, select **Review MITRE coverage** below the table and define which areas receive more coverage and which rules are deployed, at any stage of the migration project.

:::image type="content" source="media/migration-track/migration-track-mitre.png" alt-text="Screenshot of the Microsoft Sentinel Deployment Tracker Review MITRE Coverage view." lightbox="media/migration-track/migration-track-mitre.png":::

Once you have deployed the desired analytic rules and configured the Defender connector to ingest the alerts, you can monitor incident creation and frequency at the bottom half of the tab. This area displays metrics regarding alert generation by product, title, and classification, to indicate the health of the SOC and which alerts require the most attention. If alerts are generating too much volume, return to the **Analytics** tab to modify the logic.

:::image type="content" source="media/migration-track/migration-track-analytics-monitor.png" alt-text="Screenshot of the Microsoft Sentinel Deployment Tracker Analytics tab monitoring area." lightbox="media/migration-track/migration-track-analytics-monitor.png":::

## Manage workbooks

To visualize information regarding the data ingestion and detections that Microsoft Sentinel performs, select **Workbook**. Similar to the **Data Connectors** tab, you can view monitoring and configuration information. Select **Monitor** to view a list of all workbooks in the environment and how many are deployed. When you select and view the relevant workbook directly within the **Deployment and Migration** workbook.

:::image type="content" source="media/migration-track/migration-track-workbook.png" alt-text="Screenshot of the Microsoft Sentinel Deployment Tracker Workbook tab Monitor view." lightbox="media/migration-track/migration-track-workbook.png":::

If you have not yet deployed workbooks, select **Configure** to view a list of commonly used and recommended workbooks. If a workbook is not listed, select **Go to Workbook Gallery** or **Go to Content Hub** to select the workbook. 

:::image type="content" source="media/migration-track/migration-track-view-workbooks.png" alt-text="Screenshot of the Microsoft Sentinel Deployment Tracker Workbook tab workbook view." lightbox="media/migration-track/migration-track-view-workbooks.png":::

## Manage playbooks and automation rules

Select **Automation** to view deployed playbooks, and to see which playbooks are currently connected to an automation rule. If automation rules exist, the workbook highlights the following information regarding the automation rule:
- Name
- Status
- Action or actions of the rule
- The last date the rule was modified and the user that modified the rule
- The date the rule was created of the user that created the rule

If you would like to deploy additional automation, a button can be added to open the **Automation** tab, allowing you to view, deploy, and test automation, within the current section of the workbook.

:::image type="content" source="media/migration-track/migration-track-automation.png" alt-text="Screenshot of the Microsoft Sentinel Deployment Tracker Automation tab playbooks view." lightbox="media/migration-track/migration-track-automation.png":::

## Manage UEBA

Select **UEBA**, to enable UEBA, customize the entity timelines for entity pages, and view which entity related tables are populated with data. To enable UEBA, select **Enable UEBA** above the table. After you Enable UEBA, you can monitor and ensure that Microsoft Sentinel is generating UEBA data. 

:::image type="content" source="media/migration-track/migration-track-ueba.png" alt-text="Screenshot of the Microsoft Sentinel Deployment Tracker UEBA tab." lightbox="media/migration-track/migration-track-ueba.png":::

## Manage data

Select **Data Management** to monitor and control the lifecycle of the workspace data. You can view information regarding:

- Tables configured for basic log ingestion
- Tables configured for analytics tier ingestion
- Tables configured to be archived
- Tables on the default workspace retention

To modify the existing retention policy for tables, select the relevant table to edit the following information:
- Current retention in the workspace
- Current retention in the archive
- Total number of days the data will live in the environment

To edit the retention, edit the **TotalRetention** value to set a new total number of days that the data should exist within the environment. To calculate the archive retention policy, subtract the number of days that the data will exist in the workspace from the total number of days the data will exist within the environment. If you need to adjust the workspace retention, the change does not impact tables that include configured archives and data is not lost. If you edit the workspace retention and the total number of days that the data is kept within the environment does not change, Microsoft Sentinel adjusts the archive retention to compensate the change.

:::image type="content" source="media/migration-track/migration-track-data-management.png" alt-text="Screenshot of the Microsoft Sentinel Deployment Tracker Data Management tab." lightbox="media/migration-track/migration-track-data-management":::