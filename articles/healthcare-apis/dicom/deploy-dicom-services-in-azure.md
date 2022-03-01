---
title: Deploy the DICOM service using the Azure portal - Azure Health Data Services
description: This article describes how to deploy the DICOM service in the Azure portal.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 02/28/2022
ms.author: aersoy
ms.custom: mode-api
---

# Deploy DICOM service using the Azure portal

In this quickstart, you'll learn how to deploy the DICOM Service using the Azure portal.

Once deployment is complete, you can use the Azure portal to navigate to the newly created DICOM service to see the details including your Service URL. The Service URL to access your DICOM service  will be: ```https://<workspacename-dicomservicename>.dicom.azurehealthcareapis.com```. Make sure to specify the version as part of the url when making requests. More information can be found in the [API Versioning for DICOM service documentation](api-versioning-dicom-service.md).

## Prerequisite

To deploy the DICOM service, you must have a workspace created in the Azure portal. For more information about creating a workspace, see **Deploy Workspace in the Azure portal**.

## Deploying DICOM service

1. On the **Resource group** page of the Azure portal, select the name of your **Azure Health Data Services Workspace**.

   [ ![select workspace resource group.](media/select-workspace-resource-group.png) ](media/select-workspace-resource-group.png#lightbox)

2. Select **Deploy DICOM service**.

   [ ![deploy dicom service.](media/workspace-deploy-dicom-services.png) ](media/workspace-deploy-dicom-services.png#lightbox)


3. Select **Add DICOM service**.

   [ ![add dicom service.](media/add-dicom-service.png) ](media/add-dicom-service.png#lightbox)


4. Enter a name for the DICOM service, and then select **Review + create**. 

    [ ![dicom service name.](media/enter-dicom-service-name.png) ](media/enter-dicom-service-name.png#lightbox)


   (**Optional**) Select **Next: Tags >**.

    Tags are name/value pairs used for categorizing resources. For information about tags, see [Use tags to organize your Azure resources and management hierarchy](../../azure-resource-manager/management/tag-resources.md).

5. When you notice the green validation check mark, select **Create** to deploy the DICOM service.

6. When the deployment process completes, select **Go to resource**.  

   [ ![dicom go to resource.](media/go-to-resource.png) ](media/go-to-resource.png#lightbox)



   The result of the newly deployed DICOM service is shown below.

   [ ![dicom finished deployment.](media/results-deployed-dicom-service.png) ](media/results-deployed-dicom-service.png#lightbox)



## Next steps

In this quickstart, you learned how to deploy the DICOM service using the Azure portal. For more information about the DICOM service, see

>[!div class="nextstepaction"]
>[Overview of the DICOM service](dicom-services-overview.md)

For more information about how to assign roles for the DICOM service, see

>[!div class="nextstepaction"]
>[Assign roles for the DICOM service](https://docs.microsoft.com/azure/healthcare-apis/configure-azure-rbac#assign-roles-for-the-dicom-service)