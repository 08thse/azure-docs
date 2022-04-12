---
title: HPE Edgeline EL300 (legacy) - Microsoft Defender for IoT
description: Learn about the HPE Edgeline EL300 appliance for IoT in SMB rugged deployments.
ms.date: 04/04/2022
ms.topic: reference
---

# HPE Edgeline EL300 

This article describes the HPE Edgeline EL300 appliance for use in OT.

|Summary  | |
|---------|---------|
|**Architecture** | [SMB] |
|**Performance** | 	Max bandwidth: 100 Mbp/s<br>Max devices: 800 |
|**Physical Specifications** | Mounting: Mounting kit, Din Rail<br>Ports: 5x RJ45|
|**Status** | Supported, Not available pre-installed|
## Specifications


|Component|Technical specifications|
|:----|:----|
|Construction|Aluminum, Fanless & Dust-proof Design|
|Dimensions (height x width x depth)|200.5 mm (7.9”) tall, 232 mm (9.14”) wide by 100 mm (3.9”) deep|
|Weight|4.91 KG (10.83 lbs.)|
|CPU|Intel Core i7-8650U (1.9GHz/4-core/15W)|
|Chipset|Intel® Q170 Platform Controller Hub|
|Memory|8 GB DDR4 2133 MHz Wide Temperature SODIMM|
|Storage|128 GB 3ME3 Wide Temperature mSATA SSD|
|Network controller|6x Gigabit Ethernet ports by Intel® I219|
|Device access|4 USBs: Two fronts; two rears; 1 internal|
|Power Adapter|250V/10A|
|Mounting|Mounting kit, Din Rail|
|Operating Temperature|0C to +70C|
|Humidity|10%~90%, non-condensing|
|Vibration|0.3 gram 10 Hz to 300 Hz, 15 minutes per axis - Din rail|
|Shock|10G 10 ms, half-sine, three for each axis. (Both positive & negative pulse) – Din Rail|

## Appliance BOM

|Product|Description|
|:----|:----|
|P25828-B21|HPE Edgeline EL300 v2 Converged Edge System|
|P25828-B21 B19|HPE EL300 v2 Converged Edge System|
|P25833-B21|Intel Core i7-8650U (1.9GHz/4-core/15W) FIO Basic Processor Kit for HPE Edgeline EL300|
|P09176-B21|HPE Edgeline 8 GB (1x8 GB) Dual Rank x8 DDR4-2666 SODIMM WT CAS-19-19-19 Registered Memory FIO Kit|
|P09188-B21|HPE Edgeline 256-GB SATA 6G Read Intensive M.2 2242 3 year warranty wide temperature SSD|
|P04054-B21|HPE Edgeline EL300 SFF to M.2 Enablement Kit|
|P08120-B21|HPE Edgeline EL300 12VDC FIO Transfer Board|
|P08641-B21|HPE Edgeline EL300 80W 12VDC Power Supply|
|AF564A|HPE C13 - SI-32 IL 250 V 10 Amp 1.83 m Power Cord|
|P25835-B21|HPE EL300 v2 FIO Carrier Board|
|R1P49AAE|HPE EL300 iSM Adv 3 yr 24x7 Sup_Upd E-LTU|
|P08018-B21 optional|HPE Edgeline EL300 Low Profile Bracket Kit|
|P08019-B21 optional|HPE Edgeline EL300 DIN Rail Mount Kit|
|P08020-B21 optional|HPE Edgeline EL300 Wall Mount Kit|
|P03456-B21 optional|HPE Edgeline 1-GbE 4-port TSN FIO Daughter Card|

## HP EdgeLine 300 installation

- A default administrative user is provided. We recommend that you change the password during the network configuration.

- The installation process takes about 20 minutes. After the installation, the system is restarted several times.

### HP EdgeLine 300 back panel

:::image type="content" source="media/tutorial-install-components/edgeline-el300-panel.png" alt-text="View of the back panel of the EL300":::

### Enable remote access

1. Enter the iSM IP Address into your web browser.

1. Sign in using the default username, and password found on your appliance.

1. Navigate to **Wired and Wireless Network** > **IPV4**

    :::image type="content" source="media/tutorial-install-components/wired-and-wireless.png" alt-text="navigate to highlighted sections.":::

1. Disable **DHCP toggle**.

1. Configure the IPv4 addresses as such:
    - **IPV4 Address**: `192.168.1.125`
    - **IPV4 Subnet Mask**: `255.255.255.0`
    - **IPV4 Gateway**: `192.168.1.1`

1. Select **Apply**.

1. Sign out, and reboot the appliance.

### Configure the BIOS

The following procedure describes how to configure the BIOS for HP EL300 appliance.

**To configure the BIOS**:

1. Turn on the appliance, and push **F9** to enter the BIOS.

1. Select **Advanced**, and scroll down to **CSM Support**.

    :::image type="content" source="media/tutorial-install-components/csm-support.png" alt-text="Enable CSM support to open the additional menu.":::

1. Push **Enter** to enable CSM Support.

1. Navigate to Storage, and push **+/-** to change it to Legacy.

1. Navigate to Video, and push **+/-** to change it to Legacy.

    :::image type="content" source="media/tutorial-install-components/storage-and-video.png" alt-text="Navigate to storage and video and change them to Legacy.":::

1. Navigate to **Boot** > **Boot mode select**.

1. Push **+/-** to change it to Legacy.

    :::image type="content" source="media/tutorial-install-components/boot-mode.png" alt-text="Change Boot mode select to Legacy.":::

1. Navigate to **Save & Exit**.

1. Select **Save Changes and Exit**.

    :::image type="content" source="media/tutorial-install-components/save-and-exit.png" alt-text="Save your changes and exit the system.":::

1. Select **Yes**, and the appliance will reboot.

1. Push **F11** to enter the **Boot Menu**.

1. Select the device with the sensor image. Either **DVD** or **USB**.

1. Follow the software installation instructions located [here](#install-the-software).

## Next steps

Continue understanding system requirements for physical or virtual appliances. For more information, see [Which appliances do I need?](ot-appliance-sizing.md).

Then, use any of the following procedures to continue:

- [Purchase sensors or download software for sensors](how-to-manage-sensors-on-the-cloud.md#purchase-sensors-or-download-software-for-sensors)
- [Download software for an on-premises management console](how-to-manage-the-on-premises-management-console.md#download-software-for-the-on-premises-management-console)
- [Install software](how-to-install-software.md)
