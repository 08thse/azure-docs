---
title: Microsoft Azure Stack Edge Pro 2 overview | Microsoft Docs
description: Describes Azure Stack Edge Pro 2, a storage solution that uses a physical device for network-based transfer into Azure.
services: databox
author: alkohli

ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 11/19/2021
ms.author: alkohli
#Customer intent: As an IT admin, I need to understand what Azure Stack Edge Pro GPU is and how it works so I can use it to process and transform data before sending to Azure.
---
# What is Azure Stack Edge Pro 2?

Azure Stack Edge Pro 2 is the next generation of an AI-enabled edge computing device that can transfer data over the network. This article provides you an overview of the Azure Stack Edge Pro 2 solution. The overview also details the benefits, key capabilities, and the scenarios where you can deploy this device.

Azure Stack Edge Pro 2 is a Hardware-as-a-service solution. Microsoft ships you a cloud-managed device that acts as network storage gateway. This device also has a built-in compute acceleration card that enables accelerated AI-inferencing. 

The Azure Stack Edge Pro 2 offers the following benefits over its precursor, the Azure Stack Edge Pro series:

- The Azure Stack Edge Pro 2 offers multiple SKUs that closely align with your compute acceleration, storage, and memory needs. The compute acceleration on the devices could be via a Graphical Processing Unit (GPU) or a Vision Processing Unit (VPU). 
- The Pro 2 series has flexible form factors that can be rack mounted, mounted on a wall, or even placed on a shelf in your office. 
- The Pro 2 devices have low-acoustic emissions and meet the office noise requirements.


## Use cases

Here are the various scenarios where Azure Stack Edge Pro 2 can be used for rapid Machine Learning (ML) inferencing at the edge and preprocessing data before sending it to Azure.

- **Inference with Azure Machine Learning** - With Azure Stack Edge Pro 2, you can run ML models to get quick results that can be acted on before the data is sent to the cloud. The full data set can optionally be transferred to continue to retrain and improve your ML models. For more information, see how to [Deploy Azure ML hardware accelerated models on Azure Stack Edge Pro 2](../machine-learning/how-to-deploy-fpga-web-service.md#deploy-to-a-local-edge-server).

- **Preprocess data** - Transform data before sending it to Azure via compute options such as containerized workloads and Virtual Machines to create a more actionable dataset. Preprocessing can be used to: 

    - Aggregate data.
    - Modify data, for example to remove personal data.
    - Subset data to optimize storage and bandwidth, or for further analysis.
    - Analyze and react to IoT Events. 

- **Transfer data over network to Azure** - Use Azure Stack Edge Pro 2 to easily and quickly transfer data to Azure to enable further compute and analytics or for archival purposes. 

## Key capabilities

Azure Stack Edge Pro 2 has the following capabilities:

|Capability |Description  |
|---------|---------|
|Accelerated AI inferencing| Enabled by the built-in compute acceleration card, which can be a GPU or a VPU depending on your compute needs. <br> For more information, see [GPU sharing on your Azure Stack Edge device](azure-stack-edge-gpu-sharing.md).|
|Edge computing      |Supports VM and containerized workloads to allow analysis, processing, and filtering of data. <ul><li>For information on VM workloads, see [VM overview on Azure Stack Edge](azure-stack-edge-gpu-virtual-machine-overview.md).</li> <li>For containerized workloads, see [Kubernetes overview on Azure Stack Edge](azure-stack-edge-gpu-kubernetes-overview.md)</li></ul> |
|Data access     | Direct data access from Azure Storage Blobs and Azure Files using cloud APIs for additional data processing in the cloud. Local cache on the device is used for fast access of most recently used files.|
|Cloud-managed     |Device and service are managed via the Azure portal.|
|Offline upload     | Disconnected mode supports offline upload scenarios.|
|Supported file transfer protocols      | Support for standard SMB, NFS, and REST protocols for data ingestion. <br> For more information on supported versions, see [Azure Stack Edge Pro 2 system requirements](azure-stack-edge-placeholder.md).|
|Data refresh     | Ability to refresh local files with the latest from cloud. <br> For more information, see [Refresh a share on your Azure Stack Edge](azure-stack-edge-gpu-manage-shares.md#refresh-shares).|
|Encryption    | BitLocker support to locally encrypt data and secure data transfer to cloud over *https*.|
|Bandwidth throttling| Throttle to limit bandwidth usage during peak hours. <br> For more information, see [Manage bandwidth schedules on your Azure Stack Edge](azure-stack-edge-gpu-manage-bandwidth-schedules.md).|
|Easy ordering| Bulk ordering and tracking of the device via Azure Edge Hardware Center (Preview). <br> For more information, see [Order a device via Azure Edge Hardware Center](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource).|
|Specialized network functions|Use the Marketplace experience from Azure Network Function Manager to rapidly deploy network functions such as mobile packet core, SD-WAN edge, and VPN services to an Azure Stack Edge device running in your on-premises environment. For more information, see [What is Azure Network Function Manager? (Preview)](../network-function-manager/overview.md).|
|Scale out file server|The device is available as a single node or a two-node cluster. For more information, see [What is clustering on Azure Stack Edge devices? (Preview)](azure-stack-edge-placeholder.md).|

<!--|ExpressRoute | Added security through ExpressRoute. Use peering configuration where traffic from local devices to the cloud storage endpoints travels over the ExpressRoute. For more information, see [ExpressRoute overview](../expressroute/expressroute-introduction.md).|-->


## Components

The Azure Stack Edge Pro 2 solution comprises of Azure Stack Edge resource, Azure Stack Edge Pro 2 physical device, and a local web UI.

* **Azure Stack Edge Pro 2 physical device** - A 2U rack-mounted server supplied by Microsoft that can be configured to send data to Azure.

    [!INCLUDE [azure-stack-edge-gateway-edge-hardware-center-overview](../../includes/azure-stack-edge-gateway-edge-hardware-center-overview.md)]    

    For more information, go to [Create an order for your Azure Stack Edge Pro 2 device](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource).
    
* **Azure Stack Edge resource** – A resource in the Azure portal that lets you manage an Azure Stack Edge Pro 2 device from a web interface that you can access from different geographical locations. Use the Azure Stack Edge resource to create and manage resources, view, and manage devices and alerts, and manage shares.  
   

* **Azure Stack Edge Pro 2 local web UI** - A browser-based local user interface on your Azure Stack Edge Pro 2 device primarily intended for the initial configuration of the device. Use the local web UI also to run diagnostics, shut down and restart the device, or view copy logs. 

    [!INCLUDE [azure-stack-edge-gateway-local-web-ui-languages](../../includes/azure-stack-edge-gateway-local-web-ui-languages.md)]
    
    For information about using the web-based UI, go to [Use the web-based UI to administer your Azure Stack Edge](azure-stack-edge-manage-access-power-connectivity-mode.md).

## Region availability

Azure Stack Edge Pro GPU physical device, Azure resource, and target storage account to which you transfer data don’t all have to be in the same region.

- **Resource availability** - For this release, the resource is available in East US, West EU, and South East Asia regions.

- **Device availability** - You need to sign up for the preview. The Azure Stack Edge team will then enable your subscription. You should be able to see Azure Stack Edge Pro 2 as one of the available SKUs when placing the order. The countries where the device is available are: United States, Canada, Germany, Italy, Norway, Sweden, Denmark, and Japan. 

    For a list of all the countries/regions where the Azure Stack Edge Pro GPU device is available, go to **Availability** section in the **Azure Stack Edge Pro** tab for [Azure Stack Edge Pro GPU pricing](https://azure.microsoft.com/pricing/details/azure-stack/edge/#azureStackEdgePro).
    
- **Destination Storage accounts** - The storage accounts that store the data are available in all Azure regions. The regions where the storage accounts store Azure Stack Edge Pro GPU data should be located close to where the device is located for optimum performance. A storage account located far from the device results in long latencies and slower performance.

Azure Stack Edge service is a non-regional service. For more information, see [Regions and Availability Zones in Azure](../availability-zones/az-overview.md). Azure Stack Edge service doesn’t have dependency on a specific Azure region, making it resilient to zone-wide outages and region-wide outages.

For a discussion of considerations for choosing a region for the Azure Stack Edge service, device, and data storage, see [Choosing a region for Azure Stack Edge](azure-stack-edge-gpu-regions.md).

## Next steps

- Review the [Azure Stack Edge Pro GPU system requirements](azure-stack-edge-gpu-system-requirements.md).

- Understand the [Azure Stack Edge Pro GPU limits](azure-stack-edge-limits.md).

- Deploy [Azure Stack Edge Pro GPU](azure-stack-edge-gpu-deploy-prep.md) in Azure portal.
