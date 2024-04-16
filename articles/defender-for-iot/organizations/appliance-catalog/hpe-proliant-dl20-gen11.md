---
title: HPE ProLiant DL20 Gen 11 (4SFF) for OT monitoring in SMB/ L500 deployments - Microsoft Defender for IoT 
description: Learn about the HPE ProLiant DL20 Gen 11 Plus appliance when used for OT monitoring with Microsoft Defender for IoT in SMB deployments.
ms.date: 04/09/2024
ms.topic: reference
---

# HPE ProLiant DL20 Gen 11 (4SFF)

This article describes the **HPE ProLiant DL20 Gen 11** appliance for OT sensors monitoring production lines.

The HPE ProLiant DL20 Gen11 is also available for the on-premises management console.

| Appliance characteristic |Details |
|---------|---------|
|**Hardware profile** | L500 |
|**Performance** | Max bandwidth: 200 Mbps <br>Max devices: 1,000<br> Up to 8x monitoring ports |
|**Physical specifications** | Mounting: 1U <br> Minimum dimensions (H x W x D) 1.70 x 17.11 x 15.05 in<br>Minimum dimensions (H x W x D) 4.32 x 43.46 x 38.22 cm|
|**Status** | Supported, available pre-configured |

## Specifications

|Component|Technical specifications|
|----|----|
|Chassis|1U rack server|
|Physical Characteristics  | HPE DL20 Gen11 4SFF Ht Plg CTO Server  |
|Processor| Intel Xeon E-2434 3.4-GHz 4-core 55 W FIO Processor for HPE |
|Chipset|Intel C262 |
|Memory|2 x 16-GB Single Rank x8 DDR5-4800 CAS-40-39-39<br>Unbuffered Standard Memory Kit|
|Storage|HPE 4 x 1.2-TB SAS 12G Mission Critical 10K SFF |
|Network controller|On-board: 4 x 1 Gb|
|External| 1 x Broadcom BCM5719 Ethernet 1-Gb 4-port BASE-T Adapter for HPE|
|On-board| iLO Port Card 1 Gb|
|Management|HPE iLO Advanced|
|Device access| Front: One USB 3.2 1 x USB iLO Service Port<br> Rear: Four USBs 3.2|
|Internal| One USB 3.2|
|Power|HPE 1,000 W Flex Slot Titanium Hot Plug Redundant |
|Rack support|HPE 1U Short Friction Rail Kit |

## DL20 Gen11 (4SFF) - Bill of Materials

|Quantity|PN|Description|
|----|---|----|
|1|	P65392-B21 | HPE ProLiant DL20 Gen 11 4SFF Hot Plug Configure-to-order Server|
|1|	P65392-B21 B19 | HPE DL20 Gen11 4SFF Ht Plg CTO Server |
|1|	P65224-B21 | Intel Xcon E-2434 3.4-GHz 4-core 55 W FIO Processor for HPE|
|2|	P64336-B21 | HPE 16 GB (1 x 16 GB) Single Rank x8 DDR5-4800 CAS-40-39-39 Unbuffered Standard Memory Kit|
|4|	P28586-B21 | HPE 1.2 TB SAS 12 G Mission Critical 10K SFF BC 3-year Warranty Multi Vendor HDD |
|1|	P52753-B21 | HPE ProLiant DL320 Genll x 16 FHHL Riser Kit|
|1|	P51178-B21 | Broadcom BCM5719 Ethernet 1-Gb 4-port BASE-T Adapter for HPE |
|1|	P47789-B21 | HPE MRi-o Gen11 x 16 Lanes without Cache OCP SPDM Storage Controller |
|2|	P03178-B21 | HPE 1,000 W Flex Slot Titanium Hot Plug Power Supply Kit|
|1|	BD505A | HPE iLO Advanced 1-server License with yr Support on iLO Licensed Features |
|1|	P65412-B21 | HPE ProLiant DL20 Gen11 2LFF/4SFF OCP Cable Kit |
|1|	P64576-B21 | HPE Easy Install Rail 12 Kit |
|1|	P65407-B21 | HPE ProLiant DL20 GenIILP iLO/M.2 Enablement Kit |

## Port expansion

Optional modules for port expansion include:

|Location |Type|Specifications|
|--------------|--------------|---------|
| PCI Slot 1 (Low profile)  | DP F/O NIC |P26262-B21 - Broadcom BCM57414 Ethernet 10/25-Gb 2-port SFP28 Adapter for HPE |
| PCI Slot 1 (Low profile)  | DP F/O NIC |P28787-B21 - Intel X710-DA2 Ethernet 10-Gb 2-port SFP+ Adapter for HPE |
| PCI Slot 2 (High profile) | Quad Port Ethernet NIC| P21106-B21 - Intel I350-T4 Ethernet 1-Gb 4-port BASE-T Adapter for HPE |
| PCI Slot 2 (High profile) | DP F/O NIC |P26262-B21 - Broadcom BCM57414 Ethernet 10/25-Gb 2-port SFP28 Adapter for HPE |
| PCI Slot 2 (High profile) | DP F/O NIC |P28787-B21 - Intel X710-DA2 Ethernet 10-Gb 2-port SFP+ Adapter for HPE |
| SFPs for Fiber Optic NICs|MultiMode, Short Range|455883-B21 - HPE BLc 10G SFP+ SR Transceiver |
| SFPs for Fiber Optic NICs|SingleMode, Long Range | 455886-B21 - HPE BLc 10G SFP+ LR Transceiver |

## HPE ProLiant DL20 Gen 11 (4SFF) installation

This section describes how to install Defender for IoT software on the HPE ProLiant DL20 Gen11 (4SFF) appliance.

Installation includes:

- Enabling remote access and updating the default administrator password
- Configuring iLO port on network port 1
- Configuring BIOS and RAID10 settings
- Installing Defender for IoT software

> [!NOTE]
> Installation procedures are only relevant if you need to re-install software on a pre-configured device, or if you buy your own hardware and configure the appliance yourself.
> 

### Enable remote access and update the password

Use the following procedure to set up network options and update the default password.

**To enable, and update the password**:

1. Connect a screen and a keyboard to the HPE appliance, turn on the appliance, and press **F9**.

    :::image type="content" source="../media/tutorial-install-components/hpe-proliant-screen-v2.png" alt-text="Screenshot that shows the HPE ProLiant window.":::

1. Go to **System Utilities** > **System Configuration** > **iLO 5 Configuration Utility** > **Network Options**.

    :::image type="content" source="../media/tutorial-install-components/system-configuration-window-v2.png" alt-text="Screenshot that shows the System Configuration window.":::

    1. Select **Shared Network Port-LOM** from the **Network Interface Adapter** field.

    1. Set **Enable DHCP** to **Off**.

    1. Enter the IP address, subnet mask, and gateway IP address.

1. Select **F10: Save**.

1. Select **Esc** to get back to the **iLO 5 Configuration Utility**, and then select **User Management**.

1. Select **Edit/Remove User**. The administrator is the only default user defined.

1. Change the default password and select **F10: Save**.

### Configure the HPE BIOS

This procedure describes how to update the HPE BIOS configuration for your OT deployment. 

**To configure the HPE BIOS:**

1. Select **System Utilities** > **System Configuration** > **BIOS/Platform Configuration (RBSU)**.

1. In the **BIOS/Platform Configuration (RBSU)** form, select **Boot Options**.

1. Change **Boot Mode** to **UEFI BIOS Mode**, and then select **F10: Save**.

1. Select **Esc** twice to close the **System Configuration** form.

1. Select **Embedded RAID 1: HPE Smart Array E208i-a SR Gen 10** > **Array Configuration** > **Create Array**.

1. Select **Proceed to Next Form**.

1. In the **Set RAID Level** form, set the level to **RAID 10**. 

1. Select **Proceed to Next Form**. 

1. In the **Logical Drive Label** form, enter **Logical Drive 1**. 

1. Select **Submit Changes**. 

1. In the **Submit** form, select **Back to Main Menu**. 

1. Select **F10: Save** and then press **Esc** twice. 

1. In the **System Utilities** window, select **One-Time Boot Menu**. 

1. In the **One-Time Boot Menu** form, select **Legacy BIOS One-Time Boot Menu**. 

1. The **Booting in Legacy** and **Boot Override** windows appear. Choose a boot override option; for example, to a CD-ROM, USB, HDD, or UEFI shell. 

:::image type="content" source="../media/tutorial-install-components/boot-override-window-one-v2.png" alt-text="Screenshot that shows the first Boot Override window."::: 

:::image type="content" source="../media/tutorial-install-components/boot-override-window-two-v2.png" alt-text="Screenshot that shows the second Boot Override window."::: 

[!INCLUDE [install iLO remotely from virtual drive and change timeout settings](../includes/ilo-remote-install-hpe.md)]

### Install Defender for IoT software on the HPE ProLiant DL20 Gen 11 (4SFF)

This procedure describes how to install Defender for IoT software on the HPE ProLiant DL20 Gen 11 (4SFF).

The installation process takes about 20 minutes. After the installation, the system is restarted several times.

**To install Defender for IoT software**:

1. Connect the screen and keyboard to the appliance, and then connect to the CLI.

1. Connect an external CD or disk-on-key that contains the software you downloaded from the Azure portal.

1. Start the appliance.

1. Continue with the generic procedure for installing Defender for IoT software. For more information, see [Defender for IoT software installation](../how-to-install-software.md).

## Next steps

Continue learning about the system requirements for physical or virtual appliances. For more information, see [Which appliances do I need?](../ot-appliance-sizing.md).

Then, use any of the following procedures to continue:

- [Download software for an OT sensor](../ot-deploy/install-software-ot-sensor.md#download-software-files-from-the-azure-portal)
- [Download software files for an on-premises management console](../legacy-central-management/install-software-on-premises-management-console.md#download-software-files-from-the-azure-portal)
