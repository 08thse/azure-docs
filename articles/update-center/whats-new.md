---
title: What's new in Update management center
description: Learn about what's new and recent updates in the Update management center service.
ms.service: update-management-center
ms.topic: overview
author: SnehaSudhirG
ms.author: sudhirsneha
ms.date: 06/12/2023
---

# What's new in Update management center

[Update management center](overview.md) helps you manage and govern updates for all your machines. You can monitor Windows and Linux update compliance across your deployments in Azure, on-premises, and on the other cloud platforms from a single dashboard. This article summarizes new releases and features in Update management center.

## July 2023

### Dynamic scoping (preview)

You can now include virtual machines based on the scope and schedule updates at scale. You also have the flexibility to modify the scope and the patching requirements are applied at scale without any changes to your deployment schedule.

## May 2023

### Customized image support

Update management center now supports [generalized](../virtual-machines/linux/imaging.md#generalized-images) custom images, and a combination of offer, publisher, and SKU for Marketplace/PIR images.See the [list of supported operating systems](support-matrix.md#supported-operating-systems). 

### Multi-subscription support

The limit on the number of subscriptions that you can manage using the Update management center portal has now been removed. You can now manage all your subscriptions using the update management center portal.

## April 2023

### New prerequisite for scheduled patching

A new patch orchestration - **Customer Managed Schedules (Preview)** is introduced as a prerequisite to enable scheduled patching on Azure VMs. The new patch enables the *Azure-orchestrated* and *BypassPlatformSafteyChecksOnUserSchedule* VM properties on your behalf after receiving the consent. [Learn more](prerequsite-for-schedule-patching.md).

> [!IMPORTANT]
> For a seamless scheduled patching experience, we recommend that for all Azure VMs, you update the patch orchestration to **Customer Managed Schedules (Preview)**. If you fail to update the patch orchestration, you can experience a disruption in business continuity because the schedules will fail to patch the VMs.


## November 2022

### New region support

Update management center (Preview) now supports new five regions for Azure Arc-enabled servers. [Learn more](support-matrix.md#supported-regions).

## October 2022

### Improved on-boarding experience

You can now enable periodic assessment for your machines at scale using [Policy](periodic-assessment-at-scale.md) or from the [portal](manage-update-settings.md#configure-settings-on-single-vm).


## Next steps

- [Learn more](support-matrix.md) about supported regions.