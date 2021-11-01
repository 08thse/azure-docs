---
title: EIoT deployment guide
description: Learn how to deploy EIoT for your Defender for IoT service.
author: Elazark
ms.author: V-ekrieg
ms.service: defender-for-iot
ms.topic: how-to
ms.date: 11/01/2021
---

# Deploy EIoT

Thank you for joining our EIoT Private Preview program. We appreciate the time and efforts you are investing in order to help us to launch a better product.  

The purpose of this document is to outline all the requirements that are needed to prepare the site before the deployment procedure.

## Unified IoT Security - Solution Architecture

The Azure Defender for IoT (AD-IoT) team at Microsoft is responsible for securing IoT devices end-to-end whether they are on-premises, in the cloud, or in hybrid environments.  

We are now extending agentless capabilities to go beyond operational environments, and advancing into the realm of enterprise environments. Thereby, delivering coverage to the entire breadth of IoT devices in your environment. This includes everything from corporate printers, cameras, to purpose-built devices, proprietary, and unique devices.

:::image type="content" source="media/eiot-deployment/eiot-architecture.png" alt-text="This is a sample of the EIoT architecture.":::

The expansion into the enterprise network, creates a unique opportunity to leverage Microsoft 365 Defender’s asset discovery capabilities.  

The Private Preview of the new Defender for EIoT solution (covering Enterprise, OT, and M365D) includes:  

### Automatic 3T discovery for IT, IoT, and OT

Use passive agentless network monitoring to gain insight into your complete inventory of all your IT, IoT, and OT devices. The discovery process continuously identifies and classifies devices in your network, and resolves all device details. The discovery process is performed with zero impact to the network.  

### Single pane of glass

A centralized user experience lets the security team visualize, and secure all their IT, IoT, and OT devices regardless of where the devices is located.

### The power of unified SIEM and XDR  

Azure Defender for IoT shares its high-resolution signal data with Microsoft Defender, and Azure Sentinel. This optimizes the users ability to perform incident response efficiently, and accurately with a complete story that is inclusive of the IoT devices involved. Azure Sentinel customers no longer need to switch consoles to put the story together!  

### Easy deployment for a scalable solution

Azure Defender for IoT ensures quick and frictionless deployment of network sensors, both physical, or virtual appliances, with the support of our engineering team, and other departments.

## Prerequisites

### Configure your environment

Before you are able to deploy your EIoT sensor, you will need to configure server, or Virtual Machine (VM). and connect a Network Interface Cared (NIC) to a switch monitoring (SPAN) port.

**To configure your environment**:

1. Set up a server, or VM.

1. Ensure the minimum resources are set to:

    - 4C CPU

    - 8-GB ram

    - 250 GB HDD

    - 2 Network Adapters

    - OS: Ubuntu 18.04

1. Connect a NIC to a switch.

    - **Physical device** - connect a monitoring network interface (NIC) to a switch monitoring (SPAN) port.

    - **VM** - Connect a vNIC to a vSwitch in promiscuous mode.

1. Run the following command to enable the network adapter in promiscuous mode. 

    ```bash
    ifconfig <monitoring port> up promisc
    ```

1. Validate incoming traffic to the monitoring port with the following command:

    ```bash
    ifconfig <monitoring interface>
    ```

    If the number of RX packets increases each time, the interface is receiving incoming traffic. Repeat this step for each interface you have.

### Prepare your environment

Once your environment has been configured, the environment will now have to be prepared.

**To prepare the environment**:

1. Open the following ports in your firewall:

    - HTTPS - 443 TCP

    - DNS - 53 TCP

1. Hostnames for Azure resources:

    - **EventHub**: *.servicebus.windows.net

    - **Storage**: *.blob.core.windows.net

    - **IoT Hub**: *.azure-devices.net

    - **Download Center**: download.microsoft.com

You can also download, and add the [Azure public IP ranges](https://www.microsoft.com/download/details.aspx?id=56519) to your firewall will allow the Azure resources that are specified above along with their region.

> [!Note]
> The Azure public IP range are updated weekly. New ranges appearing in the file will not be used in Azure for at least one week. Please download the new json file every week and perform the necessary changes at your site to correctly identify services running in Azure.

**Prepare an Azure instance**

- If you already have a subscription that is onboarded for Azure Defender for IoT for OT environments, you would need to choose an alternate, or create a new subscription.

- Minimum security level to access different parts of Azure Defender for Iot. You must have a level of Security Owner to create a subscription, and Security Reader level permissions to access the Defender for IoT user interface.

    The following table describes user access permissions to Azure Defender for IoT portal tools:

    |  | Permission | Security reader | Security administrator | Subscription contributor | Subscription owner |
    |--|--|--|--|--|--|
    |  | View details and access software, activation files and threat intelligence packages | ✓ | ✓ | ✓ | ✓ |
    |  | Onboard a sensor |  | ✓ | ✓ | ✓ |
    |  | Update pricing |  | ✓ | ✓ | ✓ |
    |  | Recover password | ✓ | ✓ | ✓ | ✓ |

- Create an IoT Hub.

> [!Note]
> Due to GDPR regulations, EU customers must set their instances to the EU **Europe West** cloud. Any customer outside of the EU, should set their instance to US **US East** cloud.

### Onboard a new subscription

Onboard a new subscription, and provide the subscription ID to your Azure Defender for IoT contact to ensure you will not be billed.

Learn how to [Onboard a subscription](organizations/how-to-manage-subscriptions.md#onboard-a-subscription).

## Onboard a sensor

Onboard a new sensor for this scenario.

**To onboard a sensor**:

1. Navigate to the the [Azure portal](https://portal.azure.com#home).

1. Select **Onboard sensor**.

    :::image type="content" source="media/eiot-deployment/onboard-sensor.png" alt-text="On the Getting Started page select Onboard sensor.":::

1. Enter a name for the sensor.

    :::image type="content" source="media/eiot-deployment/onboard-sensor-screen.png" alt-text="Enter the following information into the onboarding screen.":::

1. Select a subscription.

1. In the Deploy for field, select **An enterprise network (Cloud connected)**.

    :::image type="content" source="media/eiot-deployment/enterprise-network.png" alt-text="Ensure An enterprise network is selected on your screen.":::

1. Select an IoTHub.

1. Enter a site name.

1. Select your zone.

1. Select **Register**.

1. On the Download activation file tab, select **Download activation file**.

1. Use WinSCP or similar to copy the file to the sensor.

1. Select **Finish**.

1. (Optional) If you are using a proxy environment, enter your credentials, and set the proxy to allow a connection to Azure. **Proxies without passwords are not supported**.

## Install the sensor

You will need to download a package, and move it, and your activation file to the home directory.

**To install the sensor**:

1. Run the following command to download the installation package to your sensor.

    ```bash
    wget -O eiot.deb "https://aka.ms/iot-security-enterprise-package-latest"
    ```

1. Update APT using the following command:

    ```bash
    sudo apt update
    ```

1. Install docker.io with the following command:

    ```bash
    sudo apt install docker.io 
    ```

1. Ensure that traffic monitoring NICs are working.

## Run the Enterprise IoT sensor installation

**To run the Enterprise IoT sensor installation**:

1. Navigate to the home directory on the machine.

1. Run the following command:

    ```bash
    sudo LICENSE_PATH=/home/<user>/<license.zip> apt install -y ./eiot.deb
    ```

1. On the Interface Selection popup screen, set all of the interfaces to **monitor**.

1. (Optional) If you are using a proxy, select **Yes**, and enter a server, port, username, and password for the proxy.

1. If you are not using a proxy, select **No**,  and wait for the installation to complete.

## Validate your setup

1. Run the following command to process the sanity of your system.

    ```bash
    sudo docker ps
    ```

1. Ensure the following containers are up:

    - compose_statistics-collector_1

    - compose_cloud-communication_1

    - compose_horizon_1

    - compose_attributes-collector_1

    - compose_properties_1

1. Monitor port validation with the following command to see which interface is defined to handle port mirroring.

    ```bash
    sudo docker logs compose_horizon_1
    ````

    :::image type="content" source="media/eiot-deployment/defined-interface.png" alt-text="Run the command to see which interface is defined to handle port monitoring.":::

1. Run the following command to check the traffic D2C sanity

    ```bash
    sudo docker logs -f compose_attributes-collector_1
    ```

    Ensure that packets are being sent to the eventHub.

## View your enterprise IoT devices in the EIoT device inventory

Once you have validated your your setup the device inventory will start to populate with all of your devices.

**To view your populated device inventory**:

1. Navigate to the [Azure portal](https://portal.azure.com/#home).

1. Search for and select **Defender for IoT**.

1. From the left side toolbar select **Device inventory**.

The device inventory is where you will be able to view all of your device systems, and network information. To learn more about the device inventory see **INSERT LINK HERE!!!**

## Remove the sensor (optional)

You can use the following command to Remove the sensor.

```bash
sudo apt purge -y microsoft-eiot-sensor
```

## Next steps

Learn more about the device inventory.
