---
title: Access sensors using a proxy
description: "Enhance system security by preventing user login directly to the sensor. Instead leverage proxy tunneling to let users access the sensor directly from the Om-premises management console with a single firewall rule."
author: mlottner
ms.author: mlottner
ms.date: 08/31/2020
ms.topic: article
ms.service: azure
---

# Proxy tunneled connection to the management console 

Enhance system security by preventing user login directly to the sensor. Instead leverage proxy tunneling to let users access the sensor directly from the Om-premises management console with a single firewall rule. This narrows the possibility of unauthorized access to the network environment beyond the sensor.

  :::image type="content" source="media/how-to-access-sensors-using-a-proxy/image310.png" alt-text="user":::

Perform the following to set up tunneling, including:

  - Connect a Sensor to the Management Console

  - Setup Tunneling on the Management Console

## Setup tunneling on the management console appliance 

**To enable tunnelling:**

1.  Log in to the management console appliance CLI with administrative credentials.

2. Type: `sudo cyberx-management-tunnel-enable.`

3. Select enter.

4. Type `--port 10000`.



