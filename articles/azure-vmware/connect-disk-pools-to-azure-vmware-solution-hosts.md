---
title: Connect disk pools to Azure VMware Solution hosts
description: Learn how to scale Azure VMware Solution hosts using disk pools instead of scaling clusters. You can use block storage for active working sets, which is an extension of vSAN, and to tier less frequently accessed data from vSAN to disks. You can also replicate data from on-premises or primary VMware environment to disk storage for the secondary site.
ms.topic: how-to 
ms.date: 06/28/2021

#Customer intent: As an Azure service administrator, I want to scale my AVS hosts using disk pools instead of scaling clusters. So that I can use block storage for active working sets, which is an extension of vSAN, and to tier less frequently accessed data from vSAN to disks. I can also replicate data from on-premises or primary VMware environment to disk storage for the secondary site.  

---

# Connect disk pools to Azure VMware Solution hosts

[Azure disk pools](../virtual-machines/disks-pools.md) offer persistent block storage to applications and workloads backed by Azure Disks. You can use disks as the persistent storage for Azure VMWare Solution for optimal cost and performance. For example, if you host data-intensive workloads like Oracle databases, you can scale up by using disk pools instead of scaling clusters. You can also use disks to replicate data from on-premises or primary VMware environment to disk storage for the secondary site.

>[!TIP]
>To scale storage independent of the Azure VMware Solution hosts, we support surfacing Azure and [Premium Disks](/azure/virtual-machines/disks-types#premium-ssd) as the datastores.

Azure Disks are attached to the managed iSCSI controller, a virtual machine deployed under the managed resource group. Disks get deployed as storage targets to a disk pool, and each storage target shows as an iSCSI LUN under the iSCSI target. You can expose a disk pool as an iSCSI target connected to Azure VMware Solution hosts as a datastore. A disk pool surfaces as a single endpoint for all underlying disks added as storage targets. Each disk pool can have only one iSCSI controller.



Disk pool in Azure VMware Solution only supports the following regions:

- East US

- Canada Central

- West US 2

You can only connect the disk pool to an Azure VMware Solution private cloud in the same region. If your private cloud is deployed in non-supported regions, you can redeploy in a supported region. Azure VMware Solution private cloud and disk pool colocation provide the best performance with minimal network latency.


The diagram shows how disk pools work with Azure VMware Solution. Each iSCSI controller can access each ultra disk over iSCSI, and the Azure VMware Solution hosts can access the iSCSI controller over iSCSI.

:::image type="content" source="media/disk-pools/azure-disks-attached-to-managed-iscsi-controllers.png" alt-text="Diagram depicting how disk pools works, each ultra disk can be accessed by each iSCSI controller over iSCSI, and the Azure VMware Solution hosts can access the iSCSI controller over iSCSI." border="false":::


In this article, you'll learn how to:

- Add an Azure VMware Solution private cloud as an iSCSI initiator that allows access to the disk pool over iSCSI protocol.

- Connect to a disk pool surfaced through an iSCSI target as the VMware datastore of an Azure VMware Solution private cloud.

- Create a VMware instance in Azure VMware Solution with storage volume created on the datastore backed by Azure disk pool.



## Prerequisites

- Identify the scalability and performance requirements of your workloads. For details, see [Planning for Azure disk pools](../ virtual-machines/disks-pools-planning.md).

- Deploy [Azure VMware Solution private cloud](deploy-azure-vmware-solution.md) with a [virtual network configured](deploy-azure-vmware-solution.md#step-3-connect-to-azure-virtual-network-with-expressroute). For more information, see [Network planning checklist](tutorial-network-checklist.md) and [Configure networking for your VMware private cloud](tutorial-configure-networking.md).

   - If you select Ultra Disks, use either the Ultra Performance or ErGw3AZ (10Gbps) SKU for the Azure VMware Solution private cloud and then [enable ExpressRoute FastPath](/azure/expressroute/expressroute-howto-linkvnet-arm#configure-expressroute-fastpath).

   - If you select Premium SSD Managed Disks, use either the Standard (1Gbps) or High Performance (2Gbps) SKU for the Azure VMware Solution private cloud.

- Deploy and configure a disk pool with UItra Disks or Premium SSD Disks as the backing storge and expose the disk pool as an iSCSI target with each disk as an individual LUN. For details, see [Deploy an Azure disk pool](../virtual-machines/disks-pools-deploy.md).

   >[!IMPORTANT]
   > The disk pool must be deployed in the same subscription as the VMware cluster, and it must be attached to the same VNET as the VMware cluster.


## Add private cloud as an iSCSI initiator

[when would you do this and why?]

- Add an Azure VMware Solution private cloud as an iSCSI initiator to allow access to the disk pool over iSCSI protocol.


## Connect to a disk pool surfaced through an iSCSI target

[when would you do this and why?]

- Connect to a disk pool surfaced through an iSCSI target as the VMware datastore of an Azure VMware Solution private cloud.


## Create VMware instance with storage volume

[when would you do this and why?]

- Create a VMware instance in Azure VMware Solution with storage volume created on the datastore backed by Azure Disk Pool.


## Next steps

Now that you've created a disk pool and connected it to Azure VMware Solution hosts, you may want to learn about:

- [Managing an Azure disk pool](../virtual-machines/disks-pools-manage.md ).  Once you've deployed a disk pool, there are various management actions available to you. You can add or remove a disk to or from a disk pool, Update iSCSI LUN mapping, or add ACLs (Only applicable if ACL mode is set to Static).

- [Deleting a disk pool](/azure/virtual-machines/disks-pools-deprovision#delete-a-disk-pool). When you delete a disk pool, all the resources in the managed resource group are also deleted.

- [Disabling iSCSI support on a disk](/azure/virtual-machines/disks-pools-deprovision#disable-iscsi-support). If you disable iSCSI support on a disk pool, you effectively can no longer use a disk pool.

- [Moving disk pools to a different subscription](/azure/virtual-machines/disks-pools-move-resource). Moving a disk pool involves moving the disk pool itself, the disks contained in the disk pool, the disk pool's managed resource group, and all the resources contained in the managed resource group.


