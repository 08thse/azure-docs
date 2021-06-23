---
title: Delete backups of an Azure NetApp Files volume  | Microsoft Docs
description: Describes how to delete individual backups that you no longer need to keep for a volume.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''

ms.assetid:
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/01/2021
ms.author: b-juche
---
# Delete backups of a volume 

You can delete individual backups that you no longer need to keep for a volume. Deleting backups will delete the associated objects in your Azure Storage account, resulting in space saving.  

You cannot delete the latest backup.  

## Steps

1. Select **Volumes**.
2. Navigate to **Backups**.
3. From the backup list, select the backup to delete. Click the three dots (`…`) to the right of the backup, then click **Delete** from the Action menu.

    ![Screenshot that shows the Delete menu for backups.](../media/azure-netapp-files/backup-action-menu-delete.png)

## Next steps  

* 
