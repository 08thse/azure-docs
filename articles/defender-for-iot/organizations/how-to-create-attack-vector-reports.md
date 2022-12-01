---
title: Create attack vector reports
description: Attack vector reports provide a graphical representation of a vulnerability chain of exploitable devices.
ms.date: 02/03/2022
ms.topic: how-to
---

# Create attack vector reports

Attack vector reports provide a graphical representation of a vulnerability chain of exploitable devices. These vulnerabilities can give an attacker access to key network devices. The Attack Vector Simulator calculates attack vectors in real time and analyzes all attack vectors for a specific target.

## Prerequisites

You must be an **Admin** or **Security Analyst** user to create an attack vector report.

## Generate an attack vector simulation

**To generate an attack vector simulation:**

1. Select **Attack vector** from the sensor side menu.
1. Select **Add simulation**.
1. Enter simulation properties:

    | Property  | Description  |
    |---------|---------|
    | **Name** | Simulation name |
    | **Maximum vectors** | The maximum number of vectors in a single simulation. |
    | **Show in Device map** | Show the attack vector as a group in the Device map. |
    | **All Source devices** | The attack vector will consider all devices as an attack source. |
    | **Attack Source** | The attack vector will consider only the specified devices as an attack source. |
    | **All Target devices** | The attack vector will consider all devices as an attack target. |
    | **Attack Target** | The attack vector will consider only the specified devices as an attack target. |
    | **Exclude devices** | Specified devices will be excluded from the attack vector simulation. |
    | **Exclude Subnets** | Specified subnets will be excluded from the attack vector simulation. |

1. Select **Save**.

## Attack vector report contents

You can use the report that is saved from the Attack vector page to review:

- network attack paths and insights
- a risk score
- source and target devices
- a graphical representation of attack vectors

:::image type="content" source="media/how-to-generate-reports/sample-attack-vectors.png" alt-text="Screen shot of Attack vectors report.":::

## Next steps

Working with the attack vector lets you evaluate the effect of mitigation activities in the attack sequence. You can now determine, for example, if a system upgrade disrupts the attacker's path by breaking the attack chain, or if an alternate attack path remains. This information helps you prioritize remediation and mitigation activities. 

For more information, see [fill this in](not-sure-yet.md).
