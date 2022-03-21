---
title: 'How to call Grafana APIs in your automation: Azure Managed Grafana Preview'
description: Learn how to call Grafana APIs in your automation with Azure Active Directory (Azure AD) and an Azure service principal
author: maud-lv 
ms.author: malev 
ms.service: managed-grafana 
ms.topic: how-to 
ms.date: 3/31/2022 
---

# How to call Grafana APIs in your automation within Azure Managed Grafana Preview

In this article, you'll learn how to call Grafana APIs within Managed Grafana using a service principal.

## Prerequisites

- An Azure account with an active subscription. [Create an account for free](https://azure.microsoft.com/free/dotnet).
- An Azure Managed Grafana workspace. If you don't have one yet, [create a workspace](/how-to-permissions.md).

## Sign in to Azure

Sign in to the Azure portal at [https://portal.azure.com/](https://portal.azure.com/) with your Azure account.

## Assign roles to the service principal of your application and of your Managed Grafana workspace 

1. Start by [Creating an Azure AD application and service principal that can access resources](../active-directory/develop/howto-create-service-principal-portal.md). This guide will take you through creating an application and assigning a role to its service principal. For simplicity, use an application located in the same Azure Active Directory (Azure AD) tenant as your Grafana instance.
1. Assign the role of your choice to the service principal for your Grafana resource. Refer to [How to share a Managed Grafana workspace](how-to-share-grafana-workspace.md) to learn how to grant access to a Grafana instance. Instead of selecting a user, select **Service principal**.

## Get an access token
To access Grafana APIs, you first need to get an access token. Here's an example showing how you can call Azure AD to retrieve a token:

```
curl -X POST -H 'Content-Type: application/x-www-form-urlencoded' \
-d 'grant_type=client_credentials&client_id=<client-id>&client_secret=<application-secret>&resource=ce34e7e5-485f-4d76-964f-b3d2b16d1e4f' \
https://login.microsoftonline.com/<tenant-id>/oauth2/token
```

Replace `<tenant-id>` with your own Azure AD tenant ID, replace `<client-id>` with your client ID and `<application-secret>` with the application secret of the application you want to share.

Here's an example of response:

```
{
  "token_type": "Bearer",
  "expires_in": "599",
  "ext_expires_in": "599",
  "expires_on": "1575500555",
  "not_before": "1575499766",
  "resource": "ce34...1e4f",
  "access_token": "eyJ0eXAiOiJ......AARUQ"
}
```

## Call the Grafana API

You can now call the Grafana API using the access token retrieved in the previous step as the Authorization header. For example:

```
curl -X GET \
-H 'Authorization: Bearer <access-token>' \
https://<grafana-url>/api/user
```
Replace `<access-token>` with the access token retrieved in the previous step and replace `<grafana-url>` with the URL of your Grafana instance (for example *test.scus.azgrafana.io*).
