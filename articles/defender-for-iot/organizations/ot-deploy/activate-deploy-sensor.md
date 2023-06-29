---
title: Configure and activate your OT sensor - - Microsoft Defender for IoT
description: Learn how to configure initial setup settings and activate your Microsoft Defender for IoT OT sensor.
ms.date: 06/26/2023
ms.topic: install-set-up-deploy
---

# Configure and activate your OT sensor

This article is one in a series of articles describing the [deployment path](ot-deploy-path.md) for OT monitoring with Microsoft Defender for IoT, and describes how to configure initial setup settings and activate your OT sensor.

:::image type="content" source="../media/deployment-paths/progress-deploy-your-sensors.png" alt-text="Diagram of a progress bar with Deploy your sensors highlighted." border="false" lightbox="../media/deployment-paths/progress-deploy-your-sensors.png":::

Several initial setup steps can be performed by the GUI or the CLI.

- Use the GUI if you can connect physical cables from your switch to the sensor to identify your interfaces correctly.
- Use the CLI if you know your networking details without needing to connect physical cables.

Configuring your setup via the CLI still requires you to complete the last few steps in the browser.

## Prerequisites

To perform the procedures in this article, you need:

- An OT sensor [onboarded](../onboard-sensors.md) to Defender for IoT in the Azure portal and [installed](install-software-ot-sensor.md) or [purchased](../ot-pre-configured-appliances.md).

- The sensor's activation file, which was downloaded after [onboarding your sensor](../onboard-sensors.md). You need a unique activation file for each OT sensor you deploy.

    [!INCLUDE [root-of-trust](../includes/root-of-trust.md)]

- A SSL/TLS certificate. We recommend using a CA-signed certificate, and not a self-signed certificate. For more information, see [Create SSL/TLS certificates for OT appliances](create-ssl-certificates.md).

- Access to the physical or virtual appliance where you'll be installing your sensor. For more information, see [Which appliances do I need?](../ot-appliance-sizing.md)

This step is performed by your deployment teams.

## Configure setup via the GUI

Configuring sensor setup via the GUI includes the following steps:

- Signing into the sensor console and changing the *support* user password
- Defining network details for your sensor
- Defining the interfaces you want to monitor
- Activating your sensor
- Configuring SSL/TLS certificate settings

### Sign in to the sensor console and change the default password

This procedure describes how to sign into the OT sensor console for the first time. You're prompted to change the default password for the *support* user.

**To sign in to your sensor**:

1. In a browser, go to the default IP address provided for your sensor at the end of the installation. If you've purchased a pre-configured appliance, this IP address is provided with the appliance. 

    The initial sign-in page appears. For example:

    :::image type="content" source="../media/install-software-ot-sensor/ui-sign-in.png" alt-text="Screenshot of the initial sensor sign-in page.":::

1. Enter the following credentials and select **Login**:

    - **Username**: `support`
    - **Password**: `support`

    You're asked to define a new password for the *support* user.

1. In the **New password** field, enter your new password. Your password must contain lowercase and uppercase alphabetic characters, numbers, and symbols.

    In the **Confirm new password** field, enter your new password again, and then select **Get started**.

    For more information, see [Default privileged users](../manage-users-sensor.md#default-privileged-users).

The **Defender for IoT | Overview** page opens to the **Management interface**. 

### Define sensor networking details

In the **Management interface** tab, use the following fields to define network details for your new sensor:

|Name  |Description  |
|---------|---------|
|**Management interface**     |  Select the interface you want to use as the management interface, to connect to either the Azure portal or an on-premises management console.<br><br>To identify a physical interface on your machine, select an interface and then select **Blink physical interface LED**. The port that matches the selected interfacelights up so that you can connect your cable correctly.        |
|<a name="ip"></a>**IP Address**     |  Enter the IP address you want to use for your sensor. This is the IP address your team will use to connect to the sensor via the browseror CLI. |
|**Subnet Mask**     | Enter the address you want to use as the sensor's subnet mask.        |
|**Default Gateway**     | Enter the address you want to use as the sensor's default gateway.        |
|**DNS**     |   Enter the sensor's DNS server IP address.      |
|**Hostname**     |  Enter the hostname you want to assign to the sensor. Make sure that you use the same hostname as is defined in the DNS server.       |
|**Enable proxy for cloud connectivity (Optional)**     | Select to define a proxy server for your sensor.  <br><br>If you use an SSL/TSL certificate to access the proxyserver, select **Client certificate** and upload your certificate.      |

When you're done, select **Next: Interface configurations** to continue.

### Define the interfaces you want to monitor

The **Interface connections** tab shows all interfaces detected by the sensor by default. You may or may not want to monitor all detected interfaces, or you may want to define specific settings for each interfaces.

In the **Interface configurations** tab, do the following to configure settings for your monitored interfaces:

1. Select the **Enable/Disable** toggle for any interfaces you want the sensor to monitor. You must select at least one interface to continue.

    If you're not sure which interface to use, select the :::image type="icon" source="../media/install-software-ot-sensor/blink-interface.png" border="false"::: **Blink physical interface LED** button to have the selected port blink on your machine. Select any of the interfaces that you've connected to your switch.

1. (Optional) For each interface you select to monitor, select the :::image type="icon" source="../media/install-software-ot-sensor/advanced-settings-icon.png" border="false"::: **Advanced settings**  button to modify any of the following settings:

    |Name  |Description  |
    |---------|---------|
    |**Mode**     | Select **SPAN Traffic (no encapsulation)** to use the default SPAN port mirroring.  Select **ERSPAN** if you're using ERSPAN mirroring. For minformation, see [Choose a traffic mirroring method for OT sensors](../best-practices/traffic-mirroring-methods.md).       |
    |**Description**     |  Enter an optional description for the interface. <!--where would i see this afterwards?-->       |
    |**Auto negotiation**     | Relevant for physical machines only.  <!--what does this do?-->     
    
    Select **Save** to save your changes.

1. Select **Next: Reboot >** to continue, and then **Start reboot** to reboot your sensor machine. After the sensor starts again, you're automatically redirected to the IP address you'd [defined earlier as your sensor IP address](#ip).

    Select **Cancel** to wait for the reboot.

### Activate your OT sensor

This procedure describes how to activate your new OT sensor. 

If you've configured the initial settings [via the CLI](#configure-setup-via-the-cli) until now, you'll start the GUI configuration at this step. After the sensor reboots, you're redirected to the same **Defender for IoT | Overview** page, to the **Activation** tab.

**To activate your sensor**:

1. In the **Activation** tab, select **Upload** to upload the sensor's activation file that you'd downloaded from the Azure portal.

1. Select the terms and conditions option and then select **Next: Certificates**.

### Define SSL/TLS certificate settings

Use the **Certificates** tab to deploy an SSL/TLS certificate on your OT sensor. We recommend that you use a [CA-signed certificate](create-ssl-certificates.md) for all production environments.


**To define SSL/TLS certificate settings**:

1. In the **Certificates** tab, select **Import trusted CA certificate (recommended)** to deploy a CA-signed certificate.

    Enter the certificate's name and passphrase, and then select **Upload** to upload your private key file, certificate file, and an optional certificate chain file.

    You may need to refresh the page after uploading your files. For more information, see [Troubleshoot certificate upload errors](../how-to-manage-individual-sensors.md#troubleshoot-certificate-upload-errors). <!-do we still need to?-->

    > [!TIP]
    > If you're working on a testing environment, you can also use the self-signed certificate that's generated locally during installation. If you select to use a self-signed certificate, make sure to select the **Confirm** option about the recommendations.
    >
    > For more information, see [Manage SSL/TLS certificates](../how-to-manage-individual-sensors.md#manage-ssltls-certificates).

1. In the **Enable certificate validation** area, select **Mandatory** to validate the certificate against a certificate revocation list (CRL), as [configured in your certificate](../best-practices/certificate-requirements.md#crt-file-requirements). <!--in the current UI this actually reads Validation of on-premises management console certificate-->

1. Select **Finish** to complete the initial setup and open your sensor console.

## Configure setup via the CLI

Use this procedure to configure the following initial setup settings via CLI:

- Signing into the sensor console and setting a new *support* user password
- Defining network details for your sensor
- Defining the interfaces you want to monitor

You'll continue with [activating](#activate-your-ot-sensor) and [configuring SSL/TLS certificate settings](#define-ssltls-certificate-settings) in the GUI.

**To configure initial setup settings via CLI**:

1. In the installation screen, after the default networking details are shown, press **ENTER** to continue.

1. At the `D4Iot login` prompt, sign in with the following default credentials:

    - **Username**: `support`
    - **Password**: `support`

    When you enter your password, the password characters don't display on the screen. Make sure you enter them carefully.

1. At the prompt, enter a new password for the *support* user. Your password must contain lowercase and uppercase alphabetic characters, numbers, and symbols.

    When prompted to confirm your password, enter your new password again. For more information, see [Default privileged users](../manage-users-sensor.md#default-privileged-users).

    <does this happen immediately? unclear-->The `Package configuration` Linux configuration wizard opens. In this wizard, use the up or down arrows to navigate, and the **SPACE** bar to select an option. Press **ENTER** to advance to the next screen.

1. In the wizard's `Select monitor interfaces` screen, select any of the interfaces you want to monitor with this sensor.

    <!--this isn't available in the sample machine i used: By default, `eno1` is reserved for the management interface and we recommend that you leave this option unselected.

    For example:

    :::image type="content" source="../media/tutorial-install-components/monitor-interface.png" alt-text="Screenshot of the select monitor interface screen."::: -->

    For example:

    :::image type="content" source="../media/install-software-ot-sensor/select-monitor-interfaces.png" alt-text="Screenshot of the Select monitor interfaces screen.":::

    > [!IMPORTANT]
    > Make sure that you select only interfaces that are connected.
    >
    > If you select interfaces that are enabled but not connected, the sensor will show a *No traffic monitored* health notification in the Azure portal. If you connect more traffic sources after installation and want to monitor them with Defender for IoT, you can add them later via the [CLI](../references-work-with-defender-for-iot-cli-commands.md). <!--can i still not do this after via the UI?-->

<!--
1. In the `Select erspan monitor interfaces` screen, select any ERSPAN monitoring ports that you have. The wizard lists available interfaces, even if you don't have any ERSPAN monitoring ports in your system. If you have no ERSPAN monitoring ports, leave all options unselected. <!--does this page still show? i don't see it

    For example:

    :::image type="content" source="../media/tutorial-install-components/erspan-monitor.png" alt-text="Screenshot of the select erspan monitor screen.":::

-->
1. In the `Select management interface` screen, select the interface you want to use to connect to the Azure portal or an on-premises management console.  <!--is this correct?-->

    <!--not shown in my sample machine: we recommend keeping the default `eno1` value selected as the management interface.-->

    For example:

    :::image type="content" source="../media/install-software-ot-sensor/select-management-interface.png" alt-text="Screenshot of the Select management interface screen.":::

    <!--:::image type="content" source="../media/tutorial-install-components/management-interface.png" alt-text="Screenshot of the management interface select screen.":::-->

1. In the `Enter sensor IP address` screen, enter the IP address you want to use for this sensor. You'll use this IP address to connect to the sensor via CLI or the browser. For example:

    :::image type="content" source="../media/install-software-ot-sensor/enter-sensor-ip-address.png" alt-text="Screenshot of the Enter sensor IP address screen.":::

1. In the `Enter path to the mounted backups folder` screen, enter the path to the sensor's mounted backups. We recommend using the default path of `/opt/sensor/persist/backups`. For example:

    :::image type="content" source="../media/install-software-ot-sensor/mounted-backups.png" alt-text="Screenshot of the mounted backups folder configuration.":::

1. In the `Enter Subnet Mask` screen, enter the IP address for the sensor's subnet mask. For example:

    :::image type="content" source="../media/install-software-ot-sensor/subnet-mask.png" alt-text="Screenshot of the Enter Subnet Mask screen.":::

1. In the `Enter Gateway` screen, enter the sensor's default gateway IP address. For example:

    :::image type="content" source="../media/install-software-ot-sensor/enter-gateway.png" alt-text="Screenshot of the Enter Gateway screen.":::

1. In the `Enter DNS server` screen, enter the sensor's DNS server IP address. For example:

    :::image type="content" source="../media/install-software-ot-sensor/enter-dns-server.png" alt-text="Screenshot of the Enter DNS server screen.":::

1. In the `Enter hostname` screen, enter a name you want to use as the sensor hostname. Make sure that you use the same hostname as is defined in the DNS server.  For example:

    :::image type="content" source="../media/install-software-ot-sensor/enter-hostname.png" alt-text="Screenshot of the Enter hostname screen.":::

1. In the `Run this sensor as a proxy server (Preview)` screen, select `<Yes>` only if you want to configure a proxy, and then enter the proxy credentials as prompted. For more information, see [Configure proxy settings on an OT sensor](../connect-sensors.md).

    The default configuration is without a proxy.

1. The configuration process starts running, reboots, and then prompts you to sign in again. For example:

    :::image type="content" source="../media/install-software-ot-sensor/final-cli-sign-in.png" alt-text="Screenshot of the final sign-in prompt at the end of the initial CLI configuration.":::

At this point, open a browser to the IP address you'd defined for your sensor and continue the setup in the browser. For more information, see [Activate your OT sensor](#activate-your-ot-sensor).

## Next steps

> [!div class="step-by-step"]
> [« Validate an OT sensor software installation](post-install-validation-ot-software.md)

> [!div class="step-by-step"]
> [Configure proxy settings on an OT sensor »](../connect-sensors.md)