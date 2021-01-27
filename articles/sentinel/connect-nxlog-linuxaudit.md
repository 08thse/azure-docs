---
title: Connect </MFR NAME> </PRODUCT NAME> data to Azure Sentinel| Microsoft Docs
description: Learn how to use the </MFR NAME> </PRODUCT NAME> data connector to pull </PRODUCT NAME> logs into Azure Sentinel. View </PRODUCT NAME> data in workbooks, create alerts, and improve investigation.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''

ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/27/2021
ms.author: yelevin

---
# Connect your NXLog LinuxAudit to Azure Sentinel

The NXLog LinuxAudit connector allows you to easily connect all your NXLog [LinuxAudit](https://nxlog.co/documentation/nxlog-user-guide/im_linuxaudit.html) security logs with Azure Sentinel to view dashboards, create custom alerts, and improve investigation. The NXLog LinuxAudit module supports custom audit rules and collects logs without auditd or any other user-space software. IP addresses and group/user ids are resolved to their respective names making [Linux audit](https://nxlog.co/documentation/nxlog-user-guide/linux-audit.html) logs more intelligible to security analysts. Integration between NXLog LinuxAudit and Azure Sentinel is facilitated via REST API.

> [!NOTE]
> Data will be stored in the geographic location of the workspace on which you are running Azure Sentinel.

## Configure and connect NXLog LinuxAudit

NXLog LinuxAudit can be configured to forward events in JSON format directly to Azure Sentinel.

1. In the Azure Sentinel portal, click **Data connectors** and select **NXLog LinuxAudit** connector.

2. Select **Open connector page**.

3. Follow the step-by-step instructions in the *NXLog User Guide* Integration Topic [Microsoft Azure Sentinel](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html) to configure forwarding via REST API.

## Find your data

After a successful connection is established, the data appears in Log Analytics under CustomLogs in the **LinuxAudit_CL** table.


## Validate connectivity
It may take upwards of 20 minutes until your logs start to appear in Log Analytics.


## Next steps
In this document, you learned how to connect NXLog LinuxAudit to Azure Sentinel. To learn more about Azure Sentinel, see the following articles:
- Learn how to [get visibility into your data, and potential threats](quickstart-get-visibility.md).
- Get started [detecting threats with Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use workbooks](tutorial-monitor-your-data.md) to monitor your data.
