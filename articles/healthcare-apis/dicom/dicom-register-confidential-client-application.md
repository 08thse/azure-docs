---
title: Register a confidential client application in Azure Active Directory - Azure Healthcare APIs for DICOM
description: This article explains how to register a confidential client application in Azure Active Directory.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 07/10/2021
ms.author: aersoy
---

# Register a confidential client application in Azure Active Directory

In this tutorial, you'll learn how to register a confidential client application in Azure Active Directory (Azure AD).

## Register a new application

A client application registration is an Azure AD representation of an application that can be used to authenticate on behalf of a user and request access to resource applications. A confidential client application is an application that can be trusted to hold a secret and present that secret when requesting access tokens. Examples of confidential applications are server-side applications.

To register a new confidential client application, refer to the steps below.

1. In the [Azure portal](https://portal.azure.com), select **Azure Active Directory**.
2. Select the **App registrations** blade.
3. Select **New registration**.

   :::image type="content" source="media/dicom-azure-app-registrations.png" alt-text="Azure App registrations" lightbox="dicom-azure-app-registrations.png":::


4. Enter a user-facing display name for the application.

   :::image type="content" source="media/dicom-registration-application-name.png" alt-text="Azure register an application" lightbox="dicom-registration-application-name.png":::

5. For **Supported account types**, select who can use the application or access the API.
6. (**Optional**) Provide a **Redirect URI**. These details can be changed later, but if you know the reply URL of your application, enter it.
7. Select **Register**.

## API permissions

Now that you've registered your application, you must select which API permissions this application must request on behalf of users.

1. Select the **API permissions** blade.

   :::image type="content" source="media/dicom-add-api-permissions.png" alt-text="Add API permissions" lightbox="dicom-add-api-permissions.png":::

2. Select **Add a permission**.

   If you're using the Azure Healthcare APIs, you'll add a permission to the DICOM service by searching for **Azure API for DICOM** under **APIs my organization** uses. 

   :::image type="content" source="media/dicom-search-apis-permissions.png" alt-text="Search API permissions" lightbox="dicom-search-apis-permissions.png":::

   The search result for Azure API for DICOM will only return if you've already deployed the DICOM service in the workspace.

   If you're referencing a different resource application, select your DICOM API Resource Application Registration that you created previously under **APIs my organization**.

3. Select scopes (permissions) that the confidential client application will ask for on behalf of a user. Select **user_impersonation**, and then select **Add permissions**.

   :::image type="content" source="media/dicom-select-scopes.png" alt-text="Select permissions scopes." lightbox="dicom-select-scopes.png":::

## Application secret

1. Select **Certificates & secrets**, and then select **New client secret**.

   :::image type="content" source="media/dicom-new-client-secret.png" alt-text="Certificates and secrets." lightbox="dicom-new-client-secret.png":::

2. Enter a **Description** for the client secret. Select the **Expires** drop-down menu to choose an expiration time frame, and then click **Add**.

   :::image type="content" source="media/dicom-client-secret-description.png" alt-text="Client secret description." lightbox="dicom-client-secret-description.png":::

3. After the client secret string is created, copy its **Value** and **ID**, and store them in a secure location of your choice.

   :::image type="content" source="media/dicom-client-secret-value-id.png" alt-text="Client secret value ID." lightbox="dicom-client-secret-value-id.png":::

   > [!NOTE]
   > The client secret string is visible only once in the Azure portal. When you navigate away from the Certificates & secrets web page and then return back to it, the Value string becomes masked. It's important to make a copy your client secret string immediately after it is generated. If you don't have a backup copy of your client secret, you must repeat the above steps to regenerate it.

## Next steps

In this article, you were guided through the steps of how to register a confidential client application in the Azure AD. You were also guided through the steps of how to add API permissions to the Azure API for DICOM. Lastly, you were shown how to create an application secret. 

>[!div class="nextstepaction"]
>[Overview of a DICOM service](dicom-services-overview.md)



