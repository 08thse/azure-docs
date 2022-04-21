---
title: Migrate Virtual Machines and Virtual Machine Scale Sets to availability zone support 
description: Learn how to migrate your Azure Virtual Machines and Virtual Machine Scale Sets to availability zone support.
author: ms-anaharris
ms.service: azure
ms.topic: conceptual
ms.date: 04/21/2022
ms.author: anaharris 
ms.reviewer: anaharris
ms.custom: references_regions
---

# Migrate Virtual Machines and Virtual Machine Scale Sets to availability zone support 

This guide describes how to migrate Virtual Machines (VMs) and Virtual Machine Scale Sets (VMSS) from non-availability zone support to availability support. We'll take you through the different options for migration, including how you can use availability zone support for Disaster Recovery solutions.

Virtual Machine (VM) and Virtual Machine Scale Sets (VMSS) are zonal services, which means that VM resources can be deployed by using one of the following methods:

- VM resources are deployed to a specific, self-selected availability zone to achieve more stringent latency or performance requirements.

- VM resources are replicated to one or more zones within the region to improve the resiliency of the application and data in a High Availability (HA) architecture.

When migrating to availability zone support, it's recommended that you select multiple zones for your new VMs and VMSS, to ensure high-availability of your compute resources.

Because zonal VMs are created across the availability zones, all migration options mentioned in this article require downtime during deployment.

## Prerequisites

To migrate to availability zone support, your VM SKUs must be available for your region. To check for VM SKU availability, use one of the following methods:

-  Use Powershell to [Check VM SKU availability](../virtual-machines/windows/create-powershell-availability-zone.md#check-vm-sku-availability).
-  Use the Azure CLI to [Check VM SKU availability](../virtual-machines/linux/create-cli-availability-zone.md#check-vm-sku-availability).
-  Go to [Foundational Services](az-region.md#an-icon-that-signifies-this-service-is-foundational-foundational-services).


## Migration Option 1: Redeployment


### When to use redeployment

Use the redeployment option if you have good Infrastructure as Code (IaC) practices setup to manage infrastructure. The redeployment option gives you more control, as well as the ability to automate various processes within your deployment pipelines.

### Redeployment considerations

- When you redeploy your VM and VMSS resources, the underlying resources such as managed disk and IP address for the VM are created in the same availability zone. You must use a Standard SKU public IP address and load balancer to create zone-redundant network resources.  

- For redeployment that require reasonably low network latency and good performance between application tier and data tier, use [proximity placement groups](../virtual-machines/co-location.md) to force grouping of different VM resources under a single network spine. For an example of an SAP workload that uses proximity placement groups in zonal deployments, see [Azure proximity placement groups for optimal network latency with SAP applications](../virtual-machines/workloads/sap/sap-proximity-placement-scenarios.md)

### How to redeploy

To redeploy, you'll need to recreate your VM and VMSS resources using Azure CLI, Azure Powershell, or Azure Portal. To ensure high-availability of your compute resources, it's recommended that you select multiple zones for your new VMs and VMSS.

To learn how create VMs in an availability zone, see:

   - [Create VM using Azure CLI](../virtual-machines/linux/create-cli-availability-zone.md)
   - [Create VM using Azure Powershell](../virtual-machines/windows/create-powershell-availability-zone.md)
   - [Create VM using Azure Portal](../virtual-machines/create-portal-availability-zone.md?tabs=standard)

To learn how create VMSS in an availability zone, see [Create a virtual machine scale set that uses Availability Zones](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md).

## Migration Option 2: Azure Resource Mover

### When to use Azure Resource Mover

Use Azure Resource Mover when you want an easy and simple way to move VMs or encrypted VMs from an Azure region without availability zone support, to a different Azure region with availability zone support. If you want to learn more about the benefits of using Azure Resource Mover, see [Why use Azure Resource Mover?](../resource-mover/overview#why-use-resource-mover.md).

### Azure Resource Mover considerations

When you use Azure Resource mover, the keys and secrets are copied from the source key vault to the newly created destination key vault in your target region. All resources related to your customer-managed keys (Azure Key Vaults, disk encryption sets, VMs, disks, and snapshots) must be in the same subscription and region. Azure Key Vault’s default availability and redundancy feature can't be used as the destination key vault for the moved VM resources even if the target region is a secondary region to which your source key vault is replicated.  

### How to use Azure Resource Mover

To learn how to move VMs to another region, see [Move Azure VMs to an availability zone in another region](../resource-mover/move-region-availability-zone.md)

To learn how to move encrypted VMs to another region, see [Tutorial: Move encrypted Azure VMs across regions](../resource-mover/tutorial-move-region-encrypted-virtual-machines.md)

## Migration Option 3: Regional to Zonal Move (Preview)

### When to use Regional to Zonal Move

You can use Regional to Zonal Move to redeploy non-zonal VM resources as zonal resources within the same Azure region.  Regional to Zonal Move is a Powershell script that supports the following scenarios:

- Single Instance VMs: Regional to Zonal Move
- Individual VMs within an Availability Set: Regional to Zonal Move
- Encrypted VMs: Regional to Zonal Move

### How to use Regional to Zonal Move

You can sign up for the private preview by filing this form, [Regional to Zonal Move of Azure VMs Sign up (office.com)](https://aka.ms/AAgqi21).

## Disaster Recovery Considerations

Typically, availability zones are used to deploy VMs in a High Availability configuration. They may be too close to each other to serve as a Disaster Recovery solution in case of natural disaster.  However, there are scenarios where availability zones can be used for Disaster Recovery. To learn more, see [Using Availability Zones for Disaster Recovery](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md#using-availability-zones-for-disaster-recovery).

The following requirements should be part of a disaster recovery strategy that helps your organization run its workloads during planned or unplanned outages across zones:

- The source VM must already be a zonal VM, which means that it is placed in a logical zone.  
- You'll need to replicate your VM from one zone to another zone using Azure Site Recovery service.  
- Once your VM is replicated to another zone, you can follow steps to run a Disaster Recovery drill, fail over, re-protect, and failback.  
- To enable VM disaster recovery between availability zones, follow the instructions in [Enable Azure VM disaster recovery between availability zones](..site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md) .  

## Next Steps
