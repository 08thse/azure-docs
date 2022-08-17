---
title: 'Tutorial: Delete network functions on Azure Stack Edge'
titleSuffix: Azure Network Function Manager
description: In this tutorial, learn how to delete a network function as a managed application.
author: sushantjrao
ms.service: network-function-manager
ms.topic: tutorial
ms.date: 07/09/2022
ms.author: sushrao
ms.custom: 
---
# Tutorial: Delete network functions on Azure Stack Edge

In this tutorial, you learn how to delete Azure Network Function Manager - Network Function and Azure Network Function Manager - Device using the Azure portal. 

> [!div class="checklist"]
> * Delete [Network Function Manager - Network Function](#delete-network-function)
> * Delete [Azure Network Function Manager - Device](#delete-network-function-manager)

* On the **Overview** tab for the Network Function Manager - Device, verify the following values are present:
  * Provisioning State = Succeeded
  * Device Status = Registered

## <a name="delete-network-function"></a> Delete network function

1. Sign in to the [Azure portal](https://portal.azure.com).

1. Navigate to the **Azure Network Manager - Devices** resource in which you have deployed a network function and select **Network Function**.

   :::image type="content" source="./media/delete-functions/select-network-function.png" alt-text="Screenshot of +Select Network Function." lightbox="./media/delete-functions/select-network-function.png":::

1. Select **Delete** Network Function.

    :::image type="content" source="./media/delete-functions/delete-network-function.png" alt-text="Screenshot of +Delete Network Function." lightbox="./media/delete-functions/delete-network-function.png":::

   > [!NOTE]
   > If you encounter failed to delete resource error with *access is denied because of the deny assignment 'System deny assignment created by managed application'* while deleting the network function, then copy the name of the Managed Application from the error and refer **Step 4**.

   >:::image type="content" source="./media/delete-functions/failed-to-delete.png" alt-text="Screenshot of +Failed To Delete." lightbox="./media/delete-functions/failed-to-delete.png":::
   > 
   
1. Navigate to search box within the **Azure portal** and search for the **Managed Application** which was thrown as an exception in **Step 3**.

    :::image type="content" source="./media/delete-functions/managed-application.png" alt-text="Screenshot of +Managed Application." lightbox="./media/delete-functions/managed-application.png":::

1. Select **Delete** Managed Application
    
    :::image type="content" source="./media/delete-functions/delete-managed-application.png" alt-text="Screenshot of +Delete Managed Application." lightbox="./media/delete-functions/delete-managed-application.png":::

## <a name="delete-network-function-manager"></a> Delete network function manager - device

   > [!IMPORTANT]
   > Ensure that all the Network Function deployed within the Azure Network Function Manager is deleted before proceeding to the next step.
   >

1. Navigate to the **Azure Network Manager - Devices** resource in which you have deleted a network function and select **Delete** Azure Network Function Manager - Device

   :::image type="content" source="./media/delete-device/delete-network-function-manager.png" alt-text="Screenshot of +delete Network Function Manager - Device." lightbox="./media/delete-device/delete-network-function-manager.png":::
