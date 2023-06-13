---
title: 'App Service authentication recommendations'
description: There are several different authentication solutions available for web apps or web APIs hosted on App Service. This article provides recommendations on which auth solution to use for specific scenarios such as custom authentication, incremental consent, or calling downstream resources as the signed-in user.  
author: rwike77
manager: CelesteDG
ms.author: ryanwi
ms.topic: conceptual
ms.date: 06/10/2023
---
# Authentication scenarios and recommendations

You can add authentication to your web app or web API running in Azure App Service to limit access to users in your organizatoin.  There are several different authentication solutions available.  This article describes which authentication solution to use for specific scenarios.

## Authentication solutions

- **Azure App Service built-in authentication** - Allows you to sign in users and access data by writing minimal or no code in your web app, RESTful API, or mobile back end. It’s built directly into the platform and doesn’t require any particular language, library, security expertise, or even any code to utilize.
- **Microsoft Authentication Library (MSAL)** - Enables developers to acquire security tokens from the Microsoft identity platform to authenticate users and access secured web APIs. Available in multiple supported platforms and frameworks, these are general purpose libraries that can be used in a variety of hosted environments. You can integrate with multiple sign-in providers. For example, Azure AD, Facebook, Google, Twitter.
- **Microsoft.Identity.Web** - A wrapper for MSAL.NET, it provides a set of ASP.NET Core libraries that simplifies adding authentication support to web apps and web APIs integrating with the Microsoft identity platform.  It provides a single-surface API convenience layer that ties together ASP.NET Core, its authentication middleware, and MSAL.NET. This library can be used in apps in a variety of hosted environments. You can integrate with multiple sign-in providers. For example, Azure AD, Facebook, Google, Twitter.

## Scenario recommendations

The following table lists each authentication solution and some important factors for when you would use it.

|Authentication method|When to use|
|--|--|
|Built-in App Service authentication |* You want less code to own and manage.<br>* Your app's language and SDKs don't provide user sign-in or authorization.<br>* You don't have the ability to modify your app code (for example, when migrating legacy apps).<br>* You need to handle authentication through configuration and not code.<br>* You need to sign in external or social users.|
|Microsoft Authentication Library (MSAL)|* You need a code solution in one of several different languages<br>* You need to add custom authorization logic.<br>* You need to support incremental consent.<br>* You also need to call more than one downstream API as the user.<br>* You need information about the signed-in user in your code.<br>* You need to sign in external or social users.<br>* Your app need to handle the access token expiring without making the user sign in again.|
|Microsoft.Identity.Web |* You have an ASP.NET Core app. <br>* You need single sign-on support in your IDE during local development.<br>* You need to add custom authorization logic.<br>* You need to support incremental consent.<br>* You also need to call more than one downstream API as the user.<br>* You need information about the signed-in user in your code.<br>* You need to sign in external or social users.<br>* Your app need to handle the access token expiring without making the user sign in again.|

The following table lists authentication scenarios and the authentication solution(s) you would use.

|Scenario |App Service built-in auth| Microsoft Authentication Library | Microsoft.Identity.Web |
|:--|:--:|:--:|:--:|
| Need a fast and simple way to limit access to users in your organization? | ✅ | ❌ | ❌ |
| Unable to modify the application code (app migration scenario)? | ✅ | ❌ | ❌ |
| Your app's language and libraries support user sign-in/authorization?  | ❌ | ✅ | ✅ |
| Even if you can use a code solution, would you rather *not* use libraries? Don't want the maintenance burden?  | ✅ | ❌ | ❌ |
| Does your web app need to provide incremental consent?  | ❌ | ✅ | ✅ |
| Your app need to handle the access token expiring without making the user sign in again (use a refresh token)? | ❌ | ✅ | ✅ |
| Need custom authorization logic or info about the signed-in user? | ❌ | ✅ | ✅ |
| Need to sign in users from external or social identity providers?  | ✅ | ✅ | ✅ |
| You have an ASP.NET Core app? | ✅ | ❌ | ✅ |
| You have a single page app or static web app? | ✅ | ✅ | ✅ |
| Want Visual Studio integration? | ❌ | ✅ | ✅ |
| Need single sign-on support in your IDE during local development? | ❌ | ✅ | ✅ |

## Get started

To get started with built-in App Service authentication, read:
- [Enable App Service built-in authentication](scenario-secure-app-authentication-app-service.md)

To get started with Microsoft Authentication Library (MSAL), read:
- [Sign in users to a web app](/azure/active-directory/develop/scenario-web-app-sign-user-overview)
- [Only allow authenticated user to access a web API](/azure/active-directory/develop/scenario-protected-web-api-overview)
- [Sign in users to a single-page application (SPA)](/azure/active-directory/develop/scenario-spa-overview)

To get started with Microsoft.Identity.Web, read:

- [Sign in users to a web app](/azure/active-directory/develop/scenario-web-app-sign-user-overview?tabs=aspnetcore)
- [Only allow authenticated user to access a web API](/azure/active-directory/develop/scenario-protected-web-api-overview)
- [Sign in users to a Blazor Server app](/azure/active-directory/develop/tutorial-blazor-server)

## Next steps

Learn more about [App Service built-in authentication and authorization](overview-authentication-authorization.md)
[Add built-in authentication to your web app running on App Service](scenario-secure-app-authentication-app-service.md)