---
title: Choosing a method of deployment for MedTech service in Azure - Azure Health Data Services
description: In this article, you'll learn how to choose a method to deploy the MedTech service in Azure.
author: mcevoy-building7
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 09/06/2022
ms.author: v-smcevoy
---

# Choose a deployment method

MedTech service provides multiple methods for deploying it into an Azure Platform as a service (PaaS) configuration. Each method has different advantages so you can customize your development environment to suit your needs.

The different deployment methods are:

## Choosing Azure portal

Using Azure portal to deploy MedTech service allows you to view and manage all your applications in one unified hub. This allows you to utilize portal's graphical features of menus, buttons, and other visual elements to implement your deployment procedures. There are two different methods available:

### QuickStart template

This is the easiest and fastest method because it automates, with the touch of a button, the creation of managed identity RBAC roles and provisions a workspace, namespace, an Event Hubs instance, FHIR service instance, and MedTech service instance.

### Manual deployment

This method requires you to implement every step individually. None are automatic, but this procedure can be useful if you need to customize or troubleshoot your deployment process.

## Choosing an ARM template

Deploying MedTech service with an Azure Resource Manager (ARM) template can be useful for adding automation code to scale your procedures for use by many developers. Azure provides Azure PowerShell and Azure CLI to speed up your configurations for large teams.

## Deployment architecture overview

The following diagram outlines the basic steps of MedTech service deployment. It also shows how these steps fit together with its data processing for ingesting and converting medical device data into unified FHIR service Observation resources so they can be used in the cloud.

:::image type="content" source="media/iot-get-started/get-started-with-iot.png" alt-text="Diagram showing MedTech service architecture overview." lightbox="media/iot-get-started/get-started-with-iot.png":::

This diagram shows the six different steps of MedTech service PaaS. Only the first four apply to deployment. All the methods of deployment will implement each of these four steps, the QuickStart template method will automatically implement part of step 1 for creating RBAC roles, and all of step 2, provisioning workspace and namespace. The key features of the four deployment steps are listed below:

### Step 1: Prerequisites

- Azure subscription
- Create RBAC roles contributor and user access administrator or owner. This feature is included in the QuickStart template method, but it is not included in manual or ARM template and must be completed individually.

### Step 2: Provision

- Create a resource group and workspace for Event Hubs, FHIR, and MedTech services.
- Provision an Event Hubs instance to a namespace.
- Provision a FHIR service instance to the same workspace.
- Provision a MedTech service instance in the same workspace.

The QuickStart template method includes these steps automatically, but is not in manual or the ARM template and must be completed individually.

### Step 3: Configure

- Configure MedTech service to ingest data from an event hub.
- Configure device mapping properties.
- Configure destination mappings to an Observation resource in the FHIR service.
- Create and deploy MedTech service, now that prerequisites, provisioning, and configuration are complete.
- All deployment methods, including QuickStart, manual, and ARM template, must do each part of this step.

### Step 4: Post-Deployment

- Connect to services.
- Must use managed identity to grant access to the device message event hub.
- Must also use managed identity to grant access to the FHIR service, enabling FHIR to receive data from the MedTech service.
- All deployment methods, including QuickStart, manual, and ARM template, must do each part of this step.

For more details on all six step of this architecture diagram, see [Get started with the MedTech service in the Azure Health Data Services](./get-started-with-iot.md).

## Granting access to the device message event hub

For information about granting access to the device message event hub, see [Granting access to the device message event hub](deploy-07-new-post-deploy.md#granting-access-to-the-device-message-event-hub).

## Granting access to the FHIR service

For information about granting access to the FHIR service, see [Granting access to the FHIR service](deploy-07-new-post-deploy.md#granting-access-to-the-fhir-service).

## Next steps

In this article, you learned about the different types of deployment for MedTech service. To learn more about each type of deployment, see

>[!div class="nextstepaction"]
>[How to deploy MedTech service using a Quickstart template with Azure portal](deploy-02-new-button.md)

>[!div class="nextstepaction"]
>[How to manually deploy MedTech service with Azure portal](deploy-03-new-manual.md)

>[!div class="nextstepaction"]
>[How to deploy MedTech service using an ARM template and Azure PowerShell or Azure CLI](deploy-08-new-ps-cli.md)

FHIR&#174; is a registered trademark of Health Level Seven International, registered in the U.S. Trademark Office and is used with their permission.



