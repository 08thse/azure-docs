---
title: Azure security baseline for Cloud Shell
description: The Cloud Shell security baseline provides procedural guidance and resources for implementing the security recommendations specified in the Azure Security Benchmark.
author: msmbaldwin
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 01/01/2000
ms.author: mbaldwin
ms.custom: subject-security-benchmark

# Important: This content is machine generated; do not modify this topic directly. Contact mbaldwin for more information.

---

# Azure security baseline for Cloud Shell

This security
baseline applies guidance from the [Azure Security Benchmark version
1.0](../security/benchmarks/overview-v1.md) to Cloud Shell. The Azure Security Benchmark
provides recommendations on how you can secure your cloud solutions on Azure.
The content is grouped by the **security controls** defined by the Azure
Security Benchmark and the related guidance applicable to Cloud Shell. **Controls** not applicable to Cloud Shell have been excluded.

 
To see how Cloud Shell completely maps to the Azure
Security Benchmark, see the [full Cloud Shell security baseline mapping
file](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

>[!WARNING]
>This preview version of the article is for review only. **DO NOT MERGE INTO MASTER!**

## Network Security

*For more information, see the [Azure Security Benchmark: Network Security](../security/benchmarks/security-control-network-security.md).*

### 1.1: Protect Azure resources within virtual networks

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33504).

**Guidance**: Customers may deploy Azure Cloud Shell into a customer owned Virtual Network.

When you deploy Azure Cloud Shell into a customer owned Virtual Network, you must create or use an existing virtual network. Ensure that the chosen virtual network has a network security group applied to its subnets and network access controls configured specific to your application's trusted ports and sources.

- [Deploy Cloud Shell into an Azure virtual network](private-vnet.md)

- [How to create a network security group with security rules](../virtual-network/tutorial-filter-network-traffic.md)

- [How to deploy and configure Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 1.2: Monitor and log the configuration and traffic of virtual networks, subnets, and NICs

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33505).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; Cloud Shell does not offer specific capabilities to log or monitor network traffic.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

### 1.3: Protect critical web applications

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33506).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; this recommendation is intended for web applications running on Azure App Service or compute resources. Azure Cloud Shell is a browser-based command-line experience that is used for interactive management of cloud resources.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

### 1.4: Deny communications with known malicious IP addresses

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33507).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; Azure Cloud Shell is a browser-based command-line experience that is ephemeral and interactive.  The shell experience that the customer is given will not be a consistent IP address.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

### 1.5: Record network packets

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33508).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; this recommendation is intended for offerings that produce network packets that can be recorded and viewed by customers. Azure Cloud Shell does not produce network packets that are customer facing.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

### 1.6: Deploy network-based intrusion detection/intrusion prevention systems (IDS/IPS)

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33509).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; this recommendation is intended for offerings that deploy into Azure virtual networks. Azure Cloud Shell is a browser-based command-line experience that is ephemeral and interactive. Cloud Shell does not offer specific capabilities to enable network intrusion detection or prevention.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

### 1.7: Manage traffic to web applications

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33510).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; this recommendation is intended for offerings that can host web applications such as App Service and Azure Virtual Machines. Azure Cloud Shell is not intended to host web applications, so there is no need to describe managing traffic to those applications. Azure Cloud Shell is a browser-based command-line experience that is used for interactive management of cloud resources.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

### 1.8: Minimize complexity and administrative overhead of network security rules

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33511).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; this recommendation is intended for offerings that can be deployed into Azure Virtual Networks, or have the capability to define groupings of allowed IP ranges for efficient management. Azure Cloud Shell does not currently support service tags, and is not designed to deploy into Azure virtual networks permanently. Cloud Shell does not support an offer-specific capability to manage network security rules.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

### 1.9: Maintain standard security configurations for network devices

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33512).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; Azure Cloud Shell is a browser-based command-line experience that is ephemeral and interactive. Cloud Shell does not offer specific capabilities or settings.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

### 1.10: Document traffic configuration rules

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33513).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; Azure Cloud Shell is a browser-based command-line experience that is ephemeral and interactive that is used for interactive management of cloud resources.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

### 1.11: Use automated tools to monitor network resource configurations and detect changes

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33514).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; Cloud Shell does not offer specific capabilities to log or monitor network-related resource configuration changes.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

## Logging and Monitoring

*For more information, see the [Azure Security Benchmark: Logging and Monitoring](../security/benchmarks/security-control-logging-monitoring.md).*

### 2.1: Use approved time synchronization sources

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33515).

>[!NOTE]
>Because the Responsibility field is set to "Microsoft", this section will be omitted from the published baseline.

**Guidance**: Not applicable; Azure Cloud Shell does not support configuring your own time synchronization sources. The Azure Cloud Shell service relies on Microsoft time synchronization sources, and is not exposed to customers for configuration.

**Responsibility**: Microsoft

**Azure Security Center monitoring**: None

### 2.2: Configure central security log management

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33516).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; Azure Cloud Shell does not support or produce any Azure Activity logging for changes to its resources.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

### 2.3: Enable audit logging for Azure resources

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33517).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; Azure Cloud Shell does not support or produce any Azure Activity logging for changes to its resources.

Azure Cloud Shell is a browser-based command-line experience that is used for interactive management of cloud resources, actions that are taken within Cloud Shell function the same as actions taken from a tool such as Azure PowerShell or Azure CLI.  Actions from individual tools can be monitored or logged but not implemented in Azure Cloud Shell.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

### 2.4: Collect security logs from operating systems

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33518).

>[!NOTE]
>Because the Responsibility field is set to "Microsoft", this section will be omitted from the published baseline.

**Guidance**: Not applicable; Azure Cloud Shell does not expose any operating system configurations or security logs to customers. Microsoft is responsible for monitoring the underlying service's compute infrastructure.

Azure Cloud Shell is a browser-based command-line experience that is used for interactive management of cloud resources, the container for the customer's session is ephemeral and not able to be customized by the user.

**Responsibility**: Microsoft

**Azure Security Center monitoring**: None

### 2.5: Configure security log storage retention

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33519).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; Azure Cloud Shell does not currently produce any Azure Activity logs, diagnostic logs, or other logs. For this reason, the customer is unable to set retention settings for logs related to Azure Cloud Shell resources.

Azure Cloud Shell is a browser-based command-line experience that is used for interactive management of cloud resources, actions that are taken within Cloud Shell function the same as actions taken from a tool such as Azure PowerShell or Azure CLI.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

### 2.6: Monitor and review Logs

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33520).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; Azure Cloud Shell does not currently produce customer facing logs. For this reason, customers are unable to review and monitor logs related to Azure Cloud Shell resources.

Azure Cloud Shell is a browser-based command-line experience that is used for interactive management of cloud resources, actions that are taken within Cloud Shell function the same as actions taken from a tool such as Azure PowerShell or Azure CLI.  Actions from individual tools can be monitored or logged but not implemented in Azure Cloud Shell.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

### 2.7: Enable alerts for anomalous activities

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33521).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; Azure Cloud Shell does not currently support ingesting its logs to Azure Monitor. For this reason, you are unable to create Azure Monitor alerts for anomalous activity logs related to Azure Cloud Shell resources.

Azure Cloud Shell is a browser-based command-line experience that is used for interactive management of cloud resources, actions that are taken within Cloud Shell function the same as actions taken from a tool such as Azure PowerShell or Azure CLI.  Actions from individual tools can be monitored or logged but not implemented in Azure Cloud Shell.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

### 2.8: Centralize anti-malware logging

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33522).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; Azure Cloud Shell does not produce or expose any anti-malware logging to customers. For all Microsoft-managed resources, Microsoft handles the anti-malware logging.

Azure Cloud Shell is a browser-based command-line experience that is used for interactive management of cloud resources, the container for the customer's session is ephemeral and not able to be customized by the user.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

### 2.9: Enable DNS query logging

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33523).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; Azure Cloud Shell does not produce or process DNS query logs.

Azure Cloud Shell is a browser-based command-line experience that is used for interactive management of cloud resources, the container that the customer's session running on is ephemeral and is not able to be customized by the user.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

### 2.10: Enable command-line audit logging

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33524).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; Azure Cloud Shell is a browser-based command-line experience that is used for interactive management of cloud resources, actions that are taken within Cloud Shell function the same as actions taken from a tool such as Azure PowerShell or Azure CLI.  Actions from individual tools should be monitored or logged if desired.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

## Identity and Access Control

*For more information, see the [Azure Security Benchmark: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### 3.1: Maintain an inventory of administrative accounts

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33525).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; Azure Cloud Shell does not have local level administrator accounts, which would be inventoried by the customer.

Azure Cloud Shell is a browser-based command-line experience that is used for interactive management of cloud resources, actions that are taken within Cloud Shell function the same as actions taken from a tool such as Azure PowerShell or Azure CLI, customers are only to perform actions on other Azure resources where they have appropriate permissions.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

### 3.2: Change default passwords where applicable

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33526).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; Azure Cloud Shell doesn't have the concept of default passwords.

Azure Cloud Shell is a browser-based command-line experience that is used for interactive management of cloud resources, Cloud Shell uses the same authorization that is used to access the Azure portal.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

### 3.3: Use dedicated administrative accounts

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33527).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; Azure Cloud Shell does not have the concept of any local-level or Azure Active Directory (Azure AD) administrator accounts which customers can use or manage.

Azure Cloud Shell is a browser-based command-line experience that is used for interactive management of cloud resources, actions that are taken within Cloud Shell function the same as actions taken from a tool such as Azure PowerShell or Azure CLI, customers are only to perform actions on other Azure resources where they have appropriate permissions.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

### 3.4: Use single sign-on (SSO) with Azure Active Directory

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33528).

**Guidance**: Azure Cloud Shell is a browser-based command-line experience that uses the same authorization that is used to access the Azure portal, in this case an SSO into the Azure portal will also authenticate you with Cloud Shell.

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 3.5: Use multi-factor authentication for all Azure Active Directory based access

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33529).

**Guidance**: Azure Cloud Shell is a browser-based command-line experience that uses the same authorization that is used to access the Azure portal, in this case any multifactor authentication that is required to connect to the Azure portal will also be required for Cloud Shell.

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 3.6: Use dedicated machines (Privileged Access Workstations) for all administrative tasks

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33530).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; Azure Cloud Shell doesn't support management from a customer workstation.

Azure Cloud Shell is a browser-based command-line experience that is used for interactive management of cloud resources.  Cloud Shell can be accesses from any machine with a browser.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

### 3.7: Log and alert on suspicious activities from administrative accounts

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33531).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; Azure Cloud Shell doesn't use any administrative accounts.

Azure Cloud Shell is a browser-based command-line experience that is used for interactive management of cloud resources, actions that are taken within Cloud Shell function the same as actions taken from a tool such as Azure PowerShell or Azure CLI, customers are only to perform actions on other Azure resources where they have appropriate permissions.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

### 3.8: Manage Azure resources only from approved locations

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33532).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; Azure Cloud Shell doesn't support capability of approved location as condition for access.

Azure Cloud Shell is a browser-based command-line experience that is used for interactive management of cloud resources.  Cloud Shell can be accesses from any machine with a browser.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

### 3.9: Use Azure Active Directory

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33533).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; Azure Cloud Shell is a browser-based command-line experience that is used for interactive management of cloud resources, Cloud Shell uses the same authorization that is used to access the Azure portal so Cloud Shell is not interacted with Azure Active Directory (Azure AD) directly.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

### 3.10: Regularly review and reconcile user access

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33534).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; Azure Cloud Shell is a browser-based command-line experience that is used for interactive management of cloud resources, actions that are taken within Cloud Shell function the same as actions taken from a tool such as Azure PowerShell or Azure CLI, customers are only to perform actions on other Azure resources where they have appropriate permissions.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

### 3.11: Monitor attempts to access deactivated credentials

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33535).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; Azure Cloud Shell is a browser-based command-line experience that is used for interactive management of cloud resources, Cloud Shell uses the same authorization that is used to access the Azure portal.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

### 3.12: Alert on account login behavior deviation

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33536).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; Azure Cloud Shell is a browser-based command-line experience that is used for interactive management of cloud resources, Cloud Shell uses the same authorization that is used to access the Azure portal.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

### 3.13: Provide Microsoft with access to relevant customer data during support scenarios

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33537).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; Cloud Shell does not have any
administrative operations, which provide access to customer data and require
lockbox support.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

## Data Protection

*For more information, see the [Azure Security Benchmark: Data Protection](../security/benchmarks/security-control-data-protection.md).*

### 4.1: Maintain an inventory of sensitive Information

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33538).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; Cloud Shell is intended for ad-hoc management of cloud resources, not the storage, or transport of sensitive materials.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

### 4.2: Isolate systems storing or processing sensitive information

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33539).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; Customers are only able to isolate Cloud Shell by using a Virtual Network, documentation can be found here: https://aka.ms/cloudshell/docs/vnet
Cloud Shell runs on top of Microsoft managed and owned containers, we do not support additional forms of isolation.
Cloud Shell doesn't handle customer data directly.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

### 4.3: Monitor and block unauthorized transfer of sensitive information

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33540).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; Azure Cloud Shell is a browser-based command-line experience that is used for interactive management of cloud resources, actions that are taken within Cloud Shell function the same as actions taken from a tool such as Azure PowerShell or Azure CLI.  Actions from individual tools should be monitored or logged if desired.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

### 4.4: Encrypt all sensitive information in transit

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33541).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; TLS 1.2 is supported but Cloud Shell doesn't handle customer data or sensitive data directly.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

### 4.5: Use an active discovery tool to identify sensitive data

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33542).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; Azure Cloud Shell is a browser-based command-line experience that is used for interactive management of cloud resources.  Cloud Shell requires that the customer links Cloud Shell to a storage account, this storage account is used for saving any of the customer's data.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

### 4.6: Use Azure RBAC to manage access to resources

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33543).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; Azure Cloud Shell is a browser-based command-line experience that is used for interactive management of cloud resources, actions that are taken within Cloud Shell function the same as actions taken from a tool such as Azure PowerShell or Azure CLI.  RBAC roles should be used for other services that wish to restrict access from Cloud Shell.  Access to Cloud Shell cannot be restricted via RBAC roles.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

### 4.7: Use host-based data loss prevention to enforce access control

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33544).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; Azure Cloud Shell containers  do not offer host-based data loss prevention.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

### 4.8: Encrypt sensitive information at rest

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33545).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; Azure Cloud Shell does not store customer data at rest.  Cloud Shell does leverage an Azure File Share that is owned by the customer, all data at rest is stored in the File Share.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

### 4.9: Log and alert on changes to critical Azure resources

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33546).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; Azure Cloud Shell does not support Azure Monitor along with Azure Activity Log, and thus cannot use fundamental monitoring functions for critical Azure resources and audit their activity.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

## Vulnerability Management

*For more information, see the [Azure Security Benchmark: Vulnerability Management](../security/benchmarks/security-control-vulnerability-management.md).*

### 5.1: Run automated vulnerability scanning tools

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33547).

**Guidance**: Azure Cloud Shell is a browser-based command-line experience that is used for interactive management of cloud resources.  Each customer container is ephemeral a new container is used for each session.  The container images are monitored and updated by the Cloud Shell team.

Azure Cloud Shell allows customers to install their own tools or software in their own image per their organizational needs.

Customers are responsible to run automated vulnerability scanning tools against software that are running in the environment.

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 5.2: Deploy automated operating system patch management solution

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33548).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; Azure Cloud Shell is a browser-based command-line experience that is used for interactive management of cloud resources.  Each customer container is ephemeral a new container is used for each session.  The container images are monitored and updated by the Cloud Shell team.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

### 5.3: Deploy an automated patch management solution for third-party software titles

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33549).

**Guidance**: Not applicable; Azure Cloud Shell is a browser-based command-line experience that is used for interactive management of cloud resources.  Each customer container is ephemeral a new container is used for each session.  The container images are monitored and updated by the Cloud Shell team.

Azure Cloud Shell allows customers to install their own tools or software in their own image per their organizational needs.

Customers are responsible for software patch management running in their environment.

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 5.4: Compare back-to-back vulnerability scans

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33550).

**Guidance**: Azure Cloud Shell is a browser-based command-line experience that is used for interactive management of cloud resources.  Each customer container is ephemeral a new container is used for each session.  The container images are monitored and updated by the Cloud Shell team.

Azure Cloud Shell allows customers to install their own tools or software in their own image per their organizational needs.

Customers are responsible to remediate vulnerabilities that are discovered through their software vulnerability scans. Export scan results at consistent intervals and compare the results with previous scans to verify that vulnerabilities have been remediated. When using vulnerability management recommendations suggested by Azure Security Center, you can pivot into the selected solution's portal to view historical scan data.

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 5.5: Use a risk-rating process to prioritize the remediation of discovered vulnerabilities

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33551).

**Guidance**: Azure Cloud Shell is a browser-based command-line experience that is used for interactive management of cloud resources.  Each customer container is ephemeral a new container is used for each session.  The container images are monitored and updated by the Cloud Shell team.

Azure Cloud Shell allows customers to install their own tools or software in their own image per their organizational needs.

Customers are responsible to remediate vulnerabilities that are discovered through their software vulnerability scans.  Use a common risk scoring program (for example, Common Vulnerability Scoring System) or the default risk ratings provided by your third-party scanning tool. 

- [NIST Publication--Common Vulnerability Scoring System](https://www.nist.gov/publications/common-vulnerability-scoring-system)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

## Inventory and Asset Management

*For more information, see the [Azure Security Benchmark: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md).*

### 6.1: Use automated asset discovery solution

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33552).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; Azure Cloud Shell does not integrate with or allow automated asset discovery, as there are no customer owned assets.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

### 6.2: Maintain asset metadata

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33553).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; Azure Cloud Shell does not integrate with or allow automated asset discovery, as there are no customer owned assets.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

### 6.3: Delete unauthorized Azure resources

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33554).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; Azure Cloud Shell does not integrate with or allow automated asset discovery, as there are no customer owned assets.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

### 6.4: Define and maintain an inventory of approved Azure resources

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33555).

**Guidance**: Azure Cloud Shell is a browser-based command-line experience that is used for interactive management of cloud resources.  Each customer container is ephemeral a new container is used for each session.  The container images and tools are monitored and updated by the Cloud Shell team.  The customer is able to install their own tools in their own image per their organizational needs and the tools does not require `sudo` permissions during install.

Customers are recommended to create an inventory of approved software that is installed through Azure Cloud Shell as per your organizational needs.

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 6.5: Monitor for unapproved Azure resources

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33556).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; All resources to run Cloud Shell are owned by Microsoft, if a customer is using Cloud Shell to connect to or manage other resources, follow the guidelines for those resources.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

### 6.6: Monitor for unapproved software applications within compute resources

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33557).

**Guidance**: Azure Cloud Shell is a free service with no customer owned assets.  The container images and tools are monitored and updated by the Cloud Shell team. 

Azure Cloud Shell allows customers to install their own tools or software in their own image per their organizational needs.

Customers are responsible to monitor software applications running in the environment to make sure they are approved per organization policy.

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 6.7: Remove unapproved Azure resources and software applications

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33558).

**Guidance**: Azure Cloud Shell is a free service with no customer owned assets.  The container images and tools are monitored and updated by the Cloud Shell team. 

Azure Cloud Shell allows customers to install their own tools or software in their own image per their organizational needs.

Customers are responsible to monitor software applications running in the environment to make sure unapproved software are managed per organization policy.

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 6.8: Use only approved applications

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33559).

**Guidance**: Azure Cloud Shell is a free service with no customer owned assets.  The container images and tools are monitored and updated by the Cloud Shell team.  Specific tools may not be removed by the customer.

Azure Cloud Shell allows customers to install their own tools or applications in their own image per their organizational needs.

Customers are responsible to monitor applications running in the environment to make sure they are approved per organization policy.

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 6.9: Use only approved Azure services

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33560).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; Azure Cloud Shell is a free service with no customer owned assets.  The container images and tools are monitored and updated by the Cloud Shell team.  Specific tools may not be removed by the customer.  Each customer container is ephemeral a new container is used for each session.  The container images are monitored and updated by the Cloud Shell team.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

### 6.10: Maintain an inventory of approved software titles

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33561).

**Guidance**: Not applicable; Azure Cloud Shell is a browser-based command-line experience that is used for interactive management of cloud resources.  Each customer container is ephemeral a new container is used for each session.  The container images are monitored and updated by the Cloud Shell team.

Azure Cloud Shell allows customers to install their own tools or software in their own image per their organizational needs.

Customers are responsible to maintain an inventory of approved software running in the environment to make sure they are approved software per organization policy.

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 6.11: Limit users' ability to interact with Azure Resource Manager

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33562).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; Azure Cloud Shell is a browser-based command-line experience that is used for interactive management of cloud resources.  Each customer container is ephemeral a new container is used for each session.  The container images are monitored and updated by the Cloud Shell team.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

### 6.12: Limit users' ability to execute scripts in compute resources

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33563).

**Guidance**: Azure Cloud Shell is a browser-based command-line experience that is used for interactive management of cloud resources.  Actions that are taken within Cloud Shell function the same as actions taken from the same tools or languages run in a local environment.  Actions from individual tools and languages should be restricted, customers cannot restrict access to Cloud Shell or restrict what is available to a user.

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 6.13: Physically or logically segregate high risk applications

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33564).

**Guidance**: Azure Cloud Shell can be isolated in a customer virtual network.

- [Deploy Cloud Shell into an Azure virtual network](private-vnet.md)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

## Secure Configuration

*For more information, see the [Azure Security Benchmark: Secure Configuration](../security/benchmarks/security-control-secure-configuration.md).*

### 7.1: Establish secure configurations for all Azure resources

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33565).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; Azure Cloud Shell doesn't have any resource configurations.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

### 7.2: Establish secure operating system configurations

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33566).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; this recommendation is intended for compute resources.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

### 7.3: Maintain secure Azure resource configurations

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33567).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; Azure Cloud Shell doesn't have any resource configurations.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

### 7.4: Maintain secure operating system configurations

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33568).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; this guideline is intended for compute resources.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

### 7.5: Securely store configuration of Azure resources

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33569).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; Azure Cloud Shell doesn't have any resource configurations.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

### 7.6: Securely store custom operating system images

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33570).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; this guideline is intended for compute resources.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

### 7.7: Deploy configuration management tools for Azure resources

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33571).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; Azure Cloud Shell doesn't have any resource configurations.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

### 7.8: Deploy configuration management tools for operating systems

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33572).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; this guideline is intended for compute resources.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

### 7.9: Implement automated configuration monitoring for Azure resources

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33573).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; Azure Cloud Shell doesn't have any resource configurations.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

### 7.10: Implement automated configuration monitoring for operating systems

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33574).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; this guideline is intended for compute resources.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

### 7.11: Manage Azure secrets securely

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33575).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; Azure Cloud Shell doesn't require customers to manage any keys.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

### 7.12: Manage identities securely and automatically

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33576).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; Azure Cloud Shell doesn't require identity management.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

### 7.13: Eliminate unintended credential exposure

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33577).

**Guidance**: Cloud Shell allows for scripts to be run in, authored in, and uploaded to the Cloud Shell environment.  Moving credentials into Azure Key Vault is our recommendation.

**Responsibility**: Customer

**Azure Security Center monitoring**: None

## Malware Defense

*For more information, see the [Azure Security Benchmark: Malware Defense](../security/benchmarks/security-control-malware-defense.md).*

### 8.1: Use centrally managed antimalware software

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33578).

**Guidance**: Azure Cloud Shell is a browser-based command-line experience that is used for interactive management of cloud resources.  Each customer container is ephemeral a new container is used for each session.  The container images and tools are monitored and updated by the Cloud Shell team.  The customer is able to install their own tools in their own image per their organizational needs and the tools does not require `sudo` permissions during install.

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 8.2: Pre-scan files to be uploaded to non-compute Azure resources

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33579).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; Cloud Shell does not allow for pre-scan files to be uploaded to non-compute resources.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

### 8.3: Ensure antimalware software and signatures are updated

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33580).

**Guidance**: Azure Cloud Shell is a browser-based command-line experience that is used for interactive management of cloud resources.  Each customer container is ephemeral a new container is used for each session.  The container images and tools are monitored and updated by the Cloud Shell team.  The customer is able to install their own tools in their own image per their organizational needs and the tools does not require `sudo` permissions during install.

**Responsibility**: Customer

**Azure Security Center monitoring**: None

## Data Recovery

*For more information, see the [Azure Security Benchmark: Data Recovery](../security/benchmarks/security-control-data-recovery.md).*

### 9.1: Ensure regular automated back ups

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33581).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; Azure Cloud Shell leverages an Azure File Share that is owned and managed by the customer for data storage.  Customers should follow the recommendations for Azure File Share.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

### 9.2: Perform complete system backups and backup any customer-managed keys

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33582).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; Azure Cloud Shell leverages an Azure File Share that is owned and managed by the customer for data storage.  Customers should follow the recommendations for Azure File Share.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

### 9.3: Validate all backups including customer-managed keys

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33583).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; Azure Cloud Shell leverages an Azure File Share that is owned and managed by the customer for data storage.  Customers should follow the recommendations for Azure File Share.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

### 9.4: Ensure protection of backups and customer-managed keys

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33584).

>[!NOTE]
>Because the Responsibility field is set to "Not applicable", this section will be omitted from the published baseline.

**Guidance**: Not applicable; Azure Cloud Shell leverages an Azure File Share that is owned and managed by the customer for data storage.  Customers should follow the recommendations for Azure File Share.

**Responsibility**: Not applicable

**Azure Security Center monitoring**: None

## Incident Response

*For more information, see the [Azure Security Benchmark: Incident Response](../security/benchmarks/security-control-incident-response.md).*

### 10.1: Create an incident response guide

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33585).

**Guidance**: Build out an incident response guide for your organization. Ensure that there are written incident response plans that define all roles of personnel as well as phases of incident handling/management from detection to post-incident review.

- [How to configure Workflow Automations within Azure Security Center](../security-center/security-center-planning-and-operations-guide.md) 

- [Guidance on building your own security incident response process](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Microsoft Security Response Center's Anatomy of an Incident](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Customer may also leverage NIST's Computer Security Incident Handling Guide to aid in the creation of their own incident response plan](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 10.2: Create an incident scoring and prioritization procedure

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33586).

**Guidance**: Security Center assigns a severity to each alert to help you prioritize which alerts should be investigated first. The severity is based on how confident Security Center is in the finding or the analytically used to issue the alert as well as the confidence level that there was malicious intent behind the activity that led to the alert.

Additionally, clearly mark subscriptions (for ex. production, non-prod) and create a naming system to clearly identify and categorize Azure resources.

- [Security alerts in Azure Security Center](../security-center/security-center-alerts-overview.md) 

- [Use tags to organize your Azure resources](../azure-resource-manager/management/tag-resources.md)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 10.3: Test security response procedures

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33591).

**Guidance**: Conduct exercises to test your systems’ incident response capabilities on a regular cadence. Identify weak points and gaps and revise plan as needed.

- [Refer to NIST's publication: Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 10.4: Provide security incident contact details and configure alert notifications for security incidents

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33587).

**Guidance**: Security incident contact information will be used by Microsoft to contact you if the Microsoft Security Response Center (MSRC) discovers that the customer's data has been accessed by an unlawful or unauthorized party. Review incidents after the fact to ensure that issues are resolved.

- [How to set the Azure Security Center Security Contact](../security-center/security-center-provide-security-contact-details.md)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 10.5: Incorporate security alerts into your incident response system

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33588).

**Guidance**: Export your Azure Security Center alerts and recommendations using the Continuous Export feature. Continuous Export allows you to export alerts and recommendations either manually or in an ongoing, continuous fashion. You may use the Azure Security Center data connector to stream the alerts to Azure Sentinel.

- [How to configure continuous export](../security-center/continuous-export.md) 

- [How to stream alerts into Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

### 10.6: Automate the response to security alerts

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33589).

**Guidance**: Use the Workflow Automation feature in Azure Security Center to automatically trigger responses via "Logic Apps" on security alerts and recommendations.

- [How to configure Workflow Automation and Logic Apps](../security-center/workflow-automation.md)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

## Penetration Tests and Red Team Exercises

*For more information, see the [Azure Security Benchmark: Penetration Tests and Red Team Exercises](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### 11.1: Conduct regular penetration testing of your Azure resources and ensure remediation of all critical security findings

>[!TIP]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/33590).

**Guidance**: Follow the Microsoft Cloud Penetration Testing Rules of Engagement to ensure your penetration tests are not in violation of Microsoft policies. Use Microsoft's strategy and execution of Red Teaming and live site penetration testing against Microsoft-managed cloud infrastructure, services, and applications.

- [Penetration Testing Rules of Engagement](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsibility**: Customer

**Azure Security Center monitoring**: None

## Next steps

- See the [Azure Security Benchmark V2 overview](/azure/security/benchmarks/overview)
- Learn more about [Azure security baselines](/azure/security/benchmarks/security-baselines-overview)
