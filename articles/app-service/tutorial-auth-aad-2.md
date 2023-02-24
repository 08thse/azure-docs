---
title: 'Tutorial: Authenticate users E2E' 
description: Learn how to use App Service authentication and authorization to secure your App Service apps end-to-end, including access to remote APIs.
keywords: app service, azure app service, authN, authZ, secure, security, multi-tiered, azure active directory, azure ad
ms.devlang: csharp
ms.topic: tutorial
ms.date: 02/13/2023
ms.custom: "devx-track-csharp, seodec18, devx-track-azurecli"
zone_pivot_groups: app-service-platform-windows-linux
# Requires non-internal subscription - internal subscriptons doesn't provide permission to correctly configure AAD apps
---

# Tutorial: Authenticate and authorize users end-to-end in Azure App Service

::: zone pivot="platform-windows"  

[Azure App Service](overview.md) provides a highly scalable, self-patching web hosting service. In addition, App Service has built-in support for [user authentication and authorization](overview-authentication-authorization.md). This tutorial shows how to secure your apps with App Service authentication and authorization. It uses an Express.js with views front end as an example. App Service authentication and authorization support all language runtimes, and you can learn how to apply it to your preferred language by following the tutorial.

::: zone-end

::: zone pivot="platform-linux"

[Azure App Service](overview.md) provides a highly scalable, self-patching web hosting service using the Linux operating system. In addition, App Service has built-in support for [user authentication and authorization](overview-authentication-authorization.md). This tutorial shows how to secure your apps with App Service authentication and authorization. It uses an Express.js with views front end front end as an example. App Service authentication and authorization support all language runtimes, and you can learn how to apply it to your preferred language by following the tutorial.

::: zone-end

Here's a more comprehensive list of things you learn in the tutorial:

> [!div class="checklist"]
> * Enable built-in authentication and authorization
> * Secure apps against unauthenticated requests
> * Use Azure Active Directory as the identity provider
> * Access a remote app on behalf of the signed-in user
> * Secure service-to-service calls with token authentication
> * Use access tokens from server code
> * Use access tokens from client (browser) code

You can follow the steps in this tutorial on macOS, Linux, Windows.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## Prerequisites

- [Git](https://git-scm.com/)
- [Node.js (LTS)](https://nodejs.org/download/)
- [Visual Studio Code](https://code.visualstudio.com/) to view and deploy the applications
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](~/articles/reusable-content/azure-cli/azure-cli-prepare-your-environment-no-header.md)]

## Authentication provided by Azure App Service

The authentication in this procedure is provided at the hosting platform layer by Azure App Service. There's no equivalent emulator. You must deploy the frontend and backend app and configuration authentication for each in order to use the authentication. 

:::image type="content" source="./media/tutorial-auth-aad/front-end-app-service-to-back-end-app-service-authentication.png" alt-text="Conceptual diagram show the authentication flow from the web user to the front-end app to the back-end app.":::

The frontend app is configured to be allowed to securely use the backend API. The frontend application provides a Microsoft sign-in for the user, then allows the user to get their _fake_ profile from the backend. 

This authentication from the frontend to the backend requires changes to the frontend application. The frontend passes the authenticated user access token, injected by App Service, `x-ms-token-aad-access-token`, to the backend as the bearer token. 

> [!TIP]
> After completing this scenario, continue to the next procedure to learn how to connect to Azure services as an authenticated user. Common scenarios for this includes accessing Azure Storage or a database as the user. 

## Create local app

In this step, use the Cloud Shell (Bash) available in the Azure portal to view and deploy the frontend and backend apps. The Cloud Shell, bash version, provides the Azure CLI and the zip CLI needed to  

### Clone the sample application 

1. In the [Azure Cloud Shell](https://shell.azure.com), run the following command to clone the sample repository.

    ```bash
    git clone https://github.com/Azure-Samples/js-e2e-web-app-easy-auth-app-to-app
    ```
    
1. Optionally, view the local frontend app in Visual Studio Code.

    ```bash
    cd frontend && code .
    ```

    ```javascript
    // Frontend - get access token from injected header
    let accessToken = req.headers['x-ms-token-aad-access-token'];
    console.log(`/get-profile:accessToken= ${accessToken}`);
    if (!accessToken) {
        return res.render(`${__dirname}/views/profile`, { error: 'Client: No access token found' });
    }
    ```

1. Optionally, view the local backend app in Visual Studio Code.

    ```bash
    cd ../backend && code .
    ```

    ```javascript
    // Backend - get remote profile
    const bearerToken =
    req.headers['Authorization'] || req.headers['authorization'];
    console.log(`bearerToken: ${bearerToken}`);
    
    const accessToken = bearerToken.split(' ')[1];
    console.log(`accessToken: ${accessToken}`);
    
    function validAccessToken(accessToken) {
        // access token validation removed for brevity
        return true;
    }
    
    // headers, bearerToken, and env returned for debugging only
    if (accessToken && validAccessToken(accessToken)) {
        return res.status(200).json({
            route: '/profile success',
            profile: {
                displayName: 'John Doe',
            },
            headers: req.headers, //
            bearerToken,
            env: process.env,
            error: null,
        });
    } else {
        return res.status(200).json({
            route: '/profile failure - empty or invalid accessToken',
            profile: null,
            headers: req.headers, //
            bearerToken,
            env: process.env,
            error: 'empty or invalid accessToken',
        });
    }
    ```

1. Return to the root of the sample repo.

    ```bash
    cd ..
    ```

## Create resource group and app plan

1. Create a resource group to manage related resources. 

    ```azurecli-interactive
    az group create --name myAuthResourceGroup --location "West Europe"
    ```

::: zone pivot="platform-windows"  

1. Create an App Service plan to manage to web apps.
    
    ```azurecli-interactive
    az appservice plan create --name myAuthAppServicePlan --resource-group myAuthResourceGroup --sku FREE --location "West Europe"
    ```

::: zone-end

::: zone pivot="platform-linux"

1. Create an App Service plan to manage to web apps.
    
    ```azurecli-interactive
    az appservice plan create --name myAuthAppServicePlan --resource-group myAuthResourceGroup --sku FREE --is-linux --location "West Europe"
    ```

::: zone-end

## Deploy apps to Azure

In this step, use the Cloud Shell from the Azure portal to create App Service web apps and deploy the two App Service apps.

::: zone pivot="platform-windows"  

In the Cloud Shell, edit and run the following commands to create two Windows web apps. Replace _\<front-end-app-name>_ and _\<back-end-app-name>_ with two globally unique app names (valid characters are `a-z`, `0-9`, and `-`). For more information on each command, see [Host a RESTful API with CORS in Azure App Service](app-service-web-tutorial-rest-api.md).

```azurecli-interactive
az webapp up --resource-group myAuthResourceGroup --name <FRONTEND-APP-NAME> --os-type Windows --runtime "NODE:18-lts" --sku B1 --plan myAuthAppServicePlan --location "West Europe"
cd ../backend
az webapp up --resource-group myAuthResourceGroup --name <BACKEND-APP-NAME> --os-type Windows --runtime "NODE:18-lts" --sku B1 --plan myAuthAppServicePlan --location "West Europe"
```

::: zone-end

::: zone pivot="platform-linux"

In the Cloud Shell, run the following commands to create two web apps. Replace _\<front-end-app-name>_ and _\<back-end-app-name>_ with two globally unique app names (valid characters are `a-z`, `0-9`, and `-`). 

```azurecli-interactive
cd frontend
az webapp up --resource-group myAuthResourceGroup --name <FRONTEND-APP-NAME> --os-type Linux --runtime NODE:18LTS --sku B1 --plan myAuthAppServicePlan --location "West Europe"
cd ../backend
az webapp up --resource-group myAuthResourceGroup --name <BACKEND-APP-NAME> --os-type Linux --runtime NODE:18LTS --sku B1 --plan myAuthAppServicePlan --location "West Europe"
```

::: zone-end

> [!NOTE]
> Save the URLs of the Git remotes for your front-end app and back-end app, which are shown in the output from `az webapp up`.
>

## Configure authentication

In this step, you enable authentication and authorization for the two apps. You use Azure Active Directory as the identity provider. 

You also configure the front-end app to: 

- Grant the front-end app access to the back-end app
- Configure App Service to return a usable token
- Use the token in your code.

For more information, see [Configure Azure Active Directory authentication for your App Services application](configure-authentication-provider-aad.md).


### Enable authentication and authorization for back-end app

1. In the [Azure portal](https://portal.azure.com) menu, select **Resource groups** or search for and select *Resource groups* from any page.

1. In **Resource groups**, find and select your resource group. In **Overview**, select your back-end app.

1. In your back-end app's left menu, select **Authentication**, and then select **Add identity provider**.

1. In the **Add an identity provider** page, select **Microsoft** as the **Identity provider** to sign in Microsoft and Azure AD identities.

1. Accept the default settings and select **Add**.

    :::image type="content" source="./media/tutorial-auth-aad/configure-auth-back-end.png" alt-text="Screenshot of the back-end app's left menu showing Authentication/Authorization selected and settings selected in the right menu.":::

1. The **Authentication** page opens. Copy the **Client ID** of the Azure AD application to a notepad. You need this value later.

    :::image type="content" source="./media/tutorial-auth-aad/get-application-id-back-end.png" alt-text="Screenshot of the Azure Active Directory Settings window showing the Azure AD App, and the Azure AD Applications window showing the Client ID to copy.":::

If you stop here, you have a self-contained app that's already secured by the App Service authentication and authorization. The remaining sections show you how to secure a multi-app solution by "flowing" the authenticated user from the front end to the back end. 

### Enable authentication and authorization for front-end app

1. In the [Azure portal](https://portal.azure.com) menu, select **Resource groups** or search for and select *Resource groups* from any page.

1. In **Resource groups**, find and select your resource group. In **Overview**, select your back-end app's management page.

    :::image type="content" source="./media/tutorial-auth-aad/portal-navigate-back-end.png" alt-text="Screenshot of the Resource groups window, showing the Overview for an example resource group and a back-end app's management page selected.":::

1. In your back-end app's left menu, select **Authentication**, and then select **Add identity provider**.

1. In the **Add an identity provider** page, select **Microsoft** as the **Identity provider** to sign in Microsoft and Azure AD identities.

1. Accept the default settings and select **Add**.

    :::image type="content" source="./media/tutorial-auth-aad/configure-auth-back-end.png" alt-text="Screenshot of the back-end app's left menu showing Authentication/Authorization selected and settings selected in the right menu.":::

1. The **Authentication** page opens. Copy the **Client ID** of the Azure AD application to a notepad. You need this value later.

    :::image type="content" source="./media/tutorial-auth-aad/get-application-id-back-end.png" alt-text="Screenshot of the Azure Active Directory Settings window showing the Azure AD App, and the Azure AD Applications window showing the Client ID to copy.":::


### Grant front-end app access to back end

Now that you've enabled authentication and authorization to both of your apps, each of them is backed by an AD application. To complete the authentication, you need to do three things:

- Grant the frontend app access to the back-end app
- Configure App Service to return a usable token
- Use the token in your code.

> [!TIP]
> If you run into errors and reconfigure your app's authentication/authorization settings, the tokens in the token store may not be regenerated from the new settings. To make sure your tokens are regenerated, you need to sign out and sign back in to your app. An easy way to do it is to use your browser in private mode, and close and reopen the browser in private mode after changing the settings in your apps.

In this step, you **grant the front-end app access to the backend app** on the user's behalf. (Technically, you give the front end's _AD application_ the permissions to access the back end's _AD application_ on the user's behalf.)

1. In the **Authentication** page for the front-end app, select your front-end app name under **Identity provider**. This app registration was automatically generated for you. Select **API permissions** in the left menu.

1. Select **Add a permission**, then select **My APIs** > **\<back-end-app-name>**.

1. In the **Request API permissions** page for the back-end app, select **Delegated permissions** and **user_impersonation**, then select **Add permissions**.

    :::image type="content" source="./media/tutorial-auth-aad/select-permission-front-end.png" alt-text="Screenshot of the Request API permissions page showing Delegated permissions, user_impersonation, and the Add permission button selected.":::

### Configure App Service to return a usable access token

The front-end app now has the required permissions to access the back-end app as the signed-in user. In this step, you configure App Service authentication and authorization to give you a usable access token for accessing the back end. For this step, you need the back end's client ID, which you copied from [Enable authentication and authorization for back-end app](#enable-authentication-and-authorization-for-back-end-app).

In the Cloud Shell, run the following commands on the front-end app to add the `scope` parameter to the authentication setting `identityProviders.azureActiveDirectory.login.loginParameters`. Replace *\<front-end-app-name>* and *\<back-end-client-id>*.

```azurecli-interactive
authSettings=$(az webapp auth show -g myAuthResourceGroup -n <front-end-app-name>)
authSettings=$(echo "$authSettings" | jq '.properties' | jq '.identityProviders.azureActiveDirectory.login += {"loginParameters":["scope=openid profile email offline_access api://<back-end-client-id>/user_impersonation"]}')
az webapp auth set --resource-group myAuthResourceGroup --name <front-end-app-name> --body "$authSettings"
```

The commands effectively add a `loginParameters` property with additional custom scopes. Here's an explanation of the requested scopes:

- `openid`, `profile`, and `email` are requested by App Service by default already. For information, see [OpenID Connect Scopes](../active-directory/develop/v2-permissions-and-consent.md#openid-connect-scopes).
- `api://<back-end-client-id>/user_impersonation` is an exposed API in your back-end app registration. It's the scope that gives you a JWT token that includes the back end app as a [token audience](https://wikipedia.org/wiki/JSON_Web_Token). 
- [offline_access](../active-directory/develop/v2-permissions-and-consent.md#offline_access) is included here for convenience (in case you want to [refresh tokens](#when-access-tokens-expire)).

> [!TIP]
> - To view the `api://<back-end-client-id>/user_impersonation` scope in the Azure portal, go to the **Authentication** page for the back-end app, click the link under **Identity provider**, then click **Expose an API** in the left menu.
> - To configure the required scopes using a web interface instead, see the Microsoft steps at [Refresh auth tokens](configure-authentication-oauth-tokens.md#refresh-auth-tokens).
> - Some scopes require admin or user consent. This requirement causes the consent request page to be displayed when a user signs into the front-end app in the browser. To avoid this consent page, add the front end's app registration as an authorized client application in the **Expose an API** page by clicking **Add a client application** and supplying the client ID of the front end's app registration.

::: zone pivot="platform-linux"

> [!NOTE]
> For Linux apps, There's a temporary requirement to configure a versioning setting for the back-end app registration. In the Cloud Shell, configure it with the following commands. Be sure to replace *\<back-end-client-id>* with your back end's client ID.
>
> ```azurecli-interactive
> id=$(az ad app show --id <back-end-client-id> --query id --output tsv)
> az rest --method PATCH --url https://graph.microsoft.com/v1.0/applications/$id --body "{'api':{'requestedAccessTokenVersion':2}}" 
> ```    

::: zone-end
    
Your apps are now configured. The front end is now ready to access the back end with a proper access token.

For information on how to configure the access token for other providers, see [Refresh identity provider tokens](configure-authentication-oauth-tokens.md#refresh-auth-tokens).

### Configure app setting

The frontend application needs to the know the URL of the backend application for API requests. Use the following Azure CLI command to configure the app setting.

```azurecli-interactive
az webapp config appsettings set --resource-group myAuthResourceGroup --name <front-end-app-name> --settings BACKEND_URL=<back-end-app-name>
```


### Configure CORS

In the Cloud Shell, enable CORS to your client's URL by using the [`az webapp cors add`](/cli/azure/webapp/cors#az-webapp-cors-add) command. Replace the _\<back-end-app-name>_ and _\<front-end-app-name>_ placeholders.

```azurecli-interactive
az webapp cors add --resource-group myAuthResourceGroup --name <back-end-app-name> --allowed-origins 'https://<front-end-app-name>.azurewebsites.net'
```

This step isn't related to authentication and authorization. However, you need it so that your browser allows the cross-domain API calls from your Angular.js app. For more information, see [Add CORS functionality](app-service-web-tutorial-rest-api.md#add-cors-functionality).

## Call the backend

Shows how you use the token to call the backend.

## Browse to the apps

1. Use the frontend web site in a browser. The URL is in the formate of `https://<FRONTEND-APP-NAME>.azurewebsites.net/`.
1. The browser requests your authentication to the web app. Complete the authentication.
1. After authentication completes, the frontend application returns the home page of the app.

    :::image type="content" source="./media/tutorial-auth-aad/app-home-page.png" alt-text="Screenshot of web browser showing frontend application after successfully completing authentication.":::

1. Select `Get user's profile`. This passes your authentication in the bearer token to the backend. 
1. The backend end responds with the _fake_ hard-coded profile name: `John Doe`.

    :::image type="content" source="./media/tutorial-auth-aad/app-profile.png" alt-text="Screenshot of web browser showing frontend application after successfully getting fake profile from backend app.":::

## When access tokens expire

Your access token expires after some time. For information on how to refresh your access tokens without requiring users to reauthenticate with your app, see [Refresh identity provider tokens](configure-authentication-oauth-tokens.md#refresh-auth-tokens).

## Troubleshooting

The frontend and backend app both have `debug` routes to help debug the authentication when this application doesn't return the fake profile.

### Did the application source code deploy correctly to each web app?

1. In the Azure portal for the web app, select **Development Tools -> Advanced Tools**, then select **Go ->**. This opens a new browser tab or window. 
1. In the new browser tab, select **Browse Directory -> Site wwwroot**.
1. Verify the following are in the directory:

    * package.json
    * node_modules.tar.gz
    * /src/index.js 

1. Verify the package.json's `name` property is the same as the web name, either `front-end` or `api-b`.

### Did the application start correctly

Both the web apps should return something when the home page is requested. If you can't reach `/debug` on a web app, the app didn't start correctly. Review the error logs for that web app. 

1. In the Azure portal for the web app, select **Development Tools -> Advanced Tools**, then select **Go ->**. This opens a new browser tab or window. 
1. In the new browser tab, select **Browse Directory -> Deployment Logs**.
1. Review each log to find any reported issues. 

### Is the front-end web app configured correctly?

In the front-end app, verify the **Settings -> Configuration** has app settings it needs

* BACKEND_URL: the URL of the API (b) app, such as `https://backend.azurewebsites.net/get-profile`
* SCM_DO_BUILD_DURING_DEPLOYMENT: true

### Does the front-end web app have a valid and correctly scoped token?

Each web site has a `/debug` route to help with this. 

Use the debug route to verify the front-end web app has a token.

1. Verify that the token isn't expired. The debug route decodes the `exp` property in the token in another property `IsExpired`. This value should be false.
1. Verify that the token has the correct scope: The debug route displays the `scp` property for the scope. This value should be `user_impersonation`.

The `/debug` route also displays the HTTP headers and the environment variables, if you need them. 

### Does the back-end web app return an empty profile `{}`? 

1. Refresh your front-end app's user token with a route like: `https://frontend.azurewebsites.net/.auth/refresh`.
1. Retry to get your profile from the API server with a route like: `https://frontend.azurewebsites.net/get-profile`

### Is the front-end web app able to talk to the back-end web app?

Because the client web app calls the API web app from server source code, this isn't something you can see in the browser network traffic. The back-end web app returns any errors to the client web app.

## Clean up resources

In the preceding steps, you created Azure resources in a resource group. 

1. Delete the resource group by running the following command in the Cloud Shell. This command may take a minute to run.


    ```azurecli-interactive
    az group delete --name myAuthResourceGroup
    ```


2. Delete app registrations

    ```azurecli-interactive
    
    ```


<a name="next"></a>
## Next steps

What you learned:

> [!div class="checklist"]
> * Enable built-in authentication and authorization
> * Secure apps against unauthenticated requests
> * Use Azure Active Directory as the identity provider
> * Access a remote app on behalf of the signed-in user
> * Secure service-to-service calls with token authentication
> * Use access tokens from server code
> * Use access tokens from client (browser) code

Advance to the next tutorial to learn how to map a custom DNS name to your app.

> [!div class="nextstepaction"]
> [Secure with custom domain and certificate](tutorial-secure-domain-certificate.md)