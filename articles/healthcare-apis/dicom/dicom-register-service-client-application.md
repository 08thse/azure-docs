---
title: Register a service client application in Azure Active Directory - Azure Healthcare APIs for DICOM
description: This article explains how to register a service client application in Azure Active Directory.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 07/10/2021
ms.author: aersoy
---

# Register a service client application in Azure Active Directory

In this article, you'll learn how to register a service client application in Azure Active Directory (Azure AD).

## Application registrations in the Azure portal

Client application registrations are Azure AD representations of applications that can be used to authenticate and obtain tokens. A service client is intended to be used by an application to obtain an access token without interactive authentication of a user. It will have certain application permissions and use an application secret (password) when obtaining access tokens.

Refer to the steps below to create a new service client.

1. In the [Azure portal](https://portal.azure.com), select **Azure Active Directory**.
2. In the **Azure Active Directory** blade, select **App registrations**.
3. Select **New registration**.

:::image type="content" source="media/dicom-azure-app-registrations.png" alt-text="Azure App registrations" lightbox="dicom-azure-app-registrations.png":::

4. Provide the DICOM service application with a user-facing display name. Service client applications typically do not use a reply URL.

:::image type="content" source="media/dicom-registration-application-name.png" alt-text="Azure register an application" lightbox="dicom-registration-application-name.png":::

5. Select **Register**.

## API permissions

Now that you have registered your DICOM service application, you'll need to select which API permissions this application must request on behalf of users.

1. Select the **API permissions** blade.

:::image type="content" source="media/dicom-add-api-permissions.png" alt-text="Add API permissions" lightbox="dicom-add-api-permissions.png":::

2. Select **Add a permission**.

If you are using the DICOM, you'll add a permission to the Azure API for DICOM by searching for **Azure Healthcare APIs** under **APIs my organization uses**. You'll only be able to find this if you have already registered the `Microsoft.HealthcareAPIs` resource provider.

:::image type="content" source="media/dicom-request-my-api-permissions.png" alt-text="My API permissions" lightbox="dicom-request-my-api-permissions.png":::

:::image type="content" source="media/dicom-request-api-permissions.png" alt-text="Request API permissions" lightbox="dicom-request-api-permissions.png":::

3. Select scopes (permissions) that the confidential client application will ask for on behalf of a user.

:::image type="content" source="media/dicom-select-scopes.png" alt-text="Select permissions scopes." lightbox="dicom-select-scopes.png":::

4. Grant consent to the application. If you don't have the permissions required, check with your ADD administrator.


## Application secret

The service client needs a secret (password) to obtain a token.

1. Select **Certificates & secrets**, and then select **New client secret**.

:::image type="content" source="media/dicom-new-client-secret.png" alt-text="Certificates and secrets." lightbox="dicom-new-client-secret.png":::

2. Enter a **Description** for the client secret. Select the **Expires** drop-down menu to choose an expiration time frame, and then click **Add**.

:::image type="content" source="media/dicom-client-secret-description.png" alt-text="Client secret description." lightbox="dicom-client-secret-description.png":::

3. After the client secret string is created, copy its **Value** and **ID**, and store them in a secure location of your choice.

:::image type="content" source="media/dicom-client-secret-value-id.png" alt-text="Client secret value ID." lightbox="dicom-client-secret-value-id.png":::

4. Select Add.

> [!NOTE]
> The client secret string is visible only once in the Azure portal. When you navigate away from the Certificates & secrets web page and then return back to it, the Value string becomes masked. It's important to make a copy your client secret string immediately after it is generated. If you don't have a backup copy of your client secret, you must repeat the above steps to regenerate it.


## Next steps

In this article, you've learned how to register a service client application in Azure AD. 

>[!div class="nextstepaction"]
>[Overview of a DICOM service](dicom-services-overview.md)




