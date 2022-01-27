---
title: Device Update for Azure IoT Hub tutorial using the Raspberry Pi 3 B+ Reference Yocto Image | Microsoft Docs
description: Get started with Device Update for Azure IoT Hub using the Raspberry Pi 3 B+ Reference Yocto Image.
author: ValOlson
ms.author: valls
ms.date: 2/11/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
---

# Device Update for Azure IoT Hub tutorial using the Raspberry Pi 3 B+ Reference Image

Device Update for IoT Hub supports image-based, package-based, and script-based updates.

Image updates provide a higher level of confidence in the end-state of the device. It is typically easier to replicate the results of an image-update between a pre-production environment and a production environment, since it doesn’t pose the same challenges as packages and their dependencies. Due to their atomic nature, one can also adopt an A/B failover model easily.

This tutorial walks you through the steps to complete an end-to-end image-based update using Device Update for IoT Hub on a Raspberry Pi 3 B+ board. 

In this tutorial you will learn how to:
> [!div class="checklist"]
> * Download image
> * Add a tag to your IoT device
> * Import an update
> * Create a device group
> * Deploy an image update
> * Monitor the update deployment
Note: Image updates in this tutorial have been validated on the Raspberry Pi B3 board.

## Prerequisites
* If you haven't already done so, create a [Device Update account and instance](create-device-update-account.md), including configuring an IoT Hub.

## Download image

We provide sample images in "Assets" on the [Device Update GitHub releases page](https://github.com/Azure/iot-hub-device-update/releases). The .gz file is the base image that you can flash onto a Raspberry Pi B3+ board, and the swUpdate file is the update you would import through Device Update for IoT Hub. 

## Flash SD card with image

Using your favorite OS flashing tool, install the Device Update base image
(adu-base-image) on the SD Card that will be used in the Raspberry Pi 3 B+
device.

### Using bmaptool to flash SD card

1. If you have not already, install the `bmaptool` utility.

   ```shell
   sudo apt-get install bmap-tools
   ```

2. Locate the path for the SD card in `/dev`. The path should look something
   like `/dev/sd*` or `/dev/mmcblk*`. You can use the `dmesg` utility to help
   locate the correct path.

3. You will need to unmount all mounted partitions before flashing.

   ```shell
   sudo umount /dev/<device>
   ```

4. Make sure you have write permissions to the device.

   ```shell
   sudo chmod a+rw /dev/<device>
   ```

5. Optional. For faster flashing, download the bimap file along with the image
   file and place them in the same directory.

6. Flash the SD card.

   ```shell
   sudo bmaptool copy <path to image> /dev/<device>
   ```
   
Device Update for Azure IoT Hub software is subject to the following license terms:
   * [Device update for IoT Hub license](https://github.com/Azure/iot-hub-device-update/blob/main/LICENSE.md)
   * [Delivery optimization client license](https://github.com/microsoft/do-client/blob/main/LICENSE)
   
Read the license terms prior to using the agent. Your installation and use constitutes your acceptance of these terms. If you do not agree with the license terms, do not use the Device Update for IoT Hub agent.

## Create device or module in IoT Hub and get connection string

Now, the device needs to be added to the Azure IoT Hub.  From within Azure
IoT Hub, a connection string will be generated for the device.

1. From the Azure portal, launch the Azure IoT Hub.
2. Create a new device.
3. On the left-hand side of the page, navigate to 'IoT Devices' >
   Select "New".
4. Provide a name for the device under 'Device ID'--Ensure that "Autogenerate
   keys" is checkbox is selected.
5. Select 'Save'.
6. Now you will be returned to the 'Devices' page and the device you created should be in the list. 
7. Get the device connection string:
	- Option 1 Using Device Update agent with a module identity: From the same 'Devices' page click on '+ Add Module Identity' on the top. Create a new Device Update module with the name 'IoTHubDeviceUpdate', choose other options as it applies to your use case and then click 'Save'. Click on the newly created 'Module' and in the module view, select the 'Copy' icon next to 'Primary Connection String'.
	- Option 2 Using Device Update agent with the device identity: In the device view, select the 'Copy' icon next to 'Primary Connection
   String'.
8. Paste the copied characters somewhere for later use in the steps below.
   **This copied string is your device connection string**.

## Provision connection string on SD card

1. Make sure that the Raspberry Pi3 is connected to the network.
2. In PowerShell, use the below command to ssh into the device
   ```markdown
   ssh raspberrypi3 -l root
      ```
4. Enter login as 'root', and password should be left as empty.
5. After you successfully ssh into the device, run 
 ```markdown
	/etc/adu/du-config.json
   ```
   and add your connection string within the double quotes.

## Connect the device in Device Update IoT Hub

1. On the left-hand side of the page, select 'IoT Devices'.
2. Select the link with your device name.
3. At the top of the page, select 'Device Twin' if directly connecting to Device Update using the IoT device identity. Otherwise select the module you created above and click on its ‘Module Twin’.
4. Under the 'reported' section of the device twin properties, look for the Linux kernel version.
For a new device, which hasn't received an update from Device Update, the
[DeviceManagement:DeviceInformation:1.swVersion](device-update-plug-and-play.md) value will represent
the firmware version running on the device.  Once an update has been applied to a device, Device Update will
use [AzureDeviceUpdateCore:ClientMetadata:4.installedUpdateId](device-update-plug-and-play.md) property
value to represent the firmware version running on the device.
5. The base and update image files have a version number in the filename.

   ```markdown
   adu-<image type>-image-<machine>-<version number>.<extension>
   ```

Use that version number in the Import Update step below.

## Add a tag to your device

1. Log into [Azure portal](https://portal.azure.com) and navigate to the IoT Hub.

2. From 'IoT Devices' or 'IoT Edge' on the left navigation pane find your IoT device and navigate to the Device Twin or Module Twin.

3. In the Module Twin of the Device Update agent module, delete any existing Device Update tag value by setting them to null. If you are using Device identity with Device Update agent make these changes on the Device Twin.

4. Add a new Device Update tag value as shown below.

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            }
```

## Import update

1. Download the [sample import manifest](https://github.com/Azure/iot-hub-device-update/releases/download/0.7.0/TutorialImportManifest_Pi.json) and [sample image update](https://github.com/Azure/iot-hub-device-update/releases/download/0.7.0-rc1/adu-update-image-raspberrypi3-0.6.5073.1.swu).

2. Log in to the [Azure portal](https://portal.azure.com/) and navigate to your IoT Hub with Device Update. Then, select the Updates option under Automatic Device Management from the left-hand navigation bar.

3. Select the Updates tab.

4. Select "+ Import New Update".

5. Select "+ Select from storage container". Select an existing account or create a new account using "+ Storage account". Then select an existing container or create a new container using "+ Container". This container will be used to stage your update files for importing 
   _Recommendation: use a new container each time you import an update to avoid accidentally importing files from previous updates. If you don't use a new container, be sure to delete any files from the existing container before completing this step._
   
   :::image type="content" source="media/import-update/storage-account-ppr.png" alt-text="Storage Account" lightbox="media/import-update/storage-account-ppr.png":::

6. In your container, select "Upload" and navigate to files downloaded in **Step 1**. When you've selected all your update files, select "Upload" Then click the "Select" button to return to the "Import update" page.

   :::image type="content" source="media/import-update/import-select-ppr.png" alt-text="Select Uploaded Files" lightbox="media/import-update/import-select-ppr.png":::
   _This screenshot shows the import step and file names may not match the ones used in the example_

8. On the Import update page, review the files to be imported. Then select "Import update" to start the import process.

   :::image type="content" source="media/import-update/import-start-2-ppr.png" alt-text="Import Start" lightbox="media/import-update/import-start-2-ppr.png":::

9. The import process begins, and the screen switches to the "Import History" section. When the `Status` column indicates the import has succeeded, select the "Available Updates" header. You should see your imported update in the list now.

   :::image type="content" source="media/import-update/update-ready-ppr.png" alt-text="Job Status" lightbox="media/import-update/update-ready-ppr.png":::
       
[Learn more](import-update.md) about importing updates.

## Create update group

1. Go to the Groups and Deployments tab at the top of the page. 
   :::image type="content" source="media/create-update-group/ungrouped-devices.png" alt-text="Screenshot of ungrouped devices." lightbox="media/create-update-group/ungrouped-devices.png":::

2. Select the "Add group" button to create a new group.
   :::image type="content" source="media/create-update-group/add-group.png" alt-text="Screenshot of device group addition." lightbox="media/create-update-group/add-group.png":::

3. Select an IoT Hub tag and Device Class from the list and then select Create group.
   :::image type="content" source="media/create-update-group/select-tag.png" alt-text="Screenshot of tag selection." lightbox="media/create-update-group/select-tag.png":::

4. Once the group is created, you will see that the update compliance chart and groups list are updated.  Update compliance chart shows the count of devices in various states of compliance: On latest update, New updates available, and Updates in Progress. [Learn  about update compliance.](device-update-compliance.md)
   :::image type="content" source="media/create-update-group/updated-view.png" alt-text="Screenshot of update compliance view." lightbox="media/create-update-group/updated-view.png":::

5. You should see your newly created group and any available updates for the devices in the new group. If there are devices that don't meet the device class requirements of the group, they will show up in a corresponding invalid group. You can deploy the best available update to the new user-defined group from this view by clicking on the "Deploy" button next to the group.

[Learn more](create-update-group.md) about adding tags and creating update groups


## Deploy update

1. Once the group is created, you should see a new update available for your device group, with a link to the update under Best Update (you may need to Refresh once). [Learn More about update compliance.](device-update-compliance.md) 

2. Select the target group by clicking on the group name. You will be directed to the group details under Group basics.

  :::image type="content" source="media/deploy-update/group-basics.png" alt-text="Group details" lightbox="media/deploy-update/group-basics.png":::

3. To initiate the deployment, go to the Current deployment tab. Click the deploy link next to the desired update from the Available updates section. The best, available update for a given group will be denoted with a "Best" highlight. 

  :::image type="content" source="media/deploy-update/select-update.png" alt-text="Select update" lightbox="media/deploy-update/select-update.png":::

4. Schedule your deployment to start immediately or in the future, then select Create.

 :::image type="content" source="media/deploy-update/create-deployment.png" alt-text="Create deployment" lightbox="media/deploy-update/create-deployment.png":::

5. The Status under Deployment details should turn to Active, and the deployed update should be marked with "(deploying)".

 :::image type="content" source="media/deploy-update/deployment-active.png" alt-text="Deployment active" lightbox="media/deploy-update/deployment-active.png":::

6. View the compliance chart. You should see the update is now in progress. 

7. After your device is successfully updated, you should see your compliance chart and deployment details update to reflect the same. 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="Update succeeded" lightbox="media/deploy-update/update-succeeded.png":::

## Monitor an update deployment

1. Select the Deployment history tab at the top of the page.

   :::image type="content" source="media/deploy-update/deployments-history.png" alt-text="Deployment History" lightbox="media/deploy-update/deployments-history.png":::

2. Select the details link next to the deployment you created.

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="Deployment details" lightbox="media/deploy-update/deployment-details.png":::

3. Select Refresh to view the latest status details.


You have now completed a successful end-to-end image update using Device Update for IoT Hub on a Raspberry Pi 3 B+ device. 

## Clean up resources

When no longer needed, clean up your device update account, instance, IoT Hub and IoT device. 

## Next steps

> [!div class="nextstepaction"]
> [Simulator Reference Agent](device-update-simulator.md)
