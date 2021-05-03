---
title: Azure IoT Hub Managed Identity | Microsoft Docs
description: How to use managed identities to allow egress connectivity from your IoT Hub to other Azure resources.
author: miag
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/30/2021
ms.author: miag
---

# IoT Hub support for Managed Identities 

Managed identities provide Azure services with an automatically managed identity in Azure AD in a secure manner. This eliminates the needs for developers having to manage credentials by providing an identity. There are two types of managed identities: system-assigned and user-assigned. IoT Hub supports both. 

In IoT Hub, managed identities can be used for egress connectivity from IoT Hub to other Azure services for features such as [message routing](iot-hub-devguide-messages-d2c), [file upload](iot-hub-devguide-file-upload), and [bulk device import/export](iot-hub-bulk-identity-mgmt). In this article, you learn how to use system-assigned and user-assigned managed identities in your IoT Hub for different functionalities. 


## Prerequisites
1.	Read the documentation of [managed identities for Azure resources](./../active-directory/managed-identities-azure-resources/overview) to understand the differences between system-assigned and user-assigned managed identity.

2.	If you don’t have an IoT Hub, [create an IoT Hub](iot-hub-create-through-portal) before continue.


## System-assigned managed identity 

In this section, you learn how to add and remove a system-assigned managed identity from an IoT Hub using Azure Portal.
1.	Sign in to the Azure portal and navigate to your desired IoT Hub.
2.	Navigate to **Identity** in your IoT Hub portal
3.	Under **System-assigned** tab, select **On** and click **Save**.
4.	To remove system-assigned managed identity from an IoT Hub, select **Off** and click **Save**.

:::image type="content" source="./media/iot-hub-managed-identity/system-assigned.png" alt-text="Turn on system-assigned":::

## User-assigned managed identity 
In this section, you learn how to add and remove a user-assigned managed identity from an IoT Hub using Azure Portal.
1.	First you need to create a user-assigned managed identity as a standalone resource. You can follow the instructions [here](./../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal) to create a user-assigned managed identity.
2.	Go to your IoT Hub, navigate to the **Identity** in your IoT Hub portal.
3.	Under **User-Assigned** tab, click **Add user-assigned managed identity**. Choose the user-assigned managed identity you want to add to IoT Hub and then click **Select**. 
4.	You can remove a user-assigned identity from an IoT Hub. Choose the user-assigned identity you want to remove, and click **Remove** button. Note you are only removing it from IoT Hub, and this does not delete the user-assigned identity as a resource. To delete the user-assigned identity as a resource, follow the instructions [here](./../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal).

:::image type="content" source="./media/iot-hub-managed-identity/user-assigned.png" alt-text="Add the user-assigned":::

## Egress connectivity from IoT Hub to other Azure resources
In IoT Hub, managed identities can be used for egress connectivity from IoT Hub to other Azure services for [message routing](iot-hub-devguide-messages-d2c), [file upload](iot-hub-devguide-file-upload), and [bulk device import/export](iot-hub-bulk-identity-mgmt). You can choose which managed identity to use for each IoT Hub egress connectivity to customer-owned endpoints including storage accounts, event hubs and service bus endpoints. 

### Message routing
In this section we use the [message routing](iot-hub-devguide-messages-d2c) to event hub custom endpoint as an example. The same thing applies to other routing custom endpoints. 

1.	First we need to go to your event hub in Azure portal, to assign the managed identity the right access. 
2.	In your event hub, navigate to the **Access control (IAM)** tab and click **Add** then **Add a role assignment**.
3.	Select **Event Hubs Data Sender as role**.
4.  For user-assigned, choose **User-assigned managed identity** under Assign access to. Select your subscription and your user-assigned managed identity in the drop-down list. Click the **Save** button.

:::image type="content" source="./media/iot-hub-managed-identity/eventhub-iam-user-assigned.png" alt-text="Event Hub IAM tab with user-assigned":::

5.	For system-assigned, under **Assign access to** choose **User, group, or service principal** and select your IoT Hub's resource name in the drop-down list. Click **Save**.

:::image type="content" source="./media/iot-hub-managed-identity/eventhub-iam-system-assigned.png" alt-text="Event Hub IAM tab with system-assigned":::

> [!NOTE]
> You need to complete above steps to assign the managed identity the right access before adding the event hub as a custom endpoint in IoT Hub using the managed identity. 

6.	Next, go to your IoT Hub. In your Hub, navigate to **Message Routing**, then click **Custom endpoints**. Click **Add** and choose the type of endpoint you would like to use. In this section, we use event hub as the example.
7.	At the bottom of the page, choose your preferred Authentication type. In this section we use the **User-Assigned** as the example. In the dropdown, select the preferred user-assigned managed identity then click **Create**.

:::image type="content" source="./media/iot-hub-managed-identity/eventhub-routing-endpoint.png" alt-text="Choose authentication type in IoT Hub":::

8. Custom endpoint successfully created. 
9. After creation, you can still change the authentication type. Select the custom endpoint that you want to change the authentication type, then click **Change authentication type**.

:::image type="content" source="./media/iot-hub-managed-identity/change-authentication-type.png" alt-text="Change authentication type":::

10. Choose the new authentication type to be updated for this endpoint, click **Save**.

### File Upload
IoT Hub's [file upload](iot-hub-devguide-file-upload) feature allows devices to upload files to a customer-owned storage account. To allow the file upload to function, IoT Hub need to have connectivity to the storage account. Similar to message routing, you can pick the preferred authentication type and managed identity for IoT Hub egress connectivity to your Azure Storage account. 

1. In the Azure portal, navigate to your storage account's **Access control (IAM)** tab and click **Add** under the **Add a role assignment** section.
2. Select **Storage Blob Data Contributor** (not Contributor or Storage Account Contributor) as role.
3. For user-assigned, choose **User-assigned managed identity** under Assign access to. Select your subscription and your user-assigned managed identity in the drop-down list. Click the **Save** button.
4. For system-assigned, under **Assign access to** choose **User, group, or service principal** and select your IoT Hub's resource name in the drop-down list. Click **Save**.

> [!NOTE]
> You need to complete above steps to assign the managed identity the right access before saving the storage account in IoT Hub for file upload using the managed identity.
 
1. On your IoT Hub's resource page, navigate to **File upload** tab.
1. On the page that shows up, select the container that you intend to use in your blob storage, configure the **File notification settings, SAS TTL, Default TTL, and Maximum delivery count** as desired. Choose the preferred authentication type, and click **Save**.

:::image type="content" source="./media/iot-hub-managed-identity/file-upload.png" alt-text="File Upload":::

### Bulk device import/export

IoT Hub supports the functionality to [import/export devices](iot-hub-bulk-identity-mgmt)' information in bulk from/to a customer-provided storage blob. This functionality requires connectivity from IoT Hub to the storage account. 

1. In the Azure portal, navigate to your storage account's **Access control (IAM)** tab and click **Add** under the **Add a role assignment** section.
2. Select **Storage Blob Data Contributor** (not Contributor or Storage Account Contributor) as role.
3. For user-assigned, choose **User-assigned managed identity** under Assign access to. Select your subscription and your user-assigned managed identity in the drop-down list. Click the **Save** button.
4. For system-assigned, under **Assign access to** choose **User, group, or service principal** and select your IoT Hub's resource name in the drop-down list. Click **Save**.


### Using Rest API or SDK for import and export jobs

You can now use the Azure IoT REST APIs for creating import and export jobs. You will need to provide the following properties in the request body 
1. storageAuthenticationType - The value should be set to 'identityBased' 
1. inputBlobContainerUri - Used only in import job 
1. outputBlobContainerUri - Used for both import and export job 
1. identity - The managed identity to use


Azure IoT Hub SDKs also support this functionality in the service client's registry manager. The following code snippet shows how to initiate an import job or export job in using the C# SDK.

C# code snippet

```dotnetcli
    // Create an export job
    // see note below

    using RegistryManager srcRegistryManager = RegistryManager.CreateFromConnectionString(hubConnectionString);

    JobProperties jobProperties = new JobProperties
    {
        OutputBlobContainerUri = blobContainerUri,
        StorageAuthenticationType = StorageAuthenticationType.IdentityBased,
        Identity = new ManagedIdentity
        {
            userAssignedIdentity = userDefinedManagedIdentityResourceId
        }
    };

    JobProperties jobResult = await srcRegistryManager
        .ExportDevicesAsync(jobProperties);
```

```dotnetcli
    // Create an import job
    // see note below

    using RegistryManager destRegistryManager = RegistryManager.CreateFromConnectionString(hubConnectionString);
    
    JobProperties jobProperties = new JobProperties
    {
        InputBlobContainerUri = blobContainerUri,
        OutputBlobContainerUri = blobContainerUri,
        StorageAuthenticationType = StorageAuthenticationType.IdentityBased,
        Identity = new ManagedIdentity
        {
            userAssignedIdentity = userDefinedManagedIdentityResourceId
        }
    };

    JobProperties jobResult = await destRegistryManager
        .ImportDevicesAsync(jobProperties);
```                

Python code snippet

```python
# see note below
iothub_job_manager = IoTHubJobManager("<IoT Hub connection string>")

# Create an import job
result = iothub_job_manager.create_import_export_job(JobProperties(
    type="import",
    input_blob_container_uri="<input container URI>",
    output_blob_container_uri="<output container URI>",
    storage_authentication_type="identityBased",
    identity=ManagedIdentity(
        user_assigned_identity="<resource ID of user assigned managed identity>"
    )
))

# Create an export job
result = iothub_job_manager.create_import_export_job(JobProperties(
    type="export",
    output_blob_container_uri="<output container URI>",
    storage_authentication_type="identityBased",
    exclude_keys_in_export=True,
    identity=ManagedIdentity(
        user_assigned_identity="<resource ID of user assigned managed identity>"
    ) 
))
```

Note:
1. If storageAuthenticationType is set to identityBased and userAssignedIdentity property is not null, the jobs will use the specified user-assigned managed identity.
1. If the IoT Hub is not configured with the user-assigned managed identity specified in userAssignedIdentity, the job will fail.
1. If storageAuthenticationType is set to identityBased the userAssignedIdentity property is null, the jobs will use system-assigned identity.
1. If the IoT Hub is not configured with the user-assigned managed identity, the job will fail.
1. If <span class="x x-first x-last">storageAuthenticationType</span> is set to <span class="x x-first x-last">identityBased</span> and neither user<span class="x x-first x-last">-assigned </span>nor system<span class="x x-first x-last">-assigned</span> managed identities are configured on the hub, the job will fail.

## Next steps

Use the links below to learn more about IoT Hub features:

* [Message routing](./iot-hub-devguide-messages-d2c.md)
* [File upload](./iot-hub-devguide-file-upload.md)
* [Bulk device import/export](./iot-hub-bulk-identity-mgmt.md)
