---
title: Understand Device Update for IoT Hub authentication and authorization | Microsoft Docs
description: Understand how Device Update for IoT Hub uses Azure RBAC to provide authentication and authorization for users and service APIs.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
---

# Azure Role-based access control (RBAC) and Device Update

Device Update uses Azure RBAC to provide authentication and authorization for users and service APIs. In order for other users and applications to have access to Device Update, users or applications must be granted access to this resource. It is also necessary to [configure access for Azure Device Update service principal](./device-update-control-access.md#configuring-access-for-azure-device-update-service-principal-in-the-iot-hub) for successfully deploying updates and managing your devices.

## Configure access control roles

 These are the roles that are supported by Device Update:

|   Role Name   | Description  |
| :--------- | :---- |
|  Device Update Administrator | Has access to all device update resources  |
|  Device Update Reader| Can view all updates and deployments |
|  Device Update Content Administrator | Can view, import, and delete updates  |
|  Device Update Content Reader | Can view updates  |
|  Device Update Deployments Administrator | Can manage deployment of updates to devices|
|  Device Update Deployments Reader| Can view deployments of updates to devices |

A combination of roles can be used to provide the right level of access. For example, a developer can import and manage updates using the Device Update Content Administrator role, but needs a Device Update Deployments Reader role to view the progress of an update. Conversely, a solution operator with the Device Update Reader role can view all updates, but needs to use the Device Update Deployments Administrator role to deploy a specific update to devices.

## Configuring access for Azure Device Update service principal in the IoT Hub

Device Update for IoT Hub communicates with the IoT Hub for deployments and manage updates at scale. In order to enable Device Update to do this, users need to set IoT Hub Data Contributor Contributor access for Azure Device Update Service Principal in the IoT Hub permissions. 

Below actions will be blocked, after 9/28/22, if these permissions are not set:
* Create Deployment
* Cancel Deployment
* Retry Deployment 
* Get Device

1. Go to the **IoT Hub** connected to your Device Update Instance. Click **Access Control(IAM)**
2. Click **+ Add** -> **Add role assignment**
3. Under Role tab, select **IoT Hub Data Contributor**
4. Click **Next**. For **Assign access to**, select **User, group, or service principal**. Click **+ Select Members**, search for '**Azure Device Update**'
5. Click **Next** -> **Review + Assign**


## Authenticate to Device Update REST APIs

Device Update uses Azure Active Directory (AD) for authentication to its REST APIs. To get started, you need to create and configure a client application.

### Create client Azure AD App

To integrate an application or service with Azure AD, [first register](../active-directory/develop/quickstart-register-app.md) a client application with Azure AD. Client application setup will vary depending on the authorization flow you'll need (users, applications or managed identities). For example, to call Device Update from:

* Mobile or desktop application, add `Mobile and desktop applications` platform with https://login.microsoftonline.com/common/oauth2/nativeclient for the Redirect URI.
* Website with implicit sign-on, add `Web` platform and select `Access tokens (used for implicit flows)`.

### Configure permissions

Next, add permissions for calling Device Update to your app:
1. Go to `API permissions` page of your app and click `Add a permission`.
2. Go to `APIs my organization uses` and search for `Azure Device Update`.
3. Select `user_impersonation` permission and click `Add permissions`.

### Requesting authorization token

Device Update REST API requires OAuth 2.0 authorization token in the request header. Following are some examples of various ways to request an authorization token.

#### Using Azure CLI

```azurecli
az login
az account get-access-token --resource 'https://api.adu.microsoft.com/'
```

#### Using PowerShell MSAL Library

[MSAL.PS](https://github.com/AzureAD/MSAL.PS) PowerShell module is a wrapper over [Microsoft Authentication Library for .NET (MSAL .NET)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet). It supports various authentication methods.

_Using user credentials_:

```powershell
$clientId = '<app_id>'
$tenantId = '<tenant_id>'
$authority = "https://login.microsoftonline.com/$tenantId/v2.0"
$Scope = 'https://api.adu.microsoft.com/user_impersonation'

Get-MsalToken -ClientId $clientId -TenantId $tenantId -Authority $authority -Scopes $Scope
```

_Using user credentials with device code_:

```powershell
$clientId = '<app_id>’
$tenantId = '<tenant_id>’
$authority = "https://login.microsoftonline.com/$tenantId/v2.0"
$Scope = 'https://api.adu.microsoft.com/user_impersonation'

Get-MsalToken -ClientId $clientId -TenantId $tenantId -Authority $authority -Scopes $Scope -Interactive -DeviceCode
```

_Using app credentials_:

```powershell
$clientId = '<app_id>’
$tenantId = '<tenant_id>’
$cert = '<client_certificate>'
$authority = "https://login.microsoftonline.com/$tenantId/v2.0"
$Scope = 'https://api.adu.microsoft.com/.default'

Get-MsalToken -ClientId $clientId -TenantId $tenantId -Authority $authority -Scopes $Scope -ClientCertificate $cert
```

## Next Steps
* Create device update resources and configure access control roles](./create-device-update-account.md)
