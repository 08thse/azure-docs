---
title: Data retention across Microsoft Defender for IoT
description: Learn about the data retention periods and capacities for Microsoft Defender for IoT data stored in Azure, the OT sensor, and on-premises management console.
ms.topic: reference
ms.date: 12/14/2022
---

# Data retention across Microsoft Defender for IoT

Microsoft Defender for IoT stores data in the Azure portal, on OT network sensors, and on-premises management consoles.

Each storage location affords a certain storage capacity and retention times. This article describes how  much and how long each type of data is stored in each location before it's either deleted or overridden.

## Devices retention periods

| Storage type | Details |
|---------|---------|
| **Azure portal** | Device inventory data is stored for 90 days from last seen/activity field. <br><br> For more information, see [Manage your device inventory from the Azure portal](how-to-manage-device-inventory-for-organizations.md). |
| **OT network sensor** | Device inventory data is stored for 90 days, for all sensors from sensor version 22.3 minor and up. <br><br> For more information, see [Manage your OT device inventory from a sensor console](how-to-investigate-sensor-detections-in-a-device-inventory.md). |
| **On-promises management console** | Device inventory data is stored for 90 days, depending on the sensor. <br><br> For more information, see [Manage your OT device inventory from an on-premises management console](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md). |

## Alert data retention

Alert data is retained as listed below, regardless of the alert's status, or whether it's been learned or muted.

| Storage type | Details |
|---------|---------|
| **Azure portal** | Alerts are stored on the Azure portal for 90 days from their first detection time. <br><br> For more information, see [View and manage alerts from the Azure portal](how-to-manage-cloud-alerts.md). |
| **OT network sensor** | Alerts are stored on the OT sensor for 90 days from their first detection time. <br><br> For more information, see [View alerts on your sensor](how-to-view-alerts.md). |
| **On-premises management console** |  Alerts are stored on the on-premises management console for 90 days from their first detection time. <br><br> For more information, see [Work with alerts on the on-premises management console](how-to-work-with-alerts-on-premises-management-console.md). |

### OT alert PCAP data retention

| Storage type | Details |
|---------|---------|
| **Azure portal** | PCAP files are available for download from the Azure portal for as long as the OT network sensor stores them. <br><br> Once downloaded, the files are cached on the Azure portal for 48 hours. <br><br> For more information, see [Access alert PCAP data (Public preview)](how-to-manage-cloud-alerts.md#access-alert-pcap-data-public-preview). |
| **OT network sensor** | PCAP files are stored on the OT sensor for up to 90 days, depending on the sensor's storage capacity. <br><br> Maximum size of filtered PCAPs allowed is set by default to 133,120 MB, but configurable in the `filtered.cache.dir.size.megabytes.max` property in the *pcap.properties* file.<br> If you exceed this size, the oldest backed-up file is deleted to accommodate the new one. <br><br> For more information, see [Download PCAP files](how-to-view-alerts.md#download-pcap-files). |
| **On-promises management console** | PCAP files aren't stored on the on-premises management console. <br><br> Access PCAP files from the on-premises management console using a direct link to the sensor, for as long as the on premises sensor stores them. |

## Security recommendation retention

Defender for IoT security recommendations are stored only on the Azure portal.

Recommendations are stored for 90 days from the their first detection time.

For more information, see [Enhance security posture with security recommendations](recommendations.md).

## OT event timeline retention

OT event timeline data is stored on OT network sensors only, and differs depending on the sensor's [hardware profile](ot-appliance-sizing.md).

The following table lists the maximum number of events that can be stored for each hardware profile:

| Hardware profile | Number of events |
|---------|---------|
| C5600 | 10M events |
| E1800 | 10M events |
| E1000 | 6M events |
| E500 | 6M events |
| L500 | 3M events |
| L100 | 500-K events |
| L60 | 500-K events |

For more information, see [Track sensor activity](how-to-track-sensor-activity.md).

## OT log file retention

Only service and processing log files are stored on the Azure portal, and are retained for 30 days.

Other OT network monitoring log files are stored only on the OT network sensor and on-premises management console.

On both the OT sensor and the on-premises management console, older log files are overridden when the appliance's storage has reached its maximum capacity. Log file sizes differ depending on the amount of content, but the average size per log file is 100-150 MB.

For more information, see:

- [Troubleshoot the sensor and on-premises management console](how-to-troubleshoot-the-sensor-and-on-premises-management-console.md).
- [Download a diagnostics log for support](how-to-manage-individual-sensors.md#download-a-diagnostics-log-for-support).

## On-premises backup file capacity

Both the OT network sensor and the on-premises management console have automated backups running daily, which are stored as follows:

| Storage type | Details |
|---------|---------|
| **OT network sensor** | The maximum size of sensor backup files stored on the sensor itself is set by default to 100 GB, but configurable in the `max_directory_size_in_gb` property in the *backup.properties.configurable* file. <br><br> Older backup files are deleted if the total backup file size passes this limit. <br><br> However, each sensor also has its own, extra backup directory on the on-premises management console. <br><br> For more information, see [Set up backup and restore files](how-to-manage-individual-sensors.md#set-up-backup-and-restore-files). |
| **On-promises management console** | The following types of backup files are stored on the on-premises management console, each with their own maximum file size: <br><br> - **On-premises management console backup file**: Set by default to 10 GB, but configurable in the `backup.max_directory_size.gb` property in the *backup.properties.configurable* file.<br> - **OT sensor backup files**: Set by default to 40 GB, but configurable in the `sensors_backup.total_size_allowed.gb` property in the *backup.properties.configurable* file. <br><br> For more information, see [Set up backup and restore files](how-to-manage-individual-sensors.md#set-up-backup-and-restore-files)|

For more information, see:

- [Configure backup settings for an OT network sensor](how-to-manage-individual-sensors.md#set-up-backup-and-restore-files)
- [Configure OT sensor backup settings from an on-premises management console](how-to-manage-sensors-from-the-on-premises-management-console.md#backup-storage-for-sensors)
- [Configure backup settings for an on-premises management console](how-to-manage-the-on-premises-management-console.md#define-backup-and-restore-settings)

## Next steps

For more information, see:

- [Manage individual OT network sensors](how-to-manage-individual-sensors.md)
- [Manage OT network sensors from an on-premises management console](how-to-manage-sensors-from-the-on-premises-management-console.md)
- [Manage an on-premises management console](how-to-manage-the-on-premises-management-console.md)
