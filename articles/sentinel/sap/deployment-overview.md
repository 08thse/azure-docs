---
title: Deploy SAP continuous threat monitoring | Microsoft Docs
description: Learn how to deploy the Microsoft Sentinel solution for SAP environments.
author: MSFTandrelom
ms.author: andrelom
ms.topic: how-to
ms.date: 02/01/2022
---

# Deploying SAP continuous threat monitoring

[!INCLUDE [Banner for top of topics](../includes/banner.md)]

This article takes you step by step through the process of deploying Microsoft Sentinel continuous threat monitoring for SAP.

> [!IMPORTANT]
> The Microsoft Sentinel SAP solution is currently in PREVIEW. The [Azure Preview Supplemental Terms](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) include additional legal terms that apply to Azure features that are in beta, preview, or otherwise not yet released into general availability.

## Overview

[Microsoft Sentinel solutions](../sentinel-solutions.md) includes bundled security content, such as threat detections, workbooks, and watchlists. With these solutions, you can onboard Microsoft Sentinel security content for a specific data connector by using a single process.

By using the Microsoft Sentinel SAP data connector, you can monitor SAP systems for sophisticated threats within the business and application layers.

The SAP data connector streams 14 application logs from the entire SAP system landscape. The data connector collects logs from Advanced Business Application Programming (ABAP) via NetWeaver RFC calls and from file storage data via OSSAP Control interface. The SAP data connector adds to the ability of Microsoft Sentinel to monitor the SAP underlying infrastructure.

To ingest SAP logs into Microsoft Sentinel, Microsoft Sentinel SAP data connector needs to be installed and connected to SAP environment. Microsoft Sentinel SAP data connector is packaged as a Docker container. For the deployment, it is recommended to deploy the container onto an Azure virtual machine, however deployment to a an on-premise physical or virtual machine is also supported.

After the SAP data connector is deployed, deploy the  SAP solution security content to gain insight into organization's SAP environment and improve any related security operation capabilities.

In this series of articles, you'll learn how to:


- Prepare your SAP system for the SAP data connector deployment.
- Deploy the SAP data connector.
- Deploy the SAP solution security content in Microsoft Sentinel.
- Configure auditing
- Configure data connector to connect to SAP using SNC and X.509 certificate authentication

> [!NOTE]
> Extra steps are required to configure communications between SAP data connector and SAP over a Secure Network Communications (SNC) connection. This is covered in [Deploy the Microsoft Sentinel SAP data connector with SNC](configure_snc.md) section of the guide.


## Deployment milestones
Deployment of the SAP continuous threat monitoring solution is divided into the following sections

1. Deployment overview (*You are here*)

1. [Prerequisites](prerequisites-for-deploying-sap-continuous-threat-monitoring.md)

1. [Deploy SAP CRs and configure Authorization](deploying-required-sap-crs.md)

1. [Define a role configuration and create a user account](role_configuration.md)

1. [Deploy and configure the data connector agent container](deploy_data_connector_agent_container.md)

1. [Deploy SAP security content](deploy_sap_security_content.md)

1. [Optional deployment steps](optional_deployment_steps.md)
   - [Configure auditing](configure_audit.md)
   - [Configure SAP data connector to use SNC](configure_snc.md)


## Next steps

Begin the deployment of SAP continuous threat monitoring solution by reviewing the Prerequisites
> [!div class="nextstepaction"]
> [Prerequisites](prerequisites-for-deploying-sap-continuous-threat-monitoring.md)