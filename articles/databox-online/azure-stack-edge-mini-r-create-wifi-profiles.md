---
title: Create WiFi profiles for Azure Stack Edge Mini R devices
description: Describes how to create Wi-Fi profiles for Azure Stack Edge Mini R devices in high-security enterprise networks and simple home networks.
services: databox
author: v-dalc@microsoft.com

ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/05/2021
ms.author: alkohli
---

# Create a Wi-Fi profile to use with Azure Stack Edge Mini R devices

<!--Add customer intent statement to metadata.-->This article describes how to create wireless network (Wi-Fi) profiles for Azure Stack Edge Mini R devices. You'll need to add a Wi-Fi profile to your Azure Stack Edge Mini R device for each wireless network you connect the device to.

TRANSITION:

- On a personal network, you may be able to download and use an existing wireless profile.<!--Introduce Wi-Fi Protected Access 2 (WPA2) - Personal network.-->

- In a high-security enterprise environment, each client computer will have a distinct profile and will be authenticated via certificates. You'll need to work with your network administrator to determine the required configuration.<!--Introduce WPA2 - Enterprise network.-->

In either case, it's very important to make sure the profile complies with the security requirements of your organization before you test or use the profile with your device. <!--We will talk more about each type of network.-->

## About Wi-Fi profiles

A wireless network (Wi-Fi) profile contains the SSID (service set identifier, or **network name**), password key, and security information to be able to connect your Azure Stack Edge Mini R device to a wireless network.

The following code example shows basic settings for a profile to use with a typical wireless network:

* `SSID` is the network name.<!--This is a hexadecimal value? Always?-->  

* `name` is the user-friendly name for the Wi-Fi connection. This is the name users will see when they browse the available connections on their device.<!--Needs verification.-->

* The profile is configured to automatically connect the computer to the wireless network when it's within range of the network (`connectionMode` = `auto`).

```html
<?xml version="1.0"?>
<WLANProfile xmlns="http://www.contoso.com/networking/WLAN/profile/v1">
	<name>ContosoWIFICORP</name>
	<SSIDConfig>
		<SSID>
			<hex>1A234561234B5012</hex>
		</SSID>
		<nonBroadcast>false</nonBroadcast>
	</SSIDConfig>
	<connectionType>ESS</connectionType>
	<connectionMode>auto</connectionMode>
	<autoSwitch>false</autoSwitch>
```

For more information about Wi-Fi profile settings, see **Enterprise profile** in [Add settings for Windows 10 and later Wi-Fi](/mem/intune/configuration/wifi-settings-windows#enterprise-profile) and [Configure a Cisco wireless controller and access point on your device](azure-stack-edge-mini-r-manage-wifi.md#configure-cisco-wi-fi-profile).

To enable wireless connections on an Azure Stack Edge Mini R device, you configure the Wi-Fi port on your device, and then add the Wi-Fi profile(s) to the device. On an enterprise network,  you'll also upload certificates to the device. You can then connect to a wireless network from the local web UI for the device. For more information, see [Manage wireless connectivity on your Azure Stack Edge Mini R](./azure-stack-edge-mini-r-manage-wifi.md).

## Profile for WPA2 - Personal network

On a Wi-Fi Protected Access 2 (WPA2) - Personal network, such as a home network or Wi-Fi open hotspot, multiple devices may use the same profile and the same password. On your home network, your mobile phone and laptop use the same wireless profile and password to connect to the network.

For example, a Windows 10 client can generate a runtime profile for you. When you sign in to the wireless network, you're prompted for the Wi-Fi password and, once you provide that password, you're connected. No certificate is needed in this environment.

On this type of network, you may be able to export a Wi-Fi profile from your laptop, and then add it to your Azure Stack Edge Mini R device. See (Export Wi-Fi profile)[#export-wifi-profile], below.<!--This procedure also is available in "Add Wi-Fi settings for Windows 10 and newer devices in Intune."-->

> [!IMPORTANT]
> Before you create a Wi-Fi profile for your Azure Stack Edge Mini R device, contact your network administrator to find out the organization's security requirements for wireless networking. You shouldn't test or use any Wi-Fi profile on your device until you know the wireless network meets requirements.

## Profiles for WPA2 - Enterprise network

On a Wireless Protected Access 2 (WPA2) - Enterprise network, you'll need to work with your network administrator to get the needed Wi-Fi profile and certificate to connect your Azure Stack Edge Mini R device to the network.

For highly secure networks, the Azure device can use Protected Extensible Authentication Protocol (PEAP) with Extensible Authentication Protocol-Transport Layer Security (EAP-TLS). PEAP with EAP-TLS uses machine authentication: the client and server use certificates to verify their identities to each other.

The network administrator will generate a unique Wi-Fi profile and a client certificate for each computer. The network administrator decides whether to use a separate certificate for each device or a shared certificate.

> [!NOTE]
> * User authentication using PEAP Microsoft Challenge Handshake Authentication Protocol version 2 (MSCHAPv2) is not supported on Azure Stack Edge Mini R devices.
> * EAP-TLS authentication is required in order to access Azure Stack Edge Mini R functionality. A wireless connection that you set up using Active Directory will not work.

If you work in more than one physical location at the workplace, the network administrator may need to provide more than one site-specific Wi-Fi profile and certificate for your wireless connections.

On an enterprise network, we recommend that you not change settings in the Wi-Fi profiles that your network administrator provides. The only adjustment you may want to make is to the auto-connect settings. For more information, see [Basic profile](/mem/intune/configuration/wi-fi-settings-windows#basic-profile) in Wi-Fi settings for Windows 10 and newer devices.<!--Also applies to home networks.-->

In a high-security enterprise environment, you may be able to use an existing wireless network profile as a template:

* You can download the corporate wireless network profile from your work computer. For instructions, see [Download a Wi-Fi profile](#download-a-wi-fi-profile), below.

* If others in your organization are already connecting to their Azure Stack Edge Mini R devices over a wireless network, they can download the Wi-Fi profile from their device. For instructions, see **Download Wi-Fi profile** in [Manage Wi-Fi](./azure-stack-edge-mini-r-manage-wifi.md#download-wi-fi-profile).

## Export a Wi-Fi profile

To export a profile for the Wi-Fi interface on your computer, do these steps:

1. To see the wireless profiles on your computer, on the **Start** menu, open **Command prompt** (cmd.exe), and enter this command:

   `netsh wlan show profiles`

   The output will look something like this:

   ```dos
   Profiles on interface Wi-Fi:

   Group policy profiles (read only)
   ---------------------------------
       <None>

   User profiles
   -------------
       All User Profile     : ContosoCORP
       All User Profile     : ContosoFTINET
       All User Profile     : GusIS2809
       All User Profile     : GusGuests
       All User Profile     : SeaTacGUEST
       All User Profile     : Boat
   ```

2. To export a profile, enter the following command:

   `netsh wlan export profile name=”<profileName>” folder=”<path>\<profileName>"`

   For example, the following command saves the ContosoFTINET profile in XML format to the Downloads folder for the user named `gusp`.

   ```dos
   C:\Users\gusp>netsh wlan export profile name="ContosoFTINET" folder=c:Downloads

   Interface profile "ContosoFTINET" is saved in file "c:Downloads\ContosoFTINET.xml" successfully.
   ```

## Add certificate, Wi-Fi profile to device

When you have the Wi-Fi profiles and certificates that you need, do these steps to configure your Azure Stack Edge Mini R device for wireless connections:

1. Go to the local web UI of your Azure Stack Edge Mini R device. ADD Step 1-2 GRAPHIC for positioning?

1. For a WPA2 - Enterprise network, upload the needed certificates to the device following the guidance in [Upload certificates](./azure-stack-edge-gpu-manage-certificates.md#upload-certificates).

1. Upload the Wi-Fi profile(s) to the Mini R device following the guidance in [Add, connect to new profile](./azure-stack-edge-mini-r-manage-wifi.md#add-connect-to-wi-fi-profile).

1. Connect to the wireless network to verify that your wireless connection is working. ADD VERIFICATION STEP, with screenshot, performed through the local web UI?  
 
<!--Connect text:

After the wireless network profile is successfully loaded, connect to this profile. Select **Connect to Wi-Fi profile**. 

    ![Local web UI "Port Wi-Fi Network settings" 4](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-4.png)-->

## Next steps

- Learn how to [Configure network for Azure Stack Edge Mini R](azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy.md)
- Learn how to [Manage Wi-Fi on your Azure Stack Edge Mini R](azure-stack-edge-mini-r-manage-wifi.md).
