---
title: How to share an Azure Managed Grafana Preview workspace
description: 'Azure Managed Grafana: learn how you can share access permissions and dashboards with your team and customers.' 
author: maud-lv 
ms.author: malev 
ms.service: managed-grafana 
ms.topic: how-to 
ms.date: 3/31/2022 
---

# How to share an Azure Managed Grafana Preview workspace

Dashboards are usually built to monitore and diagnose an application, or to report to management and customers. A dedicated support team may also build a Grafana monitoring solution for an external customer and create permissions to let this customer access their Managed Grafana dashboard. This article explains what permissions are supported and how to grant permissions to share dashboards with your internal team and external customers.

## Prerequisites

- An Azure account with an active subscription. [Create an account for free](https://azure.microsoft.com/free/dotnet).
- An Azure Managed Grafana workspace. If you don't have one yet, [create a workspace](/how-to-permissions.md).

## Grafana roles supported
Azure Managed Grafana supports the Admin, Viewer and Editor roles:

- The Admin role provides full control of the workspace including viewing, editing, and configuring data sources. 
- The Editor role provides read-write access to the Managed Grafana workspace.
- The Viewer role provides read-only access to dashboards in the workspace.

The Admin role is automatically assigned to the creator of a Grafana workspace. More details on Admin, Editor, and Viewer roles can be found at [Grafana organization roles](https://grafana.com/docs/grafana/latest/permissions/organization_roles/#compare-roles).

Grafana user roles are integrated within the Azure Active Directory roles and permissions, so that you can assign permissions from the Azure portal or the command line. This section explains how to assign users viewer or editor roles in Azure portal.

## Sign in to Azure

Sign in to the Azure portal at [https://portal.azure.com/](https://portal.azure.com/) with your Azure account.

## Open the Azure Managed Grafana Access control (IAM) tab

1. Open your Managed Grafana workspace
1. Select **Access control (IAM)** in the navigation menu.

## Assign an Admin, Viewer or Editor role to a user
1. Select **Add**, then **Add role assignment**
      :::image type="content" source="media/how-to-share-IAM.png" alt-text="Screenshot of Add role assignment in the Azure platform.":::

2. Select one of the Grafana roles to assign to a user or security group. The available roles are:

   - Grafana Admin
   - Grafana Editor
   - Grafana Viewer

    :::image type="content" source="media/how-to-share-role-assignment.png" alt-text="Screenshot of Add role assignment in the Azure platform.":::

> [NOTE!]
> Dashboard and data source level sharing will be done from within the Grafana UI, with support provided by Grafana Labs. Fore more details, refer to [Grafana permissions](https://grafana.com/docs/grafana/latest/permissions/).

## Next steps

> [!div class="nextstepaction"]
> [Configure permissions for Managed Grafana](./how-to-data-source-plugins-managed-identity.md).
> [Configure data source plugins for Azure Managed Grafana with Managed Identity](./how-to-data-source-plugins-managed-identity.md).
> [How to call Grafana APIs in your automation with Azure Managed Grafana Preview](./how-to-API-calls.md).