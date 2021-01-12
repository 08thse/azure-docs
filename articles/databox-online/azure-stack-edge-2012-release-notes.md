---
title: Azure Stack Edge Pro FPGA 2012 release notes | Microsoft Docs
description: Describes critical open issues and resolutions for the Azure Stack Edge 2012 release.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 01/11/2021
ms.author: alkohli
---

# Azure Stack Edge Pro FPGA 2012 release notes

The following release notes identify the critical open issues and the resolved issues for the 2012 release of Azure Stack Edge Pro FPGA.

The release notes are continuously updated. As critical issues that require a workaround are discovered, they are added. Before you deploy your Azure Stack Edge device, carefully review the information in the release notes.

This release corresponds to software version:

- **Azure Stack Edge 2007 (1.6.1280.1667)** - KB 4566549

> [!NOTE]
> Update 2012 can be applied only to devices that are running general availability (GA) versions of the software or later.

## What's new

This release contains the following bug fix:

- **Upload issue** - This release fixes an upload problem, where upload restarts caused by a failure can slow the rate of upload completion. This problem can occur when uploading a dataset that primarily consists of files that are large relative to available bandwidth, particularly, but not limited to, when bandwidth throttling is active. This change ensures sufficient opportunity for upload completion before restarting upload for a given file.<!--Is this Patch 7831646 (ID195692227: SR#: 120070124003931), addressed in the 2007 release notes?-->

This release also contains the following updates:

- All cumulative Windows updates and .NET framework updates released through October 2020.
- The baseboard management controller (BMC) firmware version is upgraded from 3.32.32.32 to 3.36.36.36 during factory install to address incompatibility with newer Dell power supply units.<!-- Verify. Does this apply only to Data Box Gateway or to the gateway feature of both DBG and Edge FPG?-->
- The static IP address for Azure Data Box Gateway is retained across software updates.
- This release supports IoT Edge 1.0.9.3 on Azure Stack Edge devices.

## Known issues in this release

No new issues are release noted for this release. All the release noted issues have carried over from the previous releases. To see a list of known issues, go to [Known issues in the GA release](data-box-gateway-release-notes.md#known-issues-in-ga-release).

## Next steps

- [Prepare to deploy Azure Stack Edge](../databox-online/azure-stack-edge-deploy-prep.md)
