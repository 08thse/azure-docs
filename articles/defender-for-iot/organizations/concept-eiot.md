---
title: Enhance device discovery with an Enterprise IoT network sensor - Microsoft Defender for IoT
description: Learn how integrating Microsoft Defender for Endpoint and Microsoft Defender for IoT enhances your IoT network security.
ms.topic: conceptual
ms.date: 10/19/2022
---

# Secure Enterprise IoT network resources with Defender for Endpoint and Defender for IoT

<!--these include files don't work [!INCLUDE [Microsoft 365 Defender rebranding](/microsoft-365/security/includes/microsoft-defender.md)]-->

**Applies to:**

- [Microsoft Defender for Endpoint P2](https://go.microsoft.com/fwlink/?linkid=2154037)
- [Microsoft 365 Defender](https://go.microsoft.com/fwlink/?linkid=2118804)

<!--these include files don't work--[!INCLUDE[Prerelease information](/microsoft-365/security/includes/prerelease.md)]-->

> Want to experience Microsoft Defender for Endpoint? [Sign up for a free trial.](https://signup.microsoft.com/create-account/signup?products=7f379fee-c4f9-4278-b0a1-e4c8c2fcdf7e&ru=https://aka.ms/MDEp2OpenTrial?ocid=docs-wdatp-enablesiem-abovefoldlink)

[Microsoft Defender for Endpoint](/microsoft-365/security/defender-endpoint/) seamlessly integrates with [Microsoft Defender for IoT](/azure/defender-for-iot/organizations/) for extra security value for IoT devices and extended IoT device discovery across your network.

Enterprise IoT devices include Voice over Internet Protocol (VoIP) devices, printers, and cameras, and more, and can be viewed in Defender for Endpoint alongside other IT devices like workstations, servers, and mobile devices.

## Extra security value in Defender for Endpoint

While Microsoft 365 P2 customers can already view discovered IoT devices in the Defender for Endpoint [Device inventory](/microsoft-365/security/defender-endpoint/machines-view-overview), onboarding an Enterprise IoT plan provides extra security value with alerts, recommendations and vulnerabilities for discovered IoT devices.

Use our [Onboard with Microsoft Defender for IoT](eiot-mde.md) tutorial to get started seeing this extra security value in your environments

For more information, see:

- [Alerts queue in Microsoft 365 Defender](/microsoft-365/security/defender-endpoint/alerts-queue-endpoint-detection-response).
- [Security recommendations](/microsoft-365/security/defender-vulnerability-management/tvm-security-recommendation).
- [Vulnerabilities in my organization](/microsoft-365/security/defender-vulnerability-management/tvm-weaknesses).

## Extended device visibility in the Azure portal

In addition to viewing IoT devices in Defender for Endpoint's **Device inventory**, customers onboarded to Enterprise IoT can also view detected IoT devices in the Azure portal. Viewing IoT devices in the Azure portal is especially helpful when you're also using Defender for IoT to view and manage Operational Technology (OT) network devices.

View your devices in Azure's Defender for IoT's **Device inventory** for a single, continuous, and integrated security solution across all your OT and Enterprise IoT devices.

For more information, see [Manage your device inventory from the Azure portal](how-to-manage-device-inventory-for-organizations.md).

## Enhanced device discovery with Defender for IoT network sensors (Public preview)

To discover extra devices not covered by Defender for Endpoint, Microsoft 365 P2 and Defender for IoT customers can also install the Enterprise IoT network sensor. The Enterprise IoT network sensor provides visibility into IoT segments of the corporate network that aren't covered by Defender for Endpoint, such as:

- Devices that reside outside of the subnets where managed endpoints reside
- Devices that are blocked by a NAT
- Devices in a completely different network

An Enterprise IoT network sensor also provides specific classifications for devices that are classified as *unknown* by Defender for Endpoint.

> [!NOTE]
> Deploying a network sensor is optional and is *not* a prerequisite for integrating Defender for Endpoint and Defender for IoT.

For more information, see [Enhance device discovery with a Enterprise IoT network sensor](eiot-sensor.md).


## IoT security feature comparison summary

The following table provides a comparison of the increased security features available for IoT devices as you use Defender for Endpoint, add an Enterprise IoT plan, or an extra Enterprise IoT network sensor.

|Security feature  |Defender for Endpoint only  |Defender for Endpoint and <br> Enterprise IoT plan  |Defender for Endpoint, <br>Enterprise IoT plan, <br>and Enterprise IoT network sensors (Public preview) |
|---------|---------|---------|---------|
|Initial device discovery     |   ✓      |      ✓   |      ✓   |
|Full device discovery     |         |         |    ✓     |
|Security recommendations     |         |   ✓      |    ✓     |
|Vulnerabilities     |         |    ✓     |    ✓     |
|Basic IT and IoT device alerts     |  ✓       |        ✓ | ✓        |
|IoT-focused alerts     |         |   ✓      |       ✓  |
|IoT hunting and custom detections     |         |      ✓   |      ✓   |
|Response <!--do we include this? when is this getting added?-->     |   ✓      |     ✓    |    ✓     |

## Next steps

Start securing your Enterprise IoT network resources with by [onboarding to Defender for IoT from Defender for Endpoint](eiot-mde.md).

For more information, see [Enterprise IoT networks frequently asked questions](faqs-eiot.md).
