---
title: Hybrid or air-gapped deployment path for sensor management - Microsoft Defender for IoT
description: Learn about additional steps involved in deploying Microsoft Defender for IoT in a hybrid or air-gapped environment.
ms.topic: install-set-up-deploy
ms.date: 08/07/2023
---

# Deploy hybrid or air-gapped OT sensor management

> [!IMPORTANT]
> The [legacy on-premises management console](../legacy-central-management/legacy-air-gapped-deploy.md) is planned for deprecation on September 1, 2023. Instead, we recommend using either Microsoft cloud services or existing IT infrastructure for central monitoring and maintenance in hybrid and air-gapped environments.
>
> For more information, see [Recommended architecture for hybrid or air-gapped environments](air-gapped-architecture.md).
>

Digital transformation across OT infrastructure has left many fully air-gapped organizations moving to some level of hybrid cloud connectivity. Rather than not using the cloud at all, many organizations focus on using cloud service securely. Attackers continue evolving in parallel, learning to travel laterally across silos and perimeters, creating an ever growing attack surface.

This article describes how to deploy Microsoft Defender for IoT when working in an air-gapped or hybrid environment.  Rather than keeping all Defender for IoT's maintenance infrastructure contained in a closed architecture, we recommend that you use existing IT infrastructure and any cloud services, especially Microsoft cloud services, to keep your security operations running smoothly and efficiently.

Wherever possible, we recommend that you configure cloud-to-cloud integrations from Azure for reliability to your partner services and lower costs. For example, see [Stream Defender for IoT cloud alerts to a partner SIEM](../integrations/send-cloud-data-to-partners.md).

## Architecture recommendations

The following image shows a high-level map of Defender for IoT's architecture guidance for air-gapped and hybrid networks.

:::image type="content" source="../media/on-premises-architecture/on-premises-architecture.png" alt-text="Diagram of the new architecture for hybrid and air-gapped support.":::

In this architecture, three sensors connect to four routers in different logical zones across the organization. The sensors sit behind a firewall, and support local, on-premises maintenance abilities, such as saving local backup files, forwarding alerts to an on-premises security event and information management (SIEM) system, and more.

## Deployment steps

Use the following steps to deploy a Defender for IoT system in an air-gapped or hybrid environment:

1. Complete deploying each OT network sensor according to your plan, as described in [Deploy Defender for IoT for OT monitoring](ot-deploy-path.md).
1. For each sensor, configure the following settings:

    - [Proxy settings](../connect-sensors.md)
    - [Health monitoring via a SNMP MIB server](../how-to-set-up-snmp-mib-monitoring.md)
    - [Backup files](../back-up-restore-sensor.md), including [Save your backup to an external server (SMB)](../back-up-restore-sensor.md#save-your-backup-to-an-external-server-smb).
    - Alert forwarding to a SIEM, either on-premises or a cloud SIEM like Microsoft Sentinel. For more information, see:

        - [Tutorial: Connect Microsoft Defender for IoT with Microsoft Sentinel](../iot-solution.md)
        - [Forward on-premises OT alert information](../how-to-forward-alert-information-to-partners.md)

<!--what else should we list here?-->
<!--how do you do zero trust in air-gapped?-->

## Next steps

> [!div class="step-by-step"]
> [Maintain OT network sensors from the sensor console](../how-to-manage-individual-sensors.md)
