---
title: 'Azure AD Connect cloud sync directory extensions and custom attribute mapping'
description: This topic provides information on custom attribute mapping in cloud sync.
services: active-directory
author: billmath
manager: amycolannino
ms.service: active-directory
ms.workload: identity
ms.custom: ignite-2022
ms.topic: conceptual
ms.date: 01/11/2023
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
---



# Cloud Sync directory extensions and custom attribute mapping

## Directory extensions
You can use directory extensions to extend the schema in Azure Active Directory (Azure AD) with your own attributes from on-premises Active Directory. This feature enables you to build LOB apps by consuming attributes that you continue to manage on-premises. These attributes can be consumed through [extensions](https://docs.microsoft.com/graph/extensibility-overview). You can see the available attributes by using [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer). You can also use this feature to create dynamic groups in Azure AD.
 

## Custom attributes and mapping 
If you have extended Active Directory to include custom attributes, you can add these attributes and map them to users.  

To discover and map attributes, click **Add attribute mapping**.  The attributes will automatically be discovered and will be available in the drop-down under **source attribute**.  Fill in the type of mapping you want and click **Apply**.
 [![Custom attribute mapping](media/custom-attribute-mapping/schema-1.png)](media/custom-attribute-mapping/schema-1.png#lightbox)

For information on new attributes that are added and updated in Azure AD see the [user resource type](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0#properties) and consider subscribing to [change notifications](https://docs.microsoft.com/graph/webhooks).

For more information on extension attributes, see [Syncing extension attributes for Azure Active Directory Application Provisioning](../app-provisioning/user-provisioning-sync-attributes-for-mapping.md)


## Syncing directory extensions for Azure Active Directory Connect cloud sync 

You can use [directory extensions](https://learn.microsoft.com/graph/api/resources/extensionproperty?view=graph-rest-1.0) to extend the synchronization schema directory definition in Azure Active Directory (Azure AD) with your own attributes. 

>[!Important]
> Directory extension for Azure Active Directory Connect cloud sync is only supported for application with the identifier URI “api://<tenantId>/CloudSyncCustomExtensionsApp” and the [Tenant Schema Extension App](../hybrid/how-to-connect-sync-feature-directory-extensions.md#configuration-changes-in-azure-ad-made-by-the-wizard) created by Azure AD Connect 

### Create application and service principal for directory extension 

You need to create an [application](https://learn.microsoft.com/graph/api/resources/application?view=graph-rest-1.0) with the identifier URI "api://<tenantId>/CloudSyncCustomExtensionsApp" if it does not exist and create a service principal for the application if it does not exist. 


 1. Check if application exists 
Check if application with the identifier URI "api://<tenantId>/CloudSyncCustomExtensionsApp" exists. 

     - Using Microsoft Graph 

     ```
     GET /applications/{applicationObjectId}
     ```

     For more information see [Get application](https://learn.microsoft.com/graph/api/application-get?view=graph-rest-1.0&tabs=http)

     - Using PowerShell 
     
     ```
     Get-AzureADApplication -Filter "identifierUris/any(uri:uri eq 'api://<tenantId>/CloudSyncCustomExtensionsApp')"
     ```

     For more information see [Get-AzureADApplication](https://learn.microsoft.com/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0)

 2. Create application 
     If the application does not exist, create the application with identifier URI “api://<tenantId>/CloudSyncCustomExtensionsApp.”

     - Using Microsoft Graph 
     ```
     POST https://graph.microsoft.com/v1.0/applications
     Content-type: application/json

     {
      "displayName": "CloudSyncCustomExtensionsApp",
      "identifierUris": ["api://<tenant id>/CloudSyncCustomExtensionsApp"]
     }
     ```
     For more information see [create application](https://learn.microsoft.com/graph/api/application-post-applications?view=graph-rest-1.0&tabs=http)

     - Using PowerShell 
     ```
     New-AzureADApplication -DisplayName "CloudSyncCustomExtensionsApp" -IdentifierUris "api://<tenant id>/CloudSyncCustomExtensionsApp"
     ```
     For more information see [New-AzureADApplication](https://learn.microsoft.com/powershell/module/azuread/new-azureadapplication?view=azureadps-2.0)

 

 3. Check if the service principal exists for the application with identifier URI “api://<tenantId>/CloudSyncCustomExtensionsApp” exists. 

     - Using Microsoft Graph 
     ```
     GET /servicePrincipals(appId='{appId}')
     ```
     For more information see [get service principal](https://learn.microsoft.com/graph/api/serviceprincipal-get?view=graph-rest-1.0&tabs=http)

     - Using PowerShell 
     ```
     Get-AzureADServicePrincipal -ObjectId  '<application objectid>'
     ```
     For more information see [Get-AzureADServicePrincipal](https://learn.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0)
 

 4. Create service principal for the application 
     If a service principal does not exist, create a new service principal for the application with identifier URI “api://<tenantId>/CloudSyncCustomExtensionsApp”

     - Using Microsoft Graph 
     ```
     POST https://graph.microsoft.com/v1.0/servicePrincipals
     Content-type: application/json

     {
     "appId": 
     "<application appId>"
     }
     ```
     For more information see [create servicePrincipal](https://learn.microsoft.com/graph/api/serviceprincipal-post-serviceprincipals?view=graph-rest-1.0&tabs=http)

     - Using PowerShell 
     
     ```
     New-AzureADServicePrincipal -AppId '<appId>'
     ```
     For more information see [New-AzureADServicePrincipal](https://learn.microsoft.com/en-us/powershell/module/azuread/new-azureadserviceprincipal?view=azureadps-2.0)
 
 5. Create directory extension 
You can create directory extensions in Azure AD in several different ways. 

|Method|Description|URL|
|-----|-----|-----|
|MS Graph|Create extensions using GRAPH|[Create extensionProperty](https://learn.microsoft.com/graph/api/application-post-extensionproperty?view=graph-rest-1.0&tabs=http)|
|PowerShell|Create extensions using PowerShell|[New-AzureADApplicationExtensionProperty](https://learn.microsoft.com/powershell/module/azuread/new-azureadapplicationextensionproperty?view=azureadps-2.0)| 
Using Cloud Sync and Azure AD Connect|Create extensions using Azure AD Connect|[Create an extension attribute using Azure AD Connect](https://learn.microsoft.com/azure/active-directory/app-provisioning/user-provisioning-sync-attributes-for-mapping#create-an-extension-attribute-using-azure-ad-connect) and |
|Customizing attributes to sync|Information on customizing which attributes to synch|[Customize which attributes to synchronize with Azure AD](https://learn.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-feature-directory-extensions#customize-which-attributes-to-synchronize-with-azure-ad)

## Additional resource

- [Understand the Azure AD schema and custom expressions](concept-attributes.md)
- [What is Azure AD Connect cloud sync?](what-is-cloud-sync.md)
- [Installing cloud sync](how-to-install.md)