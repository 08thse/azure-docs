---
title: Collect information for your private mobile network
titlesuffix: Azure Private 5G Core Preview
description: Learn about the information you'll need to deploy a private mobile network through Azure Private 5G Core Preview using the Azure portal.
author: djrmetaswitch
ms.author: drichards
ms.service: private-5g-core
ms.topic: how-to 
ms.date: 12/31/2021
ms.custom: template-how-to
---

# Collect the required information to deploy a private mobile network - Azure portal

This how-to guide takes you through the process of collecting the information you'll need to deploy a private mobile network through Azure Private 5G Core Preview using the Azure portal. You'll use this information to complete the steps in [Deploy a private mobile network - Azure portal](how-to-guide-deploy-a-private-mobile-network-azure-portal.md).

## Prerequisites

You must have completed all of the steps in [Complete the prerequisite tasks for deploying a private mobile network](complete-private-mobile-network-prerequisites.md).

## Collect private mobile network resource configuration values

Collect all of the following values for the resource that will represent your private mobile network.

   |Value  |Field name in Azure portal  |
   |---------|---------|
   |The Azure subscription to use to deploy the private mobile network resource. You must use the same subscription for all resources in your private mobile network deployment. This is the subscription you identified in [Complete the prerequisite tasks for deploying a private mobile network](complete-private-mobile-network-prerequisites.md).                 |**Project details: Subscription**
   |The Azure resource group to use to deploy the private mobile network resource. We recommend that you use a new resource group for this resource, and that you include the purpose of this resource group in its name for future identification (for example, *contoso-pmn-rg*).                |**Project details: Resource group**|
   |The name for the private mobile network.           |**Instance details: Mobile network name**|
   |The region in which you are deploying the private mobile network. We recommend that you use the East US region.                         |**Instance details: Region**|
   |The mobile country code for the private mobile network.     |**Network configuration: Mobile country code (MCC)**|
   |The mobile network code for the private mobile network.     |**Network configuration: Mobile network code (MNC)**|

## Collect SIM resource configuration values

Each SIM resource represents a physical SIM or eSIM that will be served by the private mobile network.

As part of creating your private mobile network, you can provision one or more SIMs that will use it. If you decide not to provision SIMs at this point, you can do so after deploying your private mobile network using the instructions in [Provisioning SIMs](provisioning-sims.md).

If you want to provision SIMs as part of deploying your private mobile network, you must choose one of the following provisioning methods.

- Manually entering values for each SIM into fields in the Azure portal. This option is best when provisioning a small number of SIMs.
- Importing a JSON file containing values for one or more SIM resources. This option is best when provisioning a large number of SIMs. The file format required for this JSON file is given in [Provisioning SIM resources through the Azure portal using a JSON file](#provisioning-sim-resources-through-the-azure-portal-using-a-json-file).

You must then collect each of the values given in the following table for each SIM resource you want to provision.

 |Value  |Field name in Azure portal  | JSON file parameter name |
   |---------|---------|---------|
   |The name for the SIM resource. This must only contain alphanumeric characters, dashes and underscores. |**SIM name**|`simName`|
   |The Integrated Circuit Card Identification Number (ICCID). This identifies a specific physical SIM or eSIM, and includes information on the SIM's country and issuer. This is a unique numerical value between 19 and 20 digits in length, beginning with 89. |**ICCID**|`integratedCircuitCardIdentifier`|
   |The international mobile subscriber identity (IMSI). This is a unique number (usually 15 digits) identifying a device or user in a mobile network. |**IMSI**|`internationalMobileSubscriberIdentity`|
   |The Authentication Key (Ki). This is a unique 128-bit value assigned to the SIM by an operator, and is used in conjunction with the derived operator code (OPc) to authenticate a user. This must be a 32-character string, containing hexadecimal characters only. |**Ki**|`authenticationKey`|
   |The Derived Operator Code (OPc). This is derived from the SIM's Ki and the network's OP (Operator Code) and is used by the packet core to authenticate a user using a standards-based algorithm. This must be a 32-character string, containing hexadecimal characters only. |**Opc**|`operatorKeyCode`|
   |The type of device that is using this SIM. This is an optional free form string, and you can use it as required to easily identify device types that are using the enterprise's mobile networks. |**Device type**|`deviceType`|

### Provisioning SIM resources through the Azure portal using a JSON file

The following is an example of the file format you must use if you want to provision your SIM resources using a JSON file. This example contains the parameters required to provision two SIMs (SIM1 and SIM2).

```json
[
 {
  "simName": "SIM1",
  "integratedCircuitCardIdentifier": "8912345678901234566",
  "internationalMobileSubscriberIdentity": "001019990010001",
  "authenticationKey": "00112233445566778899AABBCCDDEEFF",
  "operatorKeyCode": "63bfa50ee6523365ff14c1f45f88737d",
  "deviceType": "Cellphone"
 },
 {
  "simName": "SIM2",
  "integratedCircuitCardIdentifier": "8922345678901234567",
  "internationalMobileSubscriberIdentity": "001019990010002",
  "authenticationKey": "11112233445566778899AABBCCDDEEFF",
  "operatorKeyCode": "63bfa50ee6523365ff14c1f45f88738d",
  "deviceType": "Sensor"
 }
]
```

## Collect site resource configuration values

Each site represents a physical enterprise location (for example, Contoso Corporation's Chicago factory) containing an Azure Stack Edge device that hosts a packet core instance. Collect all the values in the following tables for the site you want to deploy in your private mobile network.

   |Value  |Field name in Azure portal  |
   |---------|---------|
   |The Azure subscription to use to deploy the site resource. You must use the same subscription for all resources in your private mobile network deployment.                  |**Project details: Subscription**
   |The Azure resource group to use to deploy the site resource. We recommend that you use the same resource group you chose for the private mobile network under **Project details: Resource group** in [Collect private mobile network resource configuration values](#collect-private-mobile-network-resource-configuration-values).                |**Project details: Resource group**|
   |The name for the site.           |**Instance details: Name**|
   |The region in which you are deploying the private mobile network. We recommend that you use the East US region.                         |**Instance details: Region**|
   |The private mobile network resource representing the network to which you are adding the site. This must match the private mobile network name you chose under **Instance details: Mobile network name** in [Collect private mobile network resource configuration values](#collect-private-mobile-network-resource-configuration-values).    |**Network configuration: Mobile network code (MNC)**|

## Collect access network configuration values

Collect all the values in the following table to define the packet core instance's connection to the access network over the N2 and N3 interfaces.

   |Value  |Field name in Azure portal  |
   |---------|---------|
   |The IP address for the packet core instance N2 signaling interface.                  |**N2 address (signaling)**
   |The IP address for the packet core instance N3 interface.                |**N3 address**|
   |The network address of the access subnet in CIDR notation.           |**N2 subnet** and **N3 subnet**|
   |The access subnet default gateway.                         |**N2 gateway** and **N3 gateway**|
   |The Tracking Area Codes the packet core instance must support, given as a comma separated list. For example, *0001,0002*.    |**Tracking area codes**|

## Collect attached data network configuration values

Collect all the values in the following table to define the packet core instance's connection to the data network over the N6 interface.

   |Value  |Field name in Azure portal  |
   |---------|---------|
   |The name of the data network.                  |**Data network**
   |The IP address for the packet core instance N6 interface.                |**N6 address**|
   |The network address of the data subnet in CIDR notation.           |**N6 subnet**|
   |The data subnet default gateway.                         |**N6 gateway**|
   |The network address of the subnet from which IP addresses must be allocated to UEs, given in CIDR notation. The following is an example of the network address format.<br>`198.51.100.0/24`<br>Note that the UE subnets are not related to the access subnet.    |**UE IP subnet**|
   |Whether or not Network Address and Port Translation (NAPT) should be enabled for this data network. NAPT allows you to translate a large pool of private IP addresses for UEs to a small number of public IP addresses at the point at which traffic enters the core network, maximizing the utility of a limited supply of public IP addresses.    |**NAPT**|

## Collect Kubernetes base VM configuration values

During the deployment of your private mobile network, you will create a Kubernetes base VM on each Azure Stack Edge device. This serves as the platform for the Kubernetes cluster that will run the packet core instance. Collect all of the values in the following table for the Kubernetes base VM.

   |Value  |Field name in Azure portal  |
   |---------|---------|
   |The Azure subscription to use to deploy the Kubernetes base VM. You must use the same subscription for all resources in your private mobile network deployment.                  |**Project details: Subscription**
   |The Azure resource group to use to deploy the Kubernetes base VM. We recommend that you use the same resource group you chose for the private mobile network under **Project details: Resource group** in [Collect private mobile network resource configuration values](#collect-private-mobile-network-resource-configuration-values).                |**Project details: Resource group**|
   |The name for the Kubernetes base VM.           |**Instance details: Name**|
   |The region in which you are deploying the private mobile network. We recommend that you use the East US region.                         |**Instance details: Region**|
   |The vendor of the Kubernetes base VM. This is always set to *metaswitch*.   |**Instance details: Vendor**|
   |The name of the **Azure Network Function Manager - Device** resource representing the Azure Stack Edge device on which you want to deploy the Kubernetes base VM. You created this as part of [Complete the prerequisite tasks for deploying a private mobile network](complete-private-mobile-network-prerequisites.md).  |**Instance details: Device**|
   |The stock keeping unit (SKU) for the version of the Kubernetes base VM you will deploy. You must request this from your support representative.  |**Instance details: Vendor SKU**|
   |The public key of an SSH keypair you can use to securely connect to the Kubernetes base VM. The private key must be on your local machine and you must have the public key available to place on the Kubernetes base VM as part of deploying your private mobile network. For more information on creating SSH keypairs, see [Detailed steps: Create and manage SSH keys for authentication to a Linux VM in Azure](/azure/virtual-machines/linux/create-ssh-keys-detailed).  |**Instance details: SSH public key for admin user (MecUser)**|
   |The management IP address for the Kubernetes base VM. This must be a static IPv4 address in the same subnet as, but not identical to, the IP address of the physical port used for the management network on the Azure Stack Edge device.   |**Management: IP address**|
   |The network address of the management subnet in CIDR notation.   |**Management: Subnet**|
   |The management subnet default gateway.   |**Management: Default gateway**|

## Next steps

You can now use the information you have collected to deploy your private mobile network

- [Deploy a private mobile network - Azure portal](how-to-guide-deploy-a-private-mobile-network-azure-portal.md)