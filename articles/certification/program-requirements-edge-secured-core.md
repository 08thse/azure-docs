---
title: Edge Secured-core Certification Requirements
description: Edge Secured-core Certification program requirements
author: cbroad
ms.author: cbroad
ms.topic: conceptual 
ms.date: 06/21/2021
ms.custom: Edge Secured-core Certification Requirements
ms.service: certification
zone_pivot_groups: app-service-platform-windows-linux-sphere-rtos
---

# Azure Certified Device - Edge Secured-core #

## Edge Secured-Core certification requirements ##

::: zone pivot="platform-windows"

## Windows IoT OS Support
Edge Secured-core requires a version of Windows IoT that has at least 5 years of support from Microsoft remaining in its support lifecycle, at time of certification such as:
* [Windows 10 IoT Enterprise Lifecycle](/lifecycle/products/windows-10-iot-enterprise)
* [Windows 10 IoT Enterprise LTSC 2021 Lifecycle](/lifecycle/products/windows-10-iot-enterprise-ltsc-2021)
* [Windows 11 IoT Enterprise Lifecycle](/lifecycle/products/windows-11-iot-enterprise)

## Windows IoT Hardware/Firmware Requirements
> [!Note]
> Hardware must support and have the following enabled:
> * Intel or AMD virtualization extensions
> * Trusted Platform Module (TPM) 2.0
> * <b>For Intel systems:</b> Intel Virtualization Technology for Directed I/O (VT-d), Intel Trusted Execution Technology (TXT), and SINIT ACM driver package must be included in the Windows system image (for DRTM)
> * <b>For AMD systems:</b> AMD IOMMU and AMD-V virtualization, and SKINIT package must be integrated in the Windows system image (for DRTM)
> * Kernel DMA Protection (also known as Memory Access Protection)

---
</br>

|Name|SecuredCore.Hardware.Identity|
|:---|:---|
|Status|Required|
|Description|The device identity must be rooted in hardware.|
|Purpose|Protects against cloning and masquerading of the device root identity, which is key in underpinning trust in upper software layers extended through a chain-of-trust.|
|Dependencies|TPM v2.0 device|

---
</br>

|Name|SecuredCore.Hardware.MemoryProtection|
|:---|:---|
|Status|Required|
|Description|All DMA-enabled externally accessible ports must sit behind an enabled and appropriately configured IOMMU or SMMU.|
|Purpose|Protects against drive-by and other attacks that seek to use other DMA masters to bypass CPU memory integrity protections.|
|Dependencies|Enabled and appropriately configured IOMMU or SMMU|

---
</br>

|Name|SecuredCore.Firmware.Protection|
|:---|:---|
|Status|Required|
|Description|The device boot sequence must support DRTM alongside UEFI Management Mode mitigations.|
|Purpose|Protects against firmware weaknesses, untrusted code and rootkits that seeks to exploit early and privileged boot stages to bypass OS protections.|
|Dependencies|DRTM + UEFI|
|Resources| <ul><li>https://trustedcomputinggroup.org/</li><li>[Intel's DRTM based computing whitepaper](https://www.intel.com/content/dam/www/central-libraries/us/en/documents/drtm-based-computing-whitepaper.pdf)</li><li>[AMD Security whitepaper](https://www.amd.com/system/files/documents/amd-security-white-paper.pdf)</li></ul>|

---
</br>

|Name|SecuredCore.Firmware.SecureBoot|
|:---|:---|
|Status|Required|
|Description|UEFI Secure Boot must be enabled.|
|Purpose|Ensures that the firmware and OS kernel executed as part of the boot sequence have first been signed by a trusted authority.|
|Dependencies|UEFI|

---
</br>

|Name|SecuredCore.Firmware.Attestation|
|:---|:---|
|Status|Required|
|Description|The device identity, along with its platform boot logs and measurements, must be remotely attestable to the Microsoft Azure Attestation (MAA) Service.|
|Purpose|Enables services to establish the trustworthiness of the device, allowing for reliable security posture monitoring and other trust scenarios, such as the release of access credentials.|
|Dependencies|Microsoft Azure Attestation service|
|Resources| [Microsoft Azure Attestation](../attestation/index.yml)|

---

## Windows IoT configuration requirements
---
</br>

|Name|SecuredCore.Encryption.Storage|
|:---|:---|
|Status|Required|
|Description|Sensitive and private data must be encrypted at rest using BitLocker or similar, with encryption keys backed by hardware protection.|
|Purpose|Protects against exfiltration of sensitive or private data by unauthorized actors or tampered software.|

---
</br>

|Name|SecuredCore.Encryption.TLS|
|:---|:---|
|Status|Required|
|Description|The OS must support a minimum TLS version of 1.2 and have the following TLS cipher suites available and enabled:<ul><li>TLS_RSA_WITH_AES_128_GCM_SHA256</li><li>TLS_RSA_WITH_AES_128_CBC_SHA256</li><li>TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256</li><li>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256</li><li>TLS_DHE_RSA_WITH_AES_128_GCM_SHA256</li><li>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256</li><li>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256</li></ul>|
|Purpose|Ensures that applications are able to use end-to-end encryption protocols and ciphers that have no known weaknesses.|
|Dependencies|Windows 10 IoT Enterprise Version 1903 or greater. Note: other requirements might require greater versions for other services.|
|Resources| [TLS Cipher suites in Windows 10](/windows/win32/secauthn/tls-cipher-suites-in-windows-10-v1903)|

---
</br>

|Name|SecuredCore.Protection.CodeIntegrity|
|:---|:---|
|Status|Required|
|Description|The OS must have virtualization-based code integrity features enabled (VBS + HVCI).|
|Purpose|Protects against modified/malicious code from within the kernel by ensuring that only signed and immutable code is able to run.|
|Dependencies|VBS + HVCI is enabled on the device.|
|Resources| [Hypervisor-protected Code Integrity enablement](/windows-hardware/design/device-experiences/oem-hvci-enablement)|

---
</br>

|Name|SecuredCore.Protection.NetworkServices|
|:---|:---|
|Status|Required|
|Description|Services listening for input from the network must not run with elevated privileges. Exceptions may apply for security-related services.|
|Purpose|Limits the exploitability of compromised networked services.|

---

## Windows IoT Software/Service Requirements
---
</br>

|Name|SecuredCore.Built-in.Security|
|:---|:---|
|Status|Required|
|Description|Devices must be able to report security information and events by sending security logs and alerts to a cloud-native security monitoring solution, such as Microsoft Defender for Endpoint.|
|Purpose|Enables fleet posture monitoring, diagnosis of security threats, and protects against latent and in-progress attacks.|
|Resources|[Defender for Endpoint]( https://learn.microsoft.com/microsoft-365/security/defender-endpoint/configure-endpoints-script)|

---
</br>

|Name|SecuredCore.Protection.Baselines|
|:---|:---|
|Status|Required|
|Description|The system is able to successfully apply a baseline security configuration.|
|Purpose|Ensures a secure-by-default configuration posture, reducing the risk of compromise through incorrectly configured security-sensitive settings.|
|Resources| https://techcommunity.microsoft.com/t5/microsoft-security-baselines/bg-p/Microsoft-Security-Baselines <br> https://www.cisecurity.org/cis-benchmarks/|

|Name|SecuredCore.Protection.Update Resiliency|
|:---|:---|
|Status|Required|
|Description|The device must be restorable to the last known good state in the case of an update causing issues.|
|Purpose|Ensures that devices can be restored to a functional, secure and updatable state.|



## Windows IoT Policy Requirements

|Name|SecuredCore.Policy.Protection.Debug|
|:---|:---|
|Status|Required|
|Description|Debug functionality on the device must be disabled or require authorization to enable.|
|Purpose|Ensures that software protections can not be bypassed through debugger intervention and back-channels.|

---
</br>

|Name|SecuredCore.Policy.Manageability.Reset|
|:---|:---|
|Status|Required|
|Description|It must be possible to reset the device (remove user data, remove user configs).|
|Purpose|Protects against exfiltration of sensitive or private data during device ownership or lifecycle transitions.|

---
</br>

|Name|SecuredCore.Policy.Updates.Duration|
|:---|:---|
|Status|Required|
|Description|Software updates must be provided for at least 60 months from date of submission.|
|Purpose|Ensures a minimum period of continuous security.|

---
</br>

|Name|SecuredCore.Policy.Vuln.Disclosure|
|:---|:---|
|Status|Required|
|Description|A mechanism for collecting and distributing reports of vulnerabilities in the product must be available.|
|Purpose|Provides a clear path for discovered vulnerabilities to be reported, assessed and disclosed, enabling effective risk management and timely fixes.|
|Resources|https://msrc.microsoft.com/report/vulnerability/new|

---
</br>

|Name|SecuredCore.Policy.Vuln.Fixes|
|:---|:---|
|Status|Required|
|Description|Vulnerabilities that are high/critical (using CVSS 3.0) must be addressed within 180 days of the fix being available.|
|Purpose|Ensures that high-impact vulnerabilities are addressed in a timely manner, reducing likelihood and impact of a successful exploit.|

---
</br>

::: zone-end
::: zone pivot="platform-linux"

## Linux OS Support
>[!Note]
> Linux is not yet supported. The below represent expected requirements. Please fill out this [form](https://forms.office.com/r/HSAtk0Ghru) if you are interested in certifying a Linux device, including device HW and OS specs, and whether or not it meets each of the draft requirements below.

## Linux Hardware/Firmware Requirements

---
|Name|SecuredCore.Hardware.Identity|
|:---|:---|
|Status|Required|
|Description|The device identity must be rooted in hardware.|
|Purpose|Protects against cloning and masquerading of the device root identity, which is key in underpinning trust in upper software layers extended through a chain-of-trust.|
|Dependencies|TPM v2.0 </br><sup>or *other supported method</sup>|

---
</br>

|Name|SecuredCore.Hardware.MemoryProtection|
|:---|:---|
|Status|Required|
|Description|All DMA-enabled externally accessible ports must sit behind an enabled and appropriately configured IOMMU or SMMU.|
|Purpose|Protects against drive-by and other attacks that seek to use other DMA masters to bypass CPU memory integrity protections.|
|Dependencies|Enabled and appropriately configured IOMMU or SMMU|

---
</br>


|Name|SecuredCore.Firmware.Protection|
|:---|:---|
|Status|Required|
|Description|The device boot sequence must support either: <ul><li>Approved firmware with SRTM support + runtime firmware hardening</li><li>Firmware scanning and evaluation by approved Microsoft third party</li></ul>|
|Purpose|Protects against firmware weaknesses, untrusted code and rootkits that seeks to exploit early and privileged boot stages to bypass OS protections.|
|Dependencies||
|Resources| [Trusted Computing Group](https://trustedcomputinggroup.org/) |

---
</br>

|Name|SecuredCore.Firmware.SecureBoot|
|:---|:---|
|Status|Required|
|Description|Either:<ul><li>UEFI: Secure boot must be enabled</li><li>Uboot: Verified boot must be enabled</li></ul>|
|Purpose|Ensures that the firmware and OS kernel executed as part of the boot sequence have first been signed by a trusted authority.|

---
</br>

|Name|SecuredCore.Firmware.Attestation|
|:---|:---|
|Status|Required|
|Description|The device identity, along with its platform boot logs and measurements, must be remotely attestable to the Microsoft Azure Attestation (MAA) Service.|
|Purpose|Enables services to establish the trustworthiness of the device, allowing for reliable security posture monitoring and other trust scenarios, such as the release of access credentials.|
|Dependencies|TPM 2.0 </br><sup>or *supported OP-TEE based application chained to a HWRoT (Secure Element or Secure Enclave)</sup>|
|Resources| [Microsoft Azure Attestation](../attestation/index.yml)|

---
</br>

|Name|SecuredCore.Hardware.SecureEnclave|
|:---|:---|
|Status|Optional|
|Description|The device must feature a secure enclave capable of performing security functions.|


## Linux Configuration Requirements

---
|Name|SecuredCore.Encryption.Storage|
|:---|:---|
|Status|Required|
|Description|Sensitive and private data must be encrypted at rest using dm-crypt or similar, supporting XTS-AES as the default algorithm with a key length of 128 bits or higher, with encryption keys backed by hardware protection.|
|Purpose|Protects against exfiltration of sensitive or private data by unauthorized actors or tampered software.|

---
</br>

|Name|SecuredCore.Encryption.TLS|
|:---|:---|
|Status|Required|
|Description|The OS must support a minimum TLS version of 1.2 and have the following TLS cipher suites available and enabled:<ul><li>TLS_RSA_WITH_AES_128_GCM_SHA256</li><li>TLS_RSA_WITH_AES_128_CBC_SHA256</li><li>TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256</li><li>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256</li><li>TLS_DHE_RSA_WITH_AES_128_GCM_SHA256</li><li>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256</li><li>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256</li></ul>|
|Purpose|Ensures that applications are able to use end-to-end encryption protocols and ciphers that have no known weaknesses.|

---
</br>

|Name|SecuredCore.Protection.CodeIntegrity|
|:---|:---|
|Status|Required|
|Description|The OS must have dm-verity and IMA code integrity features enabled, with code operating under least privilege.|
|Purpose|Protects against modified/malicious code, ensuring that only signed/hashed code is able to run.|

---
</br>

|Name|SecuredCore.Protection.NetworkServices|
|:---|:---|
|Status|Required|
|Description|Services listening for input from the network must not run with elevated privileges, such as SYSTEM or root. Exceptions may apply for security-related services.|
|Purpose|Limits the exploitability of compromised networked services.|

## Linux Software/Service Requirements
---
|Name|SecuredCore.Built-in.Security|
|:---|:---|
|Status|Required|
|Description|Devices must be able to report security information and events by sending security logs and alerts to a cloud-native security monitoring solution, such as Microsoft Defender for Endpoint.|
|Purpose|Enables fleet posture monitoring, diagnosis of security threats, and protects against latent and in-progress attacks.|
|Resources|[Defender for Endpoint]( https://learn.microsoft.com/microsoft-365/security/defender-endpoint/configure-endpoints-script)|

---
</br>

|Name|SecuredCore.Manageability.Configuration|
|:---|:---|
|Status|Required|
|Description|The device must support auditing and setting of system configuration (and certain management actions such as reboot) through Azure. Note: Use of other system management toolchains (for example, Ansible, etc.) by operators are not prohibited, but the device must include the azure-osconfig agent such that it's ready to be managed from Azure.|
|Purpose|Enables the application of security baselines as part of a secure-by-default configuration posture, reducing the risk of compromise through incorrectly configured security-sensitive settings.|
|Dependency|azure-osconfig|

---
</br>

|Name|SecuredCore.Update|
|:---|:---|
|Status|Audit|
|Description|The device must be able to receive and update its firmware and software through Azure Device Update or other approved services.|
|Purpose|Enables continuous security.|

---
</br>

|Name|SecuredCore.UpdateResiliency|
|:---|:---|
|Status|Required|
|Description|The device must be restorable to the last known good state in the case of an update causing issues.|
|Purpose|Ensures that devices can be restored to a functional, secure and updatable state.|

---
</br>

|Name|SecuredCore.Protection.Baselines|
|:---|:---|
|Status|Required|
|Description|The system is able to successfully apply a baseline security configuration.|
|Purpose|Ensures a secure-by-default configuration posture, reducing the risk of compromise through incorrectly configured security-sensitive settings.|
|Resources|<ul><li>https://techcommunity.microsoft.com/t5/microsoft-security-baselines/bg-p/Microsoft-Security-Baselines</li><li>https://www.cisecurity.org/cis-benchmarks/</li><li>https://learn.microsoft.com/en-us/azure/governance/policy/samples/guest-configuration-baseline-linux</li></ul>|

---
</br>

|Name|SecuredCore.Protection.SignedUpdates|
|:---|:---|
|Status|Required|
|Description|Updates to the operating system, drivers, application software, libraries, packages and firmware must be signed.|
|Purpose|Prevents unauthorized or malicious code from being installed during the update process.|


## Linux Policy Requirements
---
|Name|SecuredCore.Policy.Protection.Debug|
|:---|:---|
|Status|Required|
|Description|Debug functionality on the device must be disabled or require authorization to enable.|
|Purpose|Ensures that software protections can not be bypassed through debugger intervention and back-channels.|

---
</br>

|Name|SecuredCore.Policy.Manageability.Reset|
|:---|:---|
|Status|Required|
|Description|It must be possible to reset the device (remove user data, remove user configs).|
|Purpose|Protects against exfiltration of sensitive or private data during device ownership or lifecycle transitions.|

---
</br>

|Name|SecuredCore.Policy.Updates.Duration|
|:---|:---|
|Status|Required|
|Description|Software updates must be provided for at least 60 months from date of submission.|
|Purpose|Ensures a minimum period of continuous security.|

---
</br>

|Name|SecuredCore.Policy.Vuln.Disclosure|
|:---|:---|
|Status|Required|
|Description|A mechanism for collecting and distributing reports of vulnerabilities in the product must be available.|
|Purpose|Provides a clear path for discovered vulnerabilities to be reported, assessed and disclosed, enabling effective risk management and timely fixes.|

---
</br>

|Name|SecuredCore.Policy.Vuln.Fixes|
|:---|:---|
|Status|Required|
|Description|Vulnerabilities that are high/critical (using CVSS 3.0) must be addressed within 180 days of the fix being available.|
|Purpose|Ensures that high-impact vulnerabilities are addressed in a timely manner, reducing likelihood and impact of a successful exploit.|

</br>
::: zone-end
<!---------------------------------------------->
<!---------------------------------------------->
<!---------------------------------------------->
::: zone pivot="platform-sphere"

## Azure Sphere platform Support
The Mediatek MT3620AN must be included in your design. Additional guidance for building secured Azure Sphere applications can be within the [Azure Sphere application notes](/azure-sphere/app-notes/app-notes-overview).


## Azure Sphere Hardware/Firmware Requirements

---
|Name|SecuredCore.Hardware.Identity|
|:---|:---|
|Status|Required|
|Description|The purpose of the requirement is to validate the device identity is rooted in hardware. This requirement is met by Microsoft for Azure Sphere based products.|

---
</br>

|Name|SecuredCore.Hardware.MemoryProtection|
|:---|:---|
|Status|Required|
|Description|The purpose of the requirement is to ensure that memory integrity helps protect the device from vulnerable peripherals. This requirement is met by Microsoft for Azure Sphere based products.|

</br>

---
|Name|SecuredCore.Firmware.Protection|
|:---|:---|
|Status|Required|
|Description|The purpose of the requirement is to ensure that device has adequate mitigations from firmware security threats. This requirement is met by Microsoft for Azure Sphere based products.|

---
</br>

|Name|SecuredCore.Firmware.SecureBoot|
|:---|:---|
|Status|Required|
|Description|The purpose of the requirement is to validate the boot integrity of the device. This requirement is met by Microsoft for Azure Sphere based products.|

---
</br>

|Name|SecuredCore.Firmware.Attestation|
|:---|:---|
|Status|Required|
|Description|The purpose of the requirement is to ensure the device can remotely attest to a Microsoft Azure Attestation service. This requirement is met by Microsoft for Azure Sphere based products.|

---
</br>

|Name|SecuredCore.Hardware.SecureEnclave|
|:---|:---|
|Status|Required|
|Description|The purpose of this requirement is to validate hardware security that is accessible from a secure operating system. This requirement is met by Microsoft for Azure Sphere based products.|

## Azure Sphere OS Configuration Requirements

---
|Name|SecuredCore.Encryption.Storage|
|:---|:---|
|Status|Required|
|Description|The purpose of this requirement is to validate that sensitive data can be encrypted on nonvolatile storage. This requirement is met by Microsoft for Azure Sphere based products.|

---
</br>

|Name|SecuredCore.Encryption.TLS|
|:---|:---|
|Status|Required|
|Description|The purpose of the requirement is to validate support for required TLS versions and cipher suites. This requirement is met by Microsoft for Azure Sphere based products.|
|Resources| [TLS support in IoT Hub](../iot-hub/iot-hub-tls-support.md) <br /> |

---
</br>

|Name|SecuredCore.Protection.CodeIntegrity|
|:---|:---|
|Status|Required|
|Description|The purpose of this requirement is to validate that authorized code runs with least privilege. This requirement is met by Microsoft for Azure Sphere based products.|

---
</br>

|Name|SecuredCore.Protection.NetworkServices|
|:---|:---|
|Status|Required|
|Description|The purpose of the requirement is to validate that applications accepting input from the network aren't running with elevated privileges. This requirement is met by Microsoft for Azure Sphere based products.|

---
</br>

|Name|SecuredCore.Protection.NetworkFirewall|
|:---|:---|
|Status|Required|
|Description|The purpose of this requirement is to validate that applications can't connect to endpoints that haven't been authorized. This requirement is met by Microsoft for Azure Sphere based products.|

## Azure Sphere Software/Service Requirements
---
|Name|SecuredCore.Built-in.Security|
|:---|:---|
|Status|Required|
|Description|The purpose of this requirement is to make sure devices can report security information and events by sending data to a Microsoft telemetry service. This requirement is met by Microsoft for Azure Sphere based products.|
|Resources|[Collect and interpret error data - Azure Sphere](/azure-sphere/deployment/interpret-error-data?tabs=cliv2beta)</br>[Configure crash dumps - Azure Sphere](/azure-sphere/deployment/configure-crash-dumps)|

---
</br>

|Name|SecuredCore.Manageability.Configuration|
|:---|:---|
|Status|Required|
|Description|The purpose of this requirement is to validate the device supports remote administration via service-based configuration control. This requirement is met by Microsoft for Azure Sphere based products.|

---
</br>

|Name|SecuredCore.Update|
|:---|:---|
|Status|Required|
|Description|The purpose of the requirement is to validate the device can receive and update its firmware and software. This requirement is met by Microsoft for Azure Sphere based products.|

---
</br>

|Name|SecuredCore.Protection.Baselines|
|:---|:---|
|Status|Required|
|Description|The purpose of the requirement is to validate that the system conforms to a baseline security configuration. This requirement is met by Microsoft for Azure Sphere based products.|

---
</br>

|Name|SecuredCore.Protection.SignedUpdates|
|:---|:---|
|Status|Required|
|Description|The purpose of the requirement is to validate that updates must be signed. This requirement is met by Microsoft for Azure Sphere based products.|

## Azure Sphere Policy Requirements
---
|Name|SecuredCore.Policy.Protection.Debug|
|:---|:---|
|Status|Required|
|Description|The purpose of the policy requires that debug functionality on the device is disabled. This requirement is met by Microsoft for Azure Sphere based products.|

---
</br>

|Name|SecuredCore.Policy.Manageability.Reset|
|:---|:---|
|Status|Required|
|Description|The policy requires that the device can execute two use cases: a) Ability to perform a reset (remove user data, remove user configurations), b) Restore device to last known good in the case of an update causing issues. This requirement is met by Microsoft for Azure Sphere based products.|

---
</br>

|Name|SecuredCore.Policy.Updates.Duration|
|:---|:---|
|Status|Required|
|Description|The purpose of this policy is to ensure that the device remains secure. This requirement is met by Microsoft for Azure Sphere based products.|

---
</br>

|Name|SecuredCore.Policy.Vuln.Disclosure|
|:---|:---|
|Status|Required|
|Description|The purpose of this policy is to ensure that there's a mechanism for collecting and distributing reports of vulnerabilities in the product. Azure Sphere vulnerabilities are collected by Microsoft through MSRC and are published to customers through the Tech Community Blog, Azure Sphere “What’s New” page, and through Mitre’s CVE database.|
|Resources|<ul><li>[Report an issue and submission guidelines](https://www.microsoft.com/msrc/faqs-report-an-issue)</li><li>[What's new - Azure Sphere](/azure-sphere/product-overview/whats-new)</li><li>[Azure Sphere CVEs](/azure-sphere/deployment/azure-sphere-cves)</li></ul>|

---
</br>

|Name|SecuredCore.Policy.Vuln.Fixes|
|:---|:---|
|Status|Required|
|Description|The purpose of this policy is to ensure that vulnerabilities that are high/critical (using CVSS 3.0) are addressed within 180 days of the fix being available. This requirement is met by Microsoft for Azure Sphere based products.|



</br>
::: zone-end
