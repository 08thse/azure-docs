---
title: "include file"
description: "include file"
services: load-testing
ms.service: load-testing
ms.custom: "include file"
ms.topic: "include"
author: j-martens
ms.author: jmartens
ms.date: 9/04/2021
---

>[!IMPORTANT]
>You can use the resources that you created as prerequisites to other Azure Load Testing tutorials and how-to articles. 

If you don't plan to use any of the resources that you created, delete them so you don't incur any further charges.

* In the Azure portal:
    1. Select **Resource groups** on the far left.
 
    1. From the list, select the resource group that you created.

    1. Select **Delete resource group**.

       ![Screenshot of the selections to delete a resource group in the Azure portal.](./media/aml-delete-resource-group/delete-resources.png)

    1. Enter the resource group name. Then select **Delete**.

* Alternately, you can also use the CLI.
   ```cli
   az group delete --name myResourceGroup
   ```