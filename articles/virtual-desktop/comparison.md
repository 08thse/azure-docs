---
title: Azure Virtual Desktop and Microsoft 365 comparison - Azure
description: A comparison between Microsoft 365 and Azure Virtual Desktop.
author: Heidilohr
ms.topic: conceptual
ms.date: 11/09/2021
ms.author: helohr
manager: femila
---

# Azure Virtual Desktop vs. Microsoft 365

Intro text goes here!

## Technical architecture

| Feature | Windows 365 Enterprise | Windows 365 Business | Azure Virtual Desktop (personal) | Azure Virtual Desktop (pooled) |
|-------|--------|--------|--------|---------|
|Design|Designed to be simple and easy to use.|Designed to be simple and easy to use.|Designed to be flexible.|Designed to be flexible.|
|Type of desktop|Personal desktop|Personal desktop|Pooled (single and multi-session) desktop|Pooled (single and multi-session) desktop|
|Management|Microsoft-managed (except networking)|Fully Microsoft-managed|Customer-managed|Customer-managed|
|VM SKUs|Multiple optimized options for a range of use cases|Multiple optimized options for a range of use cases|Any Azure virtual machine (VM)|Any Azure VM|
|Backup|Local redundant storage for DR (what is this?)|Local redundant storage for DR (what is this?)|Azure backup services|Azure backup services|
|Networking|Customer-managed|Microsoft-managed|Customer-managed|Customer-managed|
|Identity|Hybrid join, Azure Active Directory (AD) join (preview)|Azure AD join (preview)|Domain join with Active Directory Domain Services (AD DS) or Azure AD DS, Hybrid Azure AD join or Azure AD join (preview)|Domain join (AD DS or Azure AD DS), Hybrid Azure AD join or Azure AD join (preview)|
|User profiles|Local redundant storage for user profiles|Local redundant storage for user profiles|Local redundant storage for user profiles, or FSLogix profiles that can be stored locally in Azure Files or Azure NetApp Files|Local redundant storage for user profiles, or FSLogix profiles that can be stored locally in Azure Files or Azure NetApp Files|
|Operating systems|Windows 10 Enterprise (single session)|Windows 10 Enterprise (single session)|Windows 10 Enterprise (single session and multi-session) <br>Windows Server 2012 R2, 2016, 2019 (single session and multi-session)<br>Windows 7 Enterprise (single session)|Windows 10 Enterprise (single session and multi-session) <br>Windows Server 2012 R2, 2016, 2019 (single session and multi-session)<br>Windows 7 Enterprise (single session)|
|Base image|Custom and Microsoft-provided|Microsoft-provided only|Custom and Microsoft-provided|Custom and Microsoft-provided|
|VM location|Most geographies|Most geographies|[Any Azure region](data-locations.md)|[Any Azure region](data-locations.md)|
|Remote app streaming|Not supported|Not supported|Supported|Supported|

## Deployment and management

Intro text goes here!

| Feature | Windows 365 Enterprise | Windows 365 Business | Azure Virtual Desktop (personal) | Azure Virtual Desktop (pooled) |
|-------|--------|--------|--------|---------|
|Hybrid (on-premises) or multi-cloud support|Not supported|Not supported|Supported with Citrix and VMware cloud integration|Supported with Citrix and VMware cloud integration|
|Management portal|Microsoft Endpoint Manager|End-user portal|Azure portal (deploy and manage), Microsoft Endpoint Manager (manage only)|Azure portal (deploy and manage), Microsoft Endpoint Manager (manage only)|
|Image management|Custom images and Microsoft-managed image management| Microsoft-managed image management only|Custom images and Microsoft-managed image management|Custom images and Microsoft-managed image management|
|Screen capture protection|Yes (feature currently in preview)|Yes (feature currently in preview)|Yes (feature currently in preview)|Yes (feature currently in preview)|
|Patches|Microsoft Endpoint Manager|Microsoft Endpoint Manager|Other Microsoft solutions|Other Microsoft solutions|
|Autoscaling|Not required due to fixed monthly cost|Not required due to fixed monthly cost|Supported with Azure Logic Apps|Supported with Azure Logic Apps|
|Application delivery|Microsoft Endpoint Manager or custom images|Microsoft Endpoint Manager or custom images|Microsoft Endpoint Manager, MSIX app attach, custom images, or Microsoft-approved partner solutions|Microsoft Endpoint Manager, MSIX app attach, custom images, or Microsoft-approved partner solutions|
|Monitoring|Endpoint Analytics|Endpoint Analytics|Azure Monitor|Azure Monitor|
|Environment validation|Built-in network configuration watchdog service|Built-in network configuration watchdog service|Configured manually with Azure Advisor|Configured manually with Azure Advisor|
|App lifecycle management|Same as physical PC (MEM, SSCM, MSI, EXE, MSIX, App-V, and so on)|Same as physical PC (MEM, SSCM, MSI, EXE, MSIX, App-V, and so on)|Same as physical PC (MEM, SCCM, MSI, EXE, MSIX, App-V, and so on) with MSIX app attach or partner solutions|Same as physical PC (MEM, SCCM, MSI, EXE, MSIX, App-V, and so on) with MSIX app attach or partner solutions|

## User experience

Intro text goes here.

| Feature | Windows 365 Enterprise | Windows 365 Business | Azure Virtual Desktop (personal) | Azure Virtual Desktop (pooled) |
|-------|--------|--------|--------|---------|
|Client|Windows, Mac, iOS, Android, HTML, Linux SDK|Windows, Mac, iOS, Android, HTML, Linux SDK|Windows, Mac, iOS, Android, HTML, Linux SDK|Windows, Mac, iOS, Android, HTML, Linux SDK|
|Printing|Universal print and print redirection support|Universal print and print redirection support|Universal print and print redirection support|Print redirection only|
|Protocol|RDP|RDP|RDP|RDP|
|End-user portal capabilities|User sign in, start VM, troubleshooting, restart, rename and profile reset, VM and disk resizing, OS choice|User sign in, start VM, troubleshooting, restart, rename and profile reset, VM and disk resizing, OS choice|IT uses the Azure portal to manage deployments|IT uses the Azure portal to manage deployments|

## Licensing and pricing

Intro text goes here.

| Feature | Windows 365 Enterprise | Windows 365 Business | Azure Virtual Desktop (personal) | Azure Virtual Desktop (pooled) |
|-------|--------|--------|--------|---------|
|License costs|Monthly per-user pricing|Monthly per-user pricing|Use existing internal license (internal users only) or use monthly per-user access pricing (for commercial remote app streaming to external users only)|Use existing internal license (internal users only) or use monthly per-user access pricing (for commercial remote app streaming to external users only)|
|Infrastructure costs|Included except for egress charges over base quota|Included|Based on consumption|Based on consumption|
|Microsoft Endpoint Manager|Required|Optional|Optional|Optional|

- For more Azure Virtual Desktop pricing details, see [Azure Virtual Desktop pricing](https://azure.microsoft.com/pricing/details/virtual-desktop/).
- For more Windows 365 pricing details, see [Windows 365 plans and pricing](https://www.microsoft.com/windows-365/all-pricing).
