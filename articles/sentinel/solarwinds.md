---
title: Using Microsoft resources to respond to supply-chain attacks and systemic-identity compromises | Microsoft Docs
description: Learn how to use Microsoft resources to respond to supply-chain attacks and systemic-identity compromises similar to the SolarWinds attack (Solorigate).
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''

ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2020
ms.author: bagol

---

# Using Microsoft resources to respond to supply-chain attacks and systemic-identity compromises

In December 2020, [FireEye discovered a nation-state cyber-attack on SolarWinds software](https://www.fireeye.com/blog/threat-research/2020/12/evasive-attacker-leverages-solarwinds-supply-chain-compromises-with-sunburst-backdoor.html).

Providing full security coverage is a [shared responsibility](/azure/security/fundamentals/shared-responsibility). This section provides information both about the steps that Microsoft has taken to shut down the SolarWinds attack, as well as the steps that you should take to make sure that your environment is clean and follows security best practices moving forward.

You can also use these resources described in this section to understand and mitigate risks from similar supply-chain attacks and systemic-identity compromises.

The SolarWinds attack provided the attackers with a foothold into affected networks, which they then used to gain elevated credentials. The attackers then used those credentials to access global administrator accounts or trusted SAML token-signing certificates. 

The global administrator account or certificates enabled the attackers to forge SAML tokens that can impersonate any of the organization's existing users and accounts, including highly privileged accounts.

Microsoft swiftly took the following steps against the SolarWinds attack:

1. **Disclosed the set of complex techniques** used by the advanced threat actor in the attack, affecting several key customers.

1. **Removed the digital certificates used by the files infected with the trojan,** causing all Windows systems to immediately stop trusting those compromised files. 

1. **Updated Microsoft Defender** to detect and alert and immediately quarantine an infected file on the system.

1. **Sinkholed one of the domains used** for the malware's command and control servers.

> [!IMPORTANT]
> The SolarWinds attack is an ongoing investigation, and our teams continue to act as first responders. As new information becomes available, we provide updates through the Microsoft Security Response Center (MSRC) blog at https://aka.ms/solorigate.
> 

## Securing your network after a system compromise

If you suspect that your organization has been affected by Solorigate or another similar attack, you will need to use Azure Sentinel, Microsoft Defender, and Azure Active Directory to identify risks and evidence of compromise, and then isolate resources and harden your system against attacks.

If you have Azure Sentinel, make sure that you set up connectors to both Microsoft Defender and Azure Active Directory, and start responding to threats by querying all the data in Azure Sentinel. 

For more information, see [Using Azure Sentinel to respond to supply-chain attacks and systemic-identity compromises](identity-compromise-azure-sentinel.md).

You can also use Microsoft Defender and Azure Active Directory directly to perform similar checks and hardening activities. For more information, see the procedures and queries detailed for [Microsoft Defender](identity-compromise-defender.md) and [Azure Active Directory](identity-compromise-aad.md).

Review the following articles to understand more about the Solorigate attack and how to identify similar threats in your environment.

- [Solorigate indicators of compromise (IOCs)](solarwinds-ioc-mitigate.md)
- [MITRE ATT&CK techniques observed in Solorigate](solarwinds-ioc-mitigate.md#mitre-attck-techniques-observed-in-solorigate)
- [Solorigate attacker behaviors](solarwinds-ioc-mitigate.md#more-solorigate-attacker-behaviors)

> [!NOTE]
> Depending on your configurations and identified risk, there may be steps described in this section that are not relevant for your organization, or that should be performed in a different order than listed. 
>
> We recommend performing as many checks as possible with an informed understanding of your network estate.
>

## Microsoft references for Solorigate

The following links were used in building this section of the documentation, and can be used to learn more about the Solorigate attack and mitigation activities:

|Source  |Links  |
|---------|---------|
|**Microsoft On The Issues**     |[Important steps for customers to protect themselves from recent nation-state cyberattacks](https://blogs.microsoft.com/on-the-issues/2020/12/13/customers-protect-nation-state-cyberattacks/)         |
|**Microsoft Security Response Center**     |  [Solorigate Resource Center: https://aka.ms/solorigate](https://aka.ms/solorigate) <br><br>[Customer guidance on recent nation-state cyber attack](https://msrc-blog.microsoft.com/2020/12/13/customer-guidance-on-recent-nation-state-cyber-attacks/)       |
|**Azure Active Directory Identity blog**     |  [Understanding "Solorigate"'s Identity IOCs - for Identity Vendors and their customers](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/understanding-quot-solorigate-quot-s-identity-iocs-for-identity/ba-p/2007610)       |
|**TechCommunity**     |    [Azure AD workbook to help you assess Solorigate risk](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/azure-ad-workbook-to-help-you-assess-solorigate-risk/ba-p/2010718) <br><br> [SolarWinds: Post compromise hunting with Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/solarwinds-post-compromise-hunting-with-azure-sentinel/ba-p/1995095)      |
|**Microsoft Security Intelligence**     |  [Malware encyclopedia definition: Solorigate](https://www.microsoft.com/en-us/wdsi/threats/malware-encyclopedia-description?Name=Trojan:MSIL/Solorigate.B!dha)       |
|**Microsoft Security blog**     |   [Analyzing Solorigate: The compromised DLL file that started a sophisticated cyberattack and how Microsoft Defender helps protect](https://www.microsoft.com/security/blog/2020/12/18/analyzing-solorigate-the-compromised-dll-file-that-started-a-sophisticated-cyberattack-and-how-microsoft-defender-helps-protect/)<br><br> [Advice for incident responders on recovery from system identity compromises](https://www.microsoft.com/security/blog/2020/12/21/advice-for-incident-responders-on-recovery-from-systemic-identity-compromises/) from the Detection and Response Team (DART) <br><br>[Using Microsoft 365 Defender to coordinate protection against Solorigate](https://www.microsoft.com/security/blog/2020/12/28/using-microsoft-365-defender-to-coordinate-protection-against-solorigate/)   <br><br>[Ensuring customers are protected from Solorigate](https://www.microsoft.com/security/blog/2020/12/15/ensuring-customers-are-protected-from-solorigate/)   |
| **GitHub resources**    |   [Azure Sentinel workbook for SolarWinds post-compromise hunting](https://github.com/Azure/Azure-Sentinel/blob/master/Workbooks/SolarWindsPostCompromiseHunting.json) <br><br>[Advanced Microsoft Defender query reference](#advanced-microsoft-defender-query-reference)      |
|     |         |
