---
title: "Tutorial: Assign Azure Static Web Apps roles with Microsoft Graph"
description: Learn to use a role assignment function to assign custom roles based on Active Directory group membership.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic:  tutorial
ms.date: 09/28/2021
ms.author: cshoe

---

# Tutorial: Assign custom roles with a function and Microsoft Graph

This article demonstrates how to use a function to query a user's Active Directory group membership with Microsoft Graph and assign custom Static Web Apps roles to the user.

In this tutorial, you learn to:

- Deploy a static web app
- Create an Azure Active Directory app registration
- Set up custom authentication with Azure Active Directory
- Configure a [role assignment function](authentication-authorization.md?tabs=function#role-management) that queries the user's Active Directory group membership and returns a list of custom roles

## Prerequisites

- **Active Azure account:** If you don't have one, you can [create an account for free](https://azure.microsoft.com/free/).

## Create a GitHub repository

1. Navigate to the following location to create a new repository:
    - [https://github.com/anthonychu/20210928-swa-roles-function/generate](https://github.com/login?return_to=/anthonychu/20210928-swa-roles-function/generate)

1. Name your repository **my-custom-roles-app**.

1. Select **Create repository from template**.

## Deploy the static web app to Azure

1. In a new browser window, navigate to the [Azure portal](https://portal.azure.com) and sign in with your Azure account.

1. Click **Create** in the top right corner.

1. Search for **Static Web Apps**.

1. Select **Static Web Apps**.

1. Select **Create**.

1. Select your _Azure subscription_.

1. Next to _Resource Group_, select the **Create new** link.

1. Enter **my-custom-roles-app-group** in the textbox.

1. Under to _Static Web App details_, enter **my-custom-roles-app** in the textbox.

1. Under _Hosting plan_, select **Standard**. Customizing roles using a function requires the Stadard plan.

1. Under _Azure Functions and staging details_, select a region closest to you.

1. Under _Deployment details_, select **GitHub**.

1. Select the **Sign-in with GitHub** button and authenticate with GitHub.

1. Select your preferred _Organization_ name.

1. Select **my-custom-roles-app** from the _Repository_ drop-down.

1. Select **main** from the _Branch_ drop-down.

1. In the _Build Details_ section, add configuration details for this app.
    1. Select **Custom** from the _Build Presets_ dropdown.
    1. Type **frontend** in the _App location_ box.
    1. Type **api** in the _API location_ box.
    1. Ensure the _Output location_ box is empty.

1. Select **Review + create**. Then select **Create** to create the static web app and initiate the first deployment.

1. Select **Go to resource** to open your new static web app.

1. In the overview section, locate and note your application's **URL**. You'll need this URL to set up Active Directory authentication and test the app.

## Create an Azure Active Directory application

  > [!NOTE]
  > You must have sufficient permissions to create an Azure Active Directory application.

1. In the Azure portal, search for and navigate to *Azure Active Directory*.

1. In the menu bar, select **App registrations**.

1. Select **+ New registration** to open the *Register an application* page

1. Enter a name for the application. For example, **MyStaticWebApp**.

1. For *Supported account types*, select **Accounts in this organizational directory only**.

1. For *Redirect URIs*, select **Web** and enter the Azure Active Directory login [authentication callback](authentication-custom.md#authentication-callbacks) of your static web app. For example, `<YOUR_SITE_URL>/.auth/login/aad/callback`.

    Replace `<YOUR_SITE_URL>` with the URL of your static web app.

1. Select **Register**.

1. After the app registration is created, note the **Application (client) ID** and **Directory (tenant) ID** in the *Essentials* section. You will need this value to configure Active Directory authentication in your static web app.

### Enable ID tokens

1. Select *API permissions* in the menu bar.

1. In the *Implicit grant and hybrid flows* section, select **ID tokens (used for implicit and hybrid flows)**.

1. Select **Save**.

### Create a client secret

1. Select *Certificates & secrets* in the menu bar.

1. In the *Client secrets* section, select **+ New client secret**.

1. Enter a name for the client secret. For example, **MyStaticWebApp**.

1. Leave the default of 6 months for the *Expires* field.

    > [!NOTE]
    > You must rotate the secret before the expiration date.

1. Select **Add**.

1. Note the **Value** of the client secret you created. You will need this value to configure Active Directory authentication in your static web app.

## Configure Active Directory authentication

1. In a browser, the GitHub repository containing the static web app you deployed. Navigate to the app's configuration file at *frontend/staticwebapp.config.json*. It contains the following section:

    ```json
    "auth": {
      "rolesSource": "/api/GetRoles",
      "identityProviders": {
        "azureActiveDirectory": {
          "userDetailsClaim": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name",
          "registration": {
            "openIdIssuer": "https://login.microsoftonline.com/e8ec9559-fad7-4d90-ab05-cb168a4ff3cc",
            "clientIdSettingName": "AAD_CLIENT_ID",
            "clientSecretSettingName": "AAD_CLIENT_SECRET"
          },
          "login": {
            "loginParameters": [
                "resource=https://graph.microsoft.com"
            ]
          }
        }
      }
    },
    ```

1. Select the **Edit** button to update the file.

1. Change the *openIdIssuer* value to `https://login.microsoftonline.com/<YOUR_AAD_TENANT>`. Replace `<YOUR_AAD_TENANT>` with the directory (tenant) ID of your Azure Active Directory.

1. Select **Commit directly to the main branch** and select **Commit changes**.

1. A GitHub Actions run will be triggered to update the static web app.

1. Navigate to your static web app in the Azure portal.

1. Select **Configuration** in the menu bar.

1. In the *Application settings* section, add the following settings:

    | Name | Value |
    |------|-------|
    | `AAD_CLIENT_ID` | *Your Active Directory application (client) ID* |
    | `AAD_CLIENT_SECRET` | *Your Active Directory application secret value* |

1. Select **Save**.

## Test the custom roles

1. In your GitHub repository, navigate to the *GetRoles* function located at *api/GetRoles/index.js*. Near the top, there is a `roleGroupMappings` object that maps static web app custom role names to Azure Active Directory groups.

1. Click the *Edit* button.

1. Update the object with group IDs from your Azure Active Directory. For instance, if you have groups with IDs `6b0b2fff-53e9-4cff-914f-dd97a13bfbd6` and `b6059db5-9cef-4b27-9434-bb793aa31805`, you would update the object to:

    ```js
    const roleGroupMappings = {
        'admin': '6b0b2fff-53e9-4cff-914f-dd97a13bfbd6',
        'reader': 'b6059db5-9cef-4b27-9434-bb793aa31805'
    };
    ```

    The *GetRoles* function will be called whenever a user is successfully authenticated with Azure Active Directory. The function uses the user's access token to query their group membership from Microsoft Graph. If they user is a member a matching group ID in the `roleGroupMappings` object, the user will be granted the corresponding custom role.

1. Select **Commit directly to the main branch** and select **Commit changes**.

1. A GitHub Actions run will be triggered to update the static web app.

1. When the deployment is complete, you can test the app by navigating to the app's URL.

1. Log in to your static web app using Azure Active Directory.

1. When you are logged in, you should see a list of custom roles displayed if you are a member of Active Directory groups in the `roleGroupMappings` object.

## Clean up resources

Clean up the resources you deployed by deleting the resource group.

1. From the Azure portal, select **Resource group** from the left menu.
2. Enter the resource group name in the **Filter by name** field.
3. Select the resource group name you used in this tutorial.
4. Select **Delete resource group** from the top menu.

## Next steps

> [!div class="nextstepaction"]
> [Authentication and authorization](./authentication-authorization.md)
