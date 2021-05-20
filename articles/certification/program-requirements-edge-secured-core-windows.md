---
title: Edge Secured-core Certification Requirements
description: Edge Secured-core Certification program requirements
author: cbroad
ms.author: cbroad
ms.topic: conceptual 
ms.date: 05/16/2021
ms.custom: Edge Secured-core Certification Requirements
ms.service: certification
---

## Windows Requirements


---

|Name|SecuredCore.Built-in.Security|
|:---|:---|
|Status|Required|
|Description|The purpose of the test is to make sure devices can report security information and events by sending data to Azure Defender for IoT.|
|Target Availability|2021|
|Applies To|Any device|
|OS|Windows, Linux Ubuntu 18.04, Linux Ubuntu 20.04|
|Requirements dependency|Azure Defender for IoT|
|Validation Type|Manual/Tools|
|Validation	|Device must generate security logs and alerts. Device logs and alerts messages to Azure Security Center.<ol><li>Download and deploy security agent from GitHub</li><li>Validate alert message from Azure Defender for IoT.</li></ol>|
|Resources|[Azure Docs IoT Defender for IoT](../defender-for-iot/how-to-configure-agent-based-solution.md)|

---

|Name|SecuredCore.Encryption.Storage|
|:---|:---|
|Status|Required|
|Description|The purpose of the test to validate that sensitive data can be encrypted on non-volatile storage.|
|Target Availability|2021|
|Applies To|Any device|
|OS|Agnostic|
|Requirements dependency|??|
|Validation Type|Manual/Tools|
|Validation|Device to be validated through toolset to ensure storage encryption is enabled and default algorithm is XTS-AES, with key length 128 bits or higher.|
|Resources||

---
|Name|SecuredCore.Hardware.SecureEnclave|
|:---|:---|
|Status|Optional|
|Description|The purpose of the test to validate the existence of a secure enclave and that the enclave is accessible from a secure agent.|
|Target Availability|2021|
|Applies To|Any device|
|OS|Agnostic|
|Requirements dependency|??|
|Validation Type|Manual/Tools|
|Validation|Device to be validated through toolset to ensure the Azure Security Agent can communicate with the secure enclave|
|Resources|https://github.com/openenclave/openenclave/blob/master/samples/BuildSamplesLinux.md|

---
|Name|SecuredCore.Hardware.Identity|
|:---|:---|
|Status|Required|
|Description|The purpose of the test is to validate the device identify is rooted in hardware.|
|Target Availability|2021|
|Applies To|Any device|
|OS|Agnostic|
|Requirements dependency|??|
|Validation Type|Manual/Tools|
|Validation|Device to be validated through toolset to ensure that the device has a TPM present and that it can be provisioned through IoT Hub using TPM endorsement key.|
|Resources|[Setup auto provisioning with DPS](../iot-dps/quick-setup-auto-provision.md)|

---
|Name|SecuredCore.Update|
|:---|:---|
|Status|Required|
|Description|The purpose of the test is to validate the device can receive and update its firmware and software.|
|Target Availability|2021|
|Applies To|Any device|
|OS|Agnostic|
|Requirements dependency|??|
|Validation Type|Manual/Tools|
|Validation|Partner confirmation that they were able to send an update to the device through Microsoft update, Azure Device update, or other approved services.|
|Resources|[Device Update for IoT Hub](../iot-hub-device-update/index.yml)|

---

|Name|SecuredCore.Manageability.Reset|
|:---|:---|
|Status|Required|
|Description|The purpose of this test is to validate the device against two use cases: a) Ability to perform a reset (remove user data, remove user configs), b) Restore device to last known good in the case of an update causing issues.|
|Target Availability|2021|
|Applies To|Any device|
|OS|Agnostic|
|Requirements dependency|??|
|Validation Type|Manual/Tools|
|Validation|Device to be validated through a combination of toolset and submitted documentation that the device supports this functionality. The device manufacturer can determine whether to implement these capabilities to support remote reset or only local reset.|
|Resources||

---



|Name|SecuredCore.Encryption.TLS|
|:---|:---|
|Status|Required|
|Description|The purpose of the test is to validate support for required TLS versions and cipher suites.|
|Target Availability|2021|
|Applies To|Any device|
|OS|Agnostic|
|Requirements dependency|??|
|Validation Type|Manual/Tools|
Validation|Device to be validated through toolset to ensure the device supports a minimum TLS version of 1.2 and supports the following required TLS cipher suites.<ul><li>TLS_RSA_WITH_AES_128_GCM_SHA256</li><li>TLS_RSA_WITH_AES_128_CBC_SHA256</li><li>TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256</li><li>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256</li><li>TLS_DHE_RSA_WITH_AES_128_GCM_SHA256</li><li>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256</li><li>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256</li></ul>|
|Resources| [TLS support in IoT Hub](../iot-hub/iot-hub-tls-support.md) <br /> [TLS Cipher suites in Windows 10](https://docs.microsoft.com/windows/win32/secauthn/tls-cipher-suites-in-windows-10-v1903) |

---

|Name|SecuredCore.Firmware.SecureBoot|
|:---|:---|
|Status|Required|
|Description|The purpose of the test is to validate the boot integrity of the device.|
|Target Availability|2021|
|Applies To|Any device|
|OS|Agnostic|
|Requirements dependency|UEFI, UBoot|
|Validation Type|Manual/Tools|
|Validation|Device to be validated through toolset to ensure that firmware and kernel signatures are validated every time the device boots. <ul><li>UEFI: Secure boot is enabled</li><li>Uboot: Verified boot is enabled</li></ul>|
|Resources||

---
|Name|SecuredCore.Protection.CodeIntegrity|
|:---|:---|
|Status|Required|
|Description|The purpose of this test is to validate that code integrity is available on this device.|
|Target Availability|2021|
|Applies To|Any device|
|OS|Agnostic|
|Requirements dependency|??|
|Validation Type|Manual/Tools|
|Validation|Device to be validated through toolset to ensure that code integrity is enabled. </br> Windows: HVCI </br> Linux: dm-verity and IMA|
|Resources||

---
|Name|SecuredCore.Protection.NetworkServices|
|:---|:---|
|Status|Required|
|Description|The purpose of the test is to validate that applications accepting input from the network are not running with elevated privileges.|
|Target Availability|2021|
|Applies To|Any device|
|OS|Agnostic|
|Requirements dependency||
|Validation Type|Manual/Tools|
|Validation|Device to be validated through toolset to ensure that services accepting network connections are not running with SYSTEM or root privileges.|
|Resources||

---
|Name|SecuredCore.Protection.Baselines|
|:---|:---|
|Status|Required|
|Description|The purpose of the test is to validate that the system conforms to a baseline security configuration.|
|Target Availability|2021|
|Applies To|Any device|
|OS|Agnostic|
|Requirements dependency|Azure Defender for IoT|
|Validation Type|Manual/Tools|
|Validation|Device to be validated through toolset to ensure that Defender IOT system configurations benchmarks have been run.|
|Resources| https://techcommunity.microsoft.com/t5/microsoft-security-baselines/bg-p/Microsoft-Security-Baselines <br> https://www.cisecurity.org/cis-benchmarks/ |

---
|Name|SecuredCore.Firmware.Protection|
|:---|:---|
|Status|Required|
|Description|The purpose of the test is to ensure that device has adequate mitigations from Firmware security threats.|
|Target Availability|2021|
|Applies To|Any device|
|OS|Agnostic|
|Requirements dependency|DRTM + UEFI|
|Validation Type|Manual/Tools|
|Validation|Device to be validated through toolset to confirm it is protected from firmware security threats through one of the following approaches: <ul><li>DRTM + UEFI Management Mode mitigations</li><li>DRTM + UEFI Management Mode hardening</li><li>Approved FW that does SRTM + runtime firmware hardening</li></ul> |
|Resources| https://trustedcomputinggroup.org/ |

---
|Name|SecuredCore.Firmware.Attestation|
|:---|:---|
|Status|Required|
|Description|The purpose of the test is to ensure the device can remotely attest to the Microsoft Azure Attestation service.|
|Target Availability|2021|
|Applies To|Any device|
|OS|Agnostic|
|Requirements dependency|Azure Attestation Service|
|Validation Type|Manual/Tools|
|Validation|Device to be validated through toolset to ensure that platform boot logs and measurements of boot activity can be collected and remotely attested to the Microsoft Azure Attestation service.|
|Resources| [Microsoft Azure Attestation](../attestation/index.yml) |

---
|Name|SecuredCore.Hardware.MemoryProtection|
|:---|:---|
|Status|Required|
|Description|The purpose of the test is to validate that DMA is not enabled on externally accessible ports.|
|Target Availability|2021|
|Applies To|Any device|
|OS|Agnostic|
|Requirements dependency|Only if DMA capable ports exist|
|Validation Type|Manual/Tools|
|Validation|If DMA capable external ports exist on the device, toolset to validate that the IOMMU or SMMU is enabled and configured for those ports.|
|Resources||

---
|Name|SecuredCore.Protection.Debug|
|:---|:---|
|Status|Required|
|Description|The purpose of the test is to validate that debug functionality on the device is disabled.|
|Target Availability|2021|
|Applies To|Any device|
|OS|Agnostic|
|Requirements dependency|??|
|Validation Type|Manual/Tools|
|Validation|Device to be validated through toolset to ensure that debug functionality requires authorization to enable.|
|Resources||

## Policy Requirements ##
Some requirements of this program are based on a business agreement between your company and Microsoft. The following requirements are not validated through our test harness, but are required by your company in certifying the device.

---
|Name|SecuredCore.Updates.Duration|
|:---|:---|
|Status|Required|
|Description|The purpose of this policy is to ensure that the device remains secure.|
|Target Availability|2021|
|Applies To|Any device|
|OS|Agnostic|
|Validation Type|Manual|
|Validation|Commitment from submission that devices certified will be required to keep devices up to date for 60 months from date of submission. Specifications available to the purchaser and devices itself in some manner should indicate the duration for which their software will be updated.|
|Resources||

---
|Name|SecuredCore.Policy.Vuln.Disclosure|
|:---|:---|
|Status|Required|
|Description|The purpose of this policy is to ensure that there is a mechanism for collecting and distributing reports of vulnerabilities in the product.|
|Target Availability|2021|
|Applies To|Any device|
|OS|Agnostic|
|Validation Type|Manual|
|Validation|Documentation on the process for submitting and receiving vulnerability reports for the certified devices will be reviewed.|
|Resources||

---

|Name|SecuredCore.Policy.Vuln.Fixes|
|:---|:---|
|Status|Required|
|Description|The purpose of this policy is to ensure that vulnerabilities that are high/critical (using CVSS 3.0) are addressed within 180 days of the fix being available.|
|Target Availability|2021|
|Applies To|Any device|
|OS|Agnostic|
|Validation Type|Manual|
|Validation|Documentation on the process for submitting and receiving vulnerability reports for the certified devices will be reviewed.|
|Resources||

---
# Preview Implemented #
Add table here for Windows