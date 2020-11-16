---
title: Enable Remote Desktop in Cloud Services (extended support) using the Azure Portal
description: Enable Remote Desktop for Cloud Services (extended support) instances using the Azure Portal
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: 
---

# Enable Remote Desktop for Cloud Services (extended support) instances using the Azure Portal

Remote Desktop enables you to access a role running in Azure. You can use a Remote Desktop connection to troubleshoot and diagnose problems with your application while it is running.

Remote Desktop can be enabled in your role during development by including the Remote Desktop modules in your service definition file or enabled through the Remote Desktop extension post deployment. 

## Configure Remote Desktop from the Azure portal

1. Navigate to the Cloud Service you want to enable Remote Desktop on and select **"Remote Desktop"** in the left navigation pane.

    :::image type="content" source="media/remote-desktop-1.png" alt-text="Image shows selecting the Remote Desktop option in the Azure Portal":::

2. Switch Remote Desktop from disabled to enabled. 

3. Fill in the required fields for user name, password, expiry, and certificate (not required)

    :::image type="content" source="media/remote-desktop-2.png" alt-text="Image shows inputting the information required to connect to remote desktop.":::

4. Select the role instance you want to enable Remote Desktop on. You can also select to enable Remote Desktop in all available role instances. 

5. When you finish your configuration updates, select Save. It will take a few moments before your role instances are ready to receive connections.

## Connect to role instances with Remote Desktop enabled
Once Remote Desktop is enabled on the roles, you can initiate a connection directly from the Azure portal.

1. Click Instances to open the Instances settings.
2. Select a role instance that has Remote Desktop configured.
3. Click Connect to download an RDP file for the role instance.
4. Click Open and then connect to start the Remote Desktop connection.
 
    :::image type="content" source="media/remote-desktop-3.png" alt-text="Image shows selecting the worker role instance in the Azure Portal.":::

## Next steps
For more information, see [Cloud Services (extended support) Reference Documentation](https://docs.microsoft.com/rest/api/compute/cloudservices/) 