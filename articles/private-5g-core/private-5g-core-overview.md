---
title: Azure Private 5G Core Preview
description: An overview of Azure 5G Core Preview - an Azure cloud service for service providers and system integrators to securely deploy and manage private mobile networks for enterprises on Azure Arc-connected edge platforms such as an Azure Stack Edge device. 
author: djrmetaswitch
ms.author: drichards
ms.service: private-5g-core
ms.topic: overview 
ms.date: 12/22/2021
ms.custom: template-overview
---

# What is Azure Private 5G Core Preview?

Azure Private 5G Core Preview is an Azure cloud service for service providers and system integrators to securely deploy and manage private mobile networks for enterprises on Azure Arc-connected edge platforms such as an Azure Stack Edge device. The private mobile network provides high performance, low latency, and secure connectivity for 5G Internet of Things (IoT) devices on the enterprise's premises.

Azure Private 5G Core enables a single private mobile network distributed across one or more sites around the world, with each site containing a packet core instance deployed on an Azure Stack Edge device.

Each packet core instance is a cloud native implementation of the 3GPP standards-defined 5G Next Generation Core (5G NGC or 5GC), which authenticates end devices and aggregates their data traffic over 5G Standalone wireless and access technologies. It comprises a high performance and highly programmable 5G user plane function (UPF), core control plane functions including policy and subscriber management, a portfolio of service-based architecture elements and management components for network monitoring.

Each packet core instance is standards compliant and compatible with several Radio Access Network (RAN) partners in the Azure private multi-access edge compute (MEC) ecosystem. See [What is Azure private multi-access edge compute](/azure/private-multi-access-edge-compute-mec/overview) for more information on Azure private MEC.

Azure Private 5G Core allows you to use Azure to deliver and automate the lifecycle of packet core instances on Azure Stack Edge devices, manage configuration, set policies, provision SIMs for User Equipment and monitor the network.

## Why use Azure Private 5G Core?

### Deployment at the enterprise edge

The key component of Azure Private 5G Core is the deployment of packet core instances on an Azure Stack edge infrastructure on the enterprise premises targeted for 5G coverage.

Deploying a packet core instance at the enterprise edge ensures complete ownership of all data by the enterprise. It also ensures the packet core instance is as close as possible to the devices it serves and is not reliant on any cloud connectivity, allowing it to deliver low latency levels and reduced backhaul through local data processing when combined with application logic in the same location. This provides a number of valuable benefits to enterprises, including the following.

- **Machine to machine automation** - Ultra Reliable Low Latency Connectivity (URLLC) for command and control messages from automated systems (like robots or automated guide vehicles) can be processed in real time to prevent stalling, ensuring high productivity.
- **Massive IoT telemetry** - secure cloud connectivity for data collection from a large density and volume of IoT sensors and devices, ensuring that data for health assessment and the operation of automated systems can be processed in real time to prevent accidents and ensure on-site safety.
- **Real time analytics** - local processing of real-time operational and diagnostics data such as live video feeds can be AI processed at the edge at minimal expense, ensuring that vital actions are not delayed.

:::image type="content" source="media/azure-private-5g-core/enterprise-edge-latency.png" alt-text="Diagram showing low latency levels for services deployed on Azure Stack Edge devices compared to the Azure / Network Edge and Azure Hyperscale Cloud.":::

Azure Private 5G Core is able to leverage this low latency with the security and high bandwidth offered by private 5G networks, putting it in the optimal position to support Industry 4.0 use cases such as the following.

- **Manufacturing** - Production-line analytics and warehouse automation with robots.
- **Public safety** - Mobility and connectivity for emergency workers and disaster recovery operatives.
- **Energy and utilities** - Backhaul networks for Smart Meters and network slicing/control.
- **Defense** - Connected command posts and battlefield with real-time analytics.
- **Smart farms** - Connected equipment for farm operation.

Each packet core instance is connected to the local 5G Standalone RAN network to provide coverage for the 5G devices. You can choose to limit these 5G devices to local connectivity, or provide multiple routes to the cloud, Internet, or other enterprise datacenters running IoT and automation applications.

### Native Azure service management

Azure Private 5G Core is available as a native Azure service, offering the same levels of reliability, security and availability for deployment and management that are key tenets of all Azure services. This allows you to use the Azure portal and Azure Resource Manager (ARM) APIs to do any of the following from anywhere in the world.

- Deploy and configure a packet core instance on your Azure Stack Edge device in minutes.
- Create a virtual representation of your physical mobile network through Azure using Mobile Network Site resources.
- Provision SIM resources to authenticate 5G devices in the network, while also supporting redundancy in case of failure.
- Employ Log Analytics and other observability services to view the health of your network and take corrective action through Azure.
- Leverage Azure's Role Based Access Control (RBAC) policies to allow granular access to the private mobile network to different personnel or teams within your organization, or even a managed service provider.
- Use an Azure Stack Edge device's compute capabilities to run applications that can benefit from low-latency networks.
- Seamlessly connect your existing Azure deployments to your new private mobile network using Azure's hybrid compute, networking and IoT services.
- Access the large ecosystem of Microsoft Independent Software Vendor (ISV) partners for applications and network functions.
- Utilize Azure Lighthouse and the Azure Expert Managed Services Provider (MSP) program to simplify the end-to-end deployment of a private mobile network through Azure.

### Flexible integration with other 5G solution components

Azure Private 5G Core exposes an N2 and N3 interface for the control plane and user plane respectively. It complies with the following 3GPP Technical Specifications, allowing you to integrate with a wide range of Radio Access Network (RAN) models.

- [TS 38.413](https://portal.3gpp.org/desktopmodules/Specifications/SpecificationDetails.aspx?specificationId=3223) for the N2 interface.
- [TS 29.281](https://portal.3gpp.org/desktopmodules/Specifications/SpecificationDetails.aspx?specificationId=1699) for the N3 interface.

It also employs a simple, scalable provisioning model to allow you to bring the SIM partner of your choice to Azure.

### Log Analytics integration

Azure Private 5G Core is integrated with the Azure Log Analytics tool, as described in [Overview of Log Analytics in Azure Monitor](/azure/azure-monitor/logs/log-analytics-overview). You can write queries to retrieve records or visualize data in charts, allowing you to monitor and analyze activity in your private mobile network directly from the Azure portal.

:::image type="content" source="media/azure-private-5g-core/log-analytics-tool.png" alt-text="Log analytics tool showing a query made on devices registered with the private mobile network.":::

### Distributed tracing and KPI metrics

Azure Private 5G Core provides proactive, real-time analysis of all message traffic, including NGAP/NAS messages and HTTP requests and responses. You can use the distributed tracing web GUI to collect detailed traces for signaling flows involving packet core instances. These can be used to diagnose many common configuration, network, and interoperability problems affecting user service.

:::image type="content" source="media/azure-private-5g-core/distributed-tracing-web-gui.png" alt-text="Distributed tracing web GUI displaying details of a successful PDU session establishment.":::

Azure Private 5G Core is also integrated with industry standard cloud native monitoring tools, such as Prometheus and Grafana, allowing for real-time analysis of system performance, fault identification and troubleshooting. You can use a number of packet core dashboards to monitor key metrics relating to your private mobile network. They also allow you to view information on firing alerts, ensuring you can react quickly to emerging issues.

:::image type="content" source="media/azure-private-5g-core/packet-core-dashboards.png" alt-text="Packet core dashboards with panels displaying metrics including registered devices and PDU sessions.":::



### 5GC features

|Feature  |Description  |
|---------|---------|
|**Supported 5G procedures**|Azure Private 5G Core complies with 3GPP TS 23.502 for the following procedures when operating as part of a wider Private 5G solution.<br><ul><li>UE registration / deregistration</li><li>Mobility Registration Update / Periodic Registration Update</li><li>UE Initiated Service Request (Signaling / Data)</li><li>AN / Network Initiated UE Context Release</li><li>PDU Session Establishment</li><li>PDU Session Release</li><li>Inter NG-RAN node N2 based handover</li><li>Xn based inter NG-RAN handover</li><li>Network Initiated Downlink Data Notification / Paging</li></ul>|
|**UE authentication**|<ul><li>Security Anchor Function (SEAF) support to provide authentication functionality in the serving network.</li><li>Authentication using Subscription Permanent Identifiers (SUPI) and Globally Unique Temporary Identities (5G-GUTI).</li><li>Assignment or reallocation of a 5G-GUTI to a UE.</li><li>5G Authentication and Key Agreement (5G-AKA) for mutual authentication between UEs and the network.</li></ul>|
|**UE Security Context Management**|The packet core instance performs ciphering and integrity protection of 5G NAS. During UE registration, the UE includes its security capabilities for 5G NAS with 128-bit keys.<br>The algorithms support by Azure Private 5G Core for ciphering and integrity protection include the following.<ul><li>5GS null encryption algorithm</li><li>128-bit Snow3G</li><li>128-bit AES</li><li>128-bit ZUC</li></ul>|
|**UE MTU configuration**|The packet core instance signals the MTU for a data network to UEs on request as part of PDU session Establishment procedures to avoid fragmentation.|
|**Index to RAT/Frequency Selection Priority (RFSP)**|The packet core instance can provide a RAN with an RFSP Index, which the RAN can match to its local configuration to apply specific Radio Resource Management policies, such as cell reselection or frequency layer redirection.|

<!-- DJR - do we want to expose the inner architecture of a packet core instance? -->

## Azure services consumed by Azure Private 5G Core

Azure Private 5G Core includes and utilizes the following Azure services.

- **Azure Cloud Services** - you can deploy and manage your private mobile network using the cloud, as described in [Native Azure service management](#native-azure-service-management).
- **Azure Stack Edge** - each packet core instance must be deployed on an Azure Stack Edge Pro with GPU.
- **Azure Network Functions Manager (NFM)** - Azure NFM allows you to deploy a packet core instance to your Azure Stack Edge device using consistent Azure tools and interfaces. For more information, see [Azure Network Function Manager](/azure/network-function-manager/overview).
- **Arc Enabled Kubernetes** - each packet core instance runs on a Kubernetes cluster. With Azure Arc enabled Kubernetes, you can attach and configure this cluster directly through Azure tools and interfaces. For more information, see [Azure Arc](https://azure.microsoft.com/services/azure-arc/).

## Next steps
<!--
DJR - we should include appropriate links for the deployment process and for SIM / policy provisioning as well ##
-->
- [Learn more about the prerequisites for deploying a private mobile network](complete-private-mobile-network-prerequisites.md)