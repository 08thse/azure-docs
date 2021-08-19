---
title: Manage the agent based device inventory on the cloud
description: Learn how to manage your device inventory on the cloud.
ms.date: 08/19/2021
ms.topic: how-to
---

# Manage the agent based device inventory on the cloud

The device inventory can be used to view device systems, and network information. The search, filter, edit columns, and export tools can be used to manage this information.

:::image type="content" source="media/how-to-manage-device-inventory-on-the-cloud/device-inventory-screenshot.png" alt-text="A total overview of Defender for IoT's device inventory screen.":::

Some of the benefits of the device inventory include:

- Group devices by site, device type, vendor.

- Gain visibility into each device and their details.

- Export the entire device inventory to CSV for your reports.

## Device inventory overview

You can see all devices in your inventory through the main indicators on the top of the screen.

:::image type="content" source="media/how-to-manage-device-inventory-on-the-cloud/device-inventory-indicators.png" alt-text="A quick view of all of the devices in your inventory along with their statuses.":::

Here you can see the:

- The total number of devices.

- The number of new devices.

- A number of the devices by class.

- A number of the devices by type.

The following table describes the different parameters in the device inventory.

| Parameter | Description |
|--|--|
| **Site** | The site that contains this device. |
| **IP Address** | The IP address of the device. |
| **Device Name** | The name of the device as the sensor discovered it, or as entered by the user. |
| **Last Activity** | The last activity that the device performed. |
| **Device Type**| The type of device, such as communication, and industrial. |
| **Device subtype**| The subtype of the device, such as speaker and smart tv.
| **Vendor** | The name of the device's vendor, as defined in the MAC address. |
| **Device model**| The devices' model number. |
| **MAC Address** | The MAC address of the device. |
| **OS platform** | The OS of the device, if detected. |
| **First seen** | The date and time the device was first seen. Presented in format MM/DD/YYYY HH:MM:SS AM/PM. |
| **Last update time** | The date and time the device was last updated. Presented in format MM/DD/YYYY HH:MM:SS AM/PM.|
| **Data source** | The source of the data, such as Micro Agent,  OtSensor, and Mde. |
| **Firmware vendor** | The vendor that supplied the firmware. |
| **Device category** | The category of the device. |
| **OS architecture** | The architecture of the operating system. |
| **OS distribution** | The distribution of the operating system, such as Android, Linux, and Haiku.|
| **OS version** | The version of the operating system, such as Windows 10 and Ubuntu 20.04.1.|

**To view the device inventory**:

1. Open the [Azure portal](https://ms.portal.azure.com).

1. Navigate to **Defender for IoT** > **Device inventory**.

    :::image type="content" source="media/how-to-manage-device-inventory-on-the-cloud/device-inventory.png" alt-text="Select device inventory from the left side menu under Defender for IoT.":::

## Customize the device inventory table 

In the device inventory table, you can add or remove columns. You can also change the column order by dragging and dropping a field.

**To customize the device inventory table**:

1. Select the :::image type="icon" source="media/how-to-manage-device-inventory-on-the-cloud/edit-columns-icon.png" border="false"::: button.

1. In the Edit columns tab, select the drop-down menu to change the value of a column.

    :::image type="content" source="media/how-to-manage-device-inventory-on-the-cloud/device-drop-down-menu.png" alt-text="Select the drop-down menu to change the value of a given column.":::

1. Add a column by selecting the :::image type="icon" source="media/how-to-manage-device-inventory-on-the-cloud/add-column-icon.png" border="false"::: button.

1. Reorder the columns by dragging a column parameter to a new location.

1. Delete a column by selecting the :::image type="icon" source="media/how-to-manage-device-inventory-on-the-cloud/trashcan-icon.png" border="false"::: button.

    :::image type="content" source="media/how-to-manage-device-inventory-on-the-cloud/delete-a-column.png" alt-text="Select the trash can icon to delete a column.":::

1. Select **Save** to save any changes made.

If you want to reset the device inventory to the default settings, in the Edit columns window, select the :::image type="icon" source="media/how-to-manage-device-inventory-on-the-cloud/reset-icon.png" border="false"::: button.

## Filter the device inventory

You can search, and filter the device inventory to define what information the table displays.

Below is a list of filters that can be applied to the device inventory table.

| Filter name | Filter type |
|--|--|
| **Device name** | contains |
| **Class** | list |
| **Device type** | contains |
| **Device subtype** | list |
| **Device model** | list |
| **Firmware vendor** | contains |
| **OS distribution** | list |
| **OS version** | contains |
| **IP address** | contains |
| **MAC address** | contains |
| **Site** | list |
| **Last activity** | contains |
| **Vendor**| list |
| **OS platform**| contains |
| **First seen** | list |
| **Last update time** | list |
| **Data source** | list |
| **OS architecture** | contains |

**To filter the device inventory**:

1. Select **Add filter**

    :::image type="content" source="media/how-to-manage-device-inventory-on-the-cloud/add-filter.png" alt-text="Select  the add filter button to specify what you want to appear in the device inventory.":::

1. In the Add filter window, select the column drop-down menu to choose which column to filter.

    :::image type="content" source="media/how-to-manage-device-inventory-on-the-cloud/add-filter-window.png" alt-text="Select which column you want to filter in the device inventory.":::

1. Enter a value in the filter field to filter by.

1. Select the **Apply button**.

Multiple filters can be applied at one time. The filters are not saved when you leave the Device inventory page.

## View device information

To view a specific devices information, select the device and the device information window appears.

:::image type="content" source="media/how-to-manage-device-inventory-on-the-cloud/device-information-window.png" alt-text="Select a device to see all of that device's information.":::

## Export the device inventory to CSV

You can export your device inventory to a CSV file. Any filters that you apply to the device inventory table will be exported, when you export the table.

Select the :::image type="icon" source="media/how-to-manage-device-inventory-on-the-cloud/export-button.png" border="false"::: button to export your current device inventory to a CSV file.

## See next

