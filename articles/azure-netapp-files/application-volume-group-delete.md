---
title: Delete an application volume group in Azure NetApp Files  | Microsoft Docs
description: Describes how to delete an application volume group.
services: azure-netapp-files
author: b-hchen
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: how-to
ms.date: 10/20/2023
ms.author: anfdocs
---
# Delete an application volume group

This article describes how to delete an application volume group.

> [!IMPORTANT]
> You can delete a volume group only if it contains no volumes. Before deleting a volume group, delete all volumes in the group. An error occurs preventing you from deleting the volume group if it contains one or more volumes.

## Steps

1. Click **Application volume groups**.   
1. From the Volume Overview page, select the volume group you want to delete.  Select **Delete** and confirm the deletion in the popup window. 

    [![Screenshot that shows Application Volume Groups deletion.](../media/azure-netapp-files/application-volume-group-delete.png)](../media/azure-netapp-files/application-volume-group-delete.png#lightbox) 

## Next steps  

* [Application volume group FAQs](faq-application-volume-group.md)
* [Troubleshoot application volume group errors](troubleshoot-application-volume-groups.md)
