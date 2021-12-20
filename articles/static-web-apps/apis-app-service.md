---
title: API support in Azure Static Web Apps with Azure App Service
description: Learn how to use Azure App Service with Azure Static Web Apps
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic:  conceptual
ms.date: 12/17/2021
ms.author: cshoe
---

# API support in Azure Static Web Apps with Azure App Service

[!INCLUDE [APIs overview](../../includes/static-web-apps-apis-overview.md)]

## Overview

[Azure App Service](../app-service/overview.md) is a managed platform for hosting web applications that execute code on servers. It supports many runtimes and frameworks including Node.js, ASP.NET Core, PHP, Java, and Python.

When you link your Azure App Service instance to your static web app, any requests to your static web app with a route that starts with `/api/` are proxied to the same route on the Azure App Service app.

By default, when an App Service app is linked to a static web app, the App Service app only accepts requests that are proxied through the linked static web app. An Azure App Service app can be linked to a single static web app at a time.

All Azure App Service hosting plans are available for use with Azure Static Web Apps.

> [!NOTE]
> Currently, you cannot link an App Service app to a Static Web Apps [pre-production environment](review-publish-pull-requests.md).

## Link an Azure App Service app

To link an Azure App Service app as the API backend for a static web app, follow these steps:

1. In the Azure portal, navigate to the static web app.

1. Select **APIs** from the navigation menu.

1. In *API Source*, select **Azure App Service**.

1. In *Subscription*, select the subscription containing the Azure App Service app you want to link.

1. In *Resource*, select the Azure App Service app.

1. Select **Link**.

When the linking process is complete, requests to routes beginning with `/api/` are proxied to the linked App Service app.

### Manage access to Azure App Service

Your App Service app is configured with an identity provider named `Azure Static Web Apps (Linked)` that permits only traffic that is proxied through the static web app. To make your App Service app accessible to other applications, update its authentication configuration to add another identity provider or change the security settings to allow unauthenticated access.

## Re-link an Azure App Service app

The re-link action ensures the connection between the static web app and the Azure App Service app is correctly configured. It's useful if you've made changes to your App Service app and it's no longer accessible from the linked static web app.

To re-link an Azure App Service app as the API backend for a static web app, follow these steps:

1. In the Azure portal, navigate to the static web app.

1. Select **APIs** from the navigation menu.

    Confirm that the static web app is linked to the correct Azure App Service app.

1. Select **Re-link**.

## Unlink an Azure App Service app

To unlink an Azure App Service app from a static web app, follow these steps:

1. In the Azure portal, navigate to the static web app.

1. Select **APIs** from the navigation menu.

1. Select **Unlink**.

When the unlinking process is complete, requests to routes beginning with `/api/` are no longer proxied to your App Service app.

> [!NOTE]
> To prevent accidentally exposing your App Service app to anonymous traffic, the identity provider created by the linking process is not automatically deleted. You can delete the identity provider named *Azure Static Web Apps (Linked)* from the App Service app's authentication settings.

## Next steps

> [!div class="nextstepaction"]
> [Add an API](add-api.md)
