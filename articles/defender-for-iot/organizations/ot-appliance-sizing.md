---
title: Which OT appliances do I need? - Microsoft Defender for IoT
description: Learn about the deployment options for Microsoft Defender for IoT sensors and on-premises management consoles.
ms.date: 04/04/2022
ms.topic: conceptual
---

# Which appliances do I need? 
This article is designed to help you choose the right OT appliances for sensors and on-premises management consoles to fit your organization's network monitoring needs. You can use both physical or virtual appliances.

## Corporate IT/OT mixed environments

Use the following deployment sizing for high bandwidth corporate IT/OT mixed networks:


|Name  |Max throughput (OT traffic) |Max monitored Assets  |Deployment |
|---------|---------|---------|---------|
|[Corporate]    | 3 Gbps        | 12 K        |Physical / Virtual         |

## Enterprise monitoring at the site level

Use the following deployment for enterprise monitoring at the site level:

|Name  |Max throughput (OT traffic)  |Max monitored assets  |Deployment  |
|---------|---------|---------|---------|
|[Enterprise]    |1 Gbps         |10K         |Physical / Virtual         |

## Production line monitoring

Use the following deployment for production line monitoring:

|Name  |Max throughput (OT traffic)  |Max monitored assets  |Deployment  |
|---------|---------|---------|---------|
|[SMB]    | 200 Mbps        |   1,000      |Physical / Virtual         |
|[Office]    | 60 Mbps        |   800      | Physical / Virtual        |
|[Rugged]    | 10 Mbps        |   100      |Physical / Virtual|

## On-premises management console systems

On-premises management consoles are supported for enterprise deployment systems. Use the following deployment when working with an on-premises management console:

|Name  |Max monitored sensors  |Deployment  |
|---------|---------|---------|
|[Enterprise]    |Up to 300         |Physical / Virtual         |

## Next steps

Continue understanding system requirements, including options for ordering pre-configured appliances, or required specifications to install software on your own appliances:

- [Pre-configured physical appliances for OT monitoring](ot-pre-configured-appliances.md)
- [Hardware compatibility matrix for non-certified appliances](ot-physical-appliances.md)
- [Resource requirements for virtual appliances](ot-virtual-appliances.md)

Then, use any of the following procedures to continue:

- [Purchase sensors or download software for sensors](how-to-manage-sensors-on-the-cloud.md#purchase-sensors-or-download-software-for-sensors)
- [Download software for an on-premises management console](how-to-manage-the-on-premises-management-console.md#download-software-for-the-on-premises-management-console)
- [Install software](how-to-install-software.md)
