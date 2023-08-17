---
title: Integrate Splunk with Microsoft Defender for IoT
description: This article describes how to integrate Splunk with Microsoft Defender for IoT for multidimensional visibility across OT protocols and IIoT devices. 
ms.topic: how-to
ms.date: 08/14/2023
ms.custom: how-to
---

# Integrate Splunk with Microsoft Defender for IoT

This article describes how to integrate Splunk with Microsoft Defender for IoT, in order to view both Splunk and Defender for IoT information in a single place.

Viewing both Defender for IoT and Splunk information together provides SOC analysts with multidimensional visibility into the specialized OT protocols and IIoT devices deployed in industrial environments, along with ICS-aware behavioral analytics to rapidly detect suspicious or anomalous behavior.

## Cloud integrations (recommended)

If you're integrating a cloud-connected OT sensor with Splunk, we recommend that you use Splunk's own [OT Security Add-on for Splunk](https://apps.splunk.com/app/5151). For more information, see:

- [The Splunk documentation on installing add-ins](https://docs.splunk.com/Documentation/AddOns/released/Overview/Distributedinstall)
- [The Splunk documentation on the OT Security Add-on for Splunk](https://splunk.github.io/ot-security-solution/integrationguide/)


## On-premises integrations (recommended)

If you're working with an air-gapped, locally managed OT sensor, you'll need an on-premises solution to view Defender for IoT and Splunk information in the same place.

In such cases, we recommend that you configure your OT sensor to send syslog files directly to Splunk, or use Defender for IoT's built-in API.

For more information, see:

- [Forward on-premises OT alert information](how-to-forward-alert-information-to-partners.md)
- [Defender for IoT API reference](references-work-with-defender-for-iot-apis.md)


## On-premises integration (legacy)

This section describes how to integrate Defender for IoT and Splunk using the legacy, on-premises integration.

> [!IMPORTANT]
> Defender for IoT plans to end support for the [legacy Splunk integration](#on-premises-integration-legacy) in an upcoming 23.x version.
>
> We recommend transitioning to newly recommended methods instead, such using the OT Security Add-on for Splunk, forwarding alert rules, or using Defender for IoT's API. For more information, see [Cloud integrations (recommended)](#cloud-integrations-recommended) and [On-premises integrations (recommended)](#on-premises-integrations-recommended).

Microsoft Defender for IoT was formally known as [CyberX](https://blogs.microsoft.com/blog/2020/06/22/microsoft-acquires-cyberx-to-accelerate-and-secure-customers-iot-deployments/). References to CyberX refer to Defender for IoT.

### Prerequisites

Before you begin, make sure that you have the following prerequisites:

|Prerequisites  |Description |
|---------|---------|
|**Version requirements**     | The following versions are required for the application to run: <br>- Defender for IoT version 2.4 and above. <br>- Splunkbase version 11 and above. <br>- Splunk Enterprise version 7.2 and above.        |
|**Permission requirements**     | Make sure you have: <br>- Access to a Defender for IoT OT sensor as an [Admin user](roles-on-premises.md). <br>- Splunk user with an *Admin* level user role.        |

> [!NOTE]
> The Splunk application can be installed locally ('Splunk Enterprise') or run on a cloud ('Splunk Cloud'). The Splunk integration along with Defender for IoT supports 'Splunk Enterprise' only.
>

### Download the Defender for IoT application in Splunk

To access the Defender for IoT application within Splunk, you need to download the application form the Splunkbase application store.

**To access the Defender for IoT application in Splunk**:

1. Navigate to the [Splunkbase](https://splunkbase.splunk.com/) application store.

1. Search for `CyberX ICS Threat Monitoring for Splunk`.

1. Select the CyberX ICS Threat Monitoring for Splunk application.

1. Select the **LOGIN TO DOWNLOAD BUTTON**.

## Next steps

> [!div class="nextstepaction"]
> [Integrations with Microsoft and partner services](integrate-overview.md)
