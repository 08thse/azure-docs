---
title: Deploy Azure Site Recovery replication appliance - Preview
description: This article describes support and requirements when deploying the replication appliance for VMware disaster recovery to Azure with Azure Site Recovery - Preview
ms.service: site-recovery
ms.topic: article
ms.date: 06/29/2021
---

# Deploy Azure Site Recovery replication appliance - Preview

>[!NOTE]
> The information in this article applies to Azure Site Recovery - Preview. For information about configuration server requirements in Classic releases, [see this article](vmware-azure-configuration-server-requirements.md).

You deploy an on-premises replication appliance when you use [Azure Site Recovery](site-recovery-overview.md) for disaster recovery of VMware VMs and physical servers to Azure.

- The replication appliance coordinates communications between on-premises VMware and Azure. It also manages data replication.
- [Learn more](vmware-azure-architecture-preview.md) about the Azure Site Recovery replication appliance components and processes.

<< as discussed earlier, we need to mention about the capacity for each replication appliance>>

## Hardware requirements

**Component** | **Requirement**
--- | ---
CPU cores | 8
RAM | 32 GB
Number of disks | 3, including the OS disk - 80 GB, data disk 1 - 620 GB, data disk 2 - 620 GB


## Software requirements

<this section needs detailed review>

**Component** | **Requirement**
--- | ---
Operating system | Windows Server 2016
Operating system locale | English (en-*)
Windows Server roles | Don't enable these roles: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V
Group policies | Don't enable these group policies: <br> - Prevent access to the command prompt. <br> - Prevent access to registry editing tools. <br> - Trust logic for file attachments. <br> - Turn on Script Execution. <br> [Learn more](/previous-versions/windows/it-pro/windows-7/gg176671(v=ws.10))
IIS | - No pre-existing default website <br> - No pre-existing website/application listening on port 443 <br>- Enable  [anonymous authentication](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731244(v=ws.10)) <br> - Enable [FastCGI](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753077(v=ws.10)) setting
FIPS (Federal Information Processing Standards) | Do not enable FIPS mode|

## Network requirements
<needs detailed review>

**Component** | **Requirement**
--- | ---
IP address type | Static
Ports | 443 (Control channel orchestration)<br>9443 (Data transport)
NIC type | VMXNET3 (if the configuration server is a VMware VM)
**Internet access**  (the server needs access to the following URLs, directly or via proxy):|
\*.backup.windowsazure.com | Used for replicated data transfer and coordination
\*.blob.core.windows.net | Used to access storage account that stores replicated data. You can provide the specific URL of your cache storage account.
\*.hypervrecoverymanager.windowsazure.com | Used for replication management operations and coordination
https:\//login.microsoftonline.com | Used for replication management operations and coordination
time.nist.gov | Used to check time synchronization between system and global time
time.windows.com | Used to check time synchronization between system and global time
https:\//management.azure.com </li><li> https:\//secure.aadcdn.microsoftonline-p.com </li><li> https:\//login.live.com </li><li> https:\//graph.windows.net </li><li> https:\//login.windows.net </li><li> *.services.visualstudio.com (Optional) </li><li> https:\//www.live.com </li><li> https:\//www.microsoft.com </li></ul> | OVF setup needs access to these additional URLs. They're used for access control and identity management by Azure Active Directory.
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi  | To complete MySQL download. </br> In a few regions, the download might be redirected to the CDN URL. Ensure that the CDN URL is also approved, if necessary.

> [!NOTE]
> If you have [private links connectivity](hybrid-how-to-enable-replication-private-endpoints.md) to Site Recovery vault, you do not need any additional internet access for the Configuration Server. An exception to this is while setting up the CS machine using OVA template, you will need access to following URLs over and above private link access - https://management.azure.com, https://www.live.com and https://www.microsoft.com. If you do not wish to allow access to these URLs, please set up the CS using Unified Installer.

## Required software
<needs detailed review>

**Component** | **Requirement**
--- | ---
VMware vSphere PowerCLI | Not required for versions 9.14 and higher
MYSQL | MySQL should be installed. You can install manually, or Site Recovery can install it. For more information, see [configure settings](vmware-azure-deploy-configuration-server.md#configure-settings).



## Prepare Azure account

To create and register the Azure Site Recovery replication appliance, you need an Azure account with:

- Contributor or Owner permissions on the Azure subscription.
- Permissions to register Azure Active Directory (AAD) apps.
- Owner or Contributor plus User Access Administrator permissions on the Azure subscription to create a Key Vault, used during agentless VMware migration.

If you just created a free Azure account, you're the owner of your subscription. If you're not the subscription owner, work with the owner for the required permissions.

Use the following steps to assign the required permissions assigned:

1. In the Azure portal, search for **Subscriptions**, and under **Services**, select **Subscriptions** search box to search for the Azure subscription.

2. In the Subscriptions page, select the subscription in which you created the Recovery Services vault.

3. In the subscription, select **Access control** (IAM) > **Check access**. In **Check access**, search for the relevant user account.

4. In **Add a role assignment**, Select **Add,** select the Contributor or Owner role, and select the account. Then Select **Save**.

  To register the appliance, your Azure account needs permissions to register AAD apps.

  Follow these steps to assign required permissions:

  - In Azure portal, navigate to **Azure Active Directory** > **Users** > **User Settings**. In **User settings**, verify that Azure AD users can register applications (set to Yes by default).

  - In case the **App registrations** settings is set to *No*, request the tenant/global admin to assign the required permission. Alternately, the tenant/global admin can assign the Application Developer role to an account to allow the registration of AAD App. Learn more.

## Prepare infrastructure - set up Azure Site Recovery Replication appliance

You need to set up an Azure Site Recovery replication appliance on the on-premises environment to channel mobility agent communications. For detailed information on the operations performed by the appliance [see this section](vmware-azure-architecture-preview.md)

Go to **Recovery Services Vault** > **Getting Started**. In VMware machines to Azure, select
 **Prepare Infrastructure** and proceed with the sections detailed below:

   [![Site recovery](./media/deploy-vmware-azure-replication-appliance-preview/site-recovery-inline.png)](./media/deploy-vmware-azure-replication-appliance-preview/site-recovery-expanded.png#lightbox)

   To set up a new appliance, you can use an OVF template (recommended) or PowerShell.

### Configuration requirements for Site Recovery replication appliance

  - OS - Windows 2016
  - CPU – 8 cores
  - RAM – 32 GB
  - Disks – 3
    - OS disk – 80 GB
    - Data disk 1 – 620 GB
    - Data disk 2 – 620 GB

### Create Site Recovery appliance

You can create the Site Recovery appliance by using the OVF template or through PowerShell.

1. Download the OVF template to set up an appliance on your on-premises environment.

  This is the recommended approach as Azure Site Recovery ensures all prerequisite configurations are handled by the template.

  The OVF template spins up a machine with the required specifications.

2. After the deployment is complete, power on the VM to accept Microsoft Evaluation license.
3. In the next screen, provide password for the administrator user.
4.  Select **Finalize,** the system reboots and you can login with the administrator user account.

In case of any organizational restrictions, you can manually set up the Site Recovery replication appliance through PowerShell. Do the following:

1. Download the installers from [here](https://aka.ms/V2ARcmApplianceCreationPowershellZip) and place this folder on the Azure Site Recovery replication appliance.
2. After successfully copying the zip folder, unzip and extract the components of the folder.
3. Go to the path in which the folder is extracted to and execute the following PowerShell script: `DRInstaller.ps1` as administrator.

  Once you create the appliance, Microsoft Azure appliance configuration manager is launched automatically. Prerequisites such as internet connectivity, Time sync, system configurations and group policies (listed below) are validated.

  - CheckRegistryAccessPolicy - Prevents access to registry editing tools.
     - Key: HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System
     - DisableRegistryTools value should not be equal 0.

  - CheckCommandPromptPolicy - Prevents access to the command prompt.

     - Key: HKLM\SOFTWARE\Policies\Microsoft\Windows\System

     - DisableCMD value should not be equal 0

  - CheckTrustLogicAttachmentsPolicy - Trust logic for file attachments.

     - Key: HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Attachments
     - UseTrustedHandlers value should not be equal 3

  - CheckPowershellExecutionPolicy - Turn on Script Execution

     - PowerShell execution policy shouldn't be AllSigned or Restricted
     - Ensure the group policy 'Turn on Script Execution Attachment Manager' is not set to Disabled or 'Allow only signed scripts'

4. Configure the proxy settings by toggling on the **use proxy to connect to internet** option.

 All Azure Site Recovery services will use these settings to connect to the internet. Only HTTP proxy is supported.

5. Ensure the following URLs are whitelisted and reachable from the Azure Site Recovery replication appliance for continuous connectivity:

  | **URL**                  | **Details**                             |
| ------------------------- | -------------------------------------------|
| portal.azure.com          | Navigate to the Azure portal.              |
| `*.windows.net `<br>`*.msftauth.net`<br>`*.msauth.net`<br>`*.microsoft.com`<br>`*.live.com `<br>`*.office.com ` | To sign-in to your Azure subscription.  |
|`*.microsoftonline.com `|Create Azure Active Directory (AD) apps for the appliance to communicate with Azure Site Recovery. |
|management.azure.com |Create Azure AD apps for the appliance to communicate with the Azure Site Recovery service. |
|`*.services.visualstudio.com `|Upload app logs used for internal monitoring. |
|`*.vault.azure.net `|Manage secrets in the Azure Key Vault. Note: Ensure machines to replicate have access to this. |
|aka.ms |Allow access to aka links. Used for Azure Site Recovery appliance updates. |
|download.microsoft.com/download |Allow downloads from Microsoft download. |
|`*.servicebus.windows.net `|Communication between the appliance and the Azure Site Recovery service. |
|`*.discoverysrv.windowsazure.com `|Connect to Azure Site Recovery discovery service URL. |
|`*.hypervrecoverymanager.windowsazure.com `|Connect to Azure Site Recovery micro-service URLs  |
|`*.blob.core.windows.net `|Upload data to Azure storage which is used to create target disks |
|`*.backup.windowsazure.com `|Protection service URL – a microservice used by Azure Site Recovery for processing and creating replicated disks in Azure |


6. After saving the details, proceed to choose the appliance connectivity. Leave the default selection as FDQN for this preview.

7. After saving connectivity details, Select **Continue** to proceed to registration with Microsoft Azure.

 **Prerequisites for successful registration of appliance with Azure**:

 You need an account with:

 - Permissions to register Azure Active Directory (AAD) apps

    - To enable, navigate to Azure portal -> Azure Active Directory -> Users -> User Settings.
    - In User settings, verify that Azure AD users can register applications (set to Yes by default)
    - In case the 'App registrations' setting is set to 'No', request the tenant/global admin to assign the required permission. Alternately, the tenant/global admin can assign the

    Application Developer role to an account to allow the registration of AAD App. [Learn more](/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).

 - Owner or Contributor plus User Access Administrator permissions on the Azure subscription to create a Key Vault. The key vault is used to store certifications generated

6. Ensure the prerequisites are met, proceed with registration.

  - **Friendly name of appliance** : Provide a friendly name with which you want to track this appliance in the Azure portal under recovery services vault infrastructure.

  -  **Azure Site Recovery replication appliance key** : Copy the key from portal by navigating to recovery services vault -> Getting started -> VMware to Azure Prepare Infrastructure.

  - After pasting the key, select **Login.** You will be redirected to a new authentication tab.

    By default, an authentication code will be generated as highlighted below, in the authentication manager page. Use this code in the authentication tab.

    Enter your Microsoft Azure credentials to complete registration.

    After successful registration, you can close the tab and move to configuration manager to continue the set up.

    [![Register recovery service vault](./media/deploy-vmware-azure-replication-appliance-preview/register-recovery-services-vault-inline.png)](./media/deploy-vmware-azure-replication-appliance-preview/register-recovery-services-vault-expanded.png#lightbox)

    ![Enter code](./media/deploy-vmware-azure-replication-appliance-preview/enter-code.png)

    > [!NOTE]
    > An authentication code expires within 5 minutes of generation. In case of inactivity for more than this duration, you will be prompted to login again to Azure.


9. Select **Login** to reconnect with the session. For authentication code, refer to the section *Summary* or *Register with Azure Recovery Services vault* in the configuration manger.

10. After successful login, subscription, resource group and recovery services vault details are displayed as shown below. You can logout in case you want to change the vault. Else, Select 'continue' to proceed.

11. After successful registration, proceed to configure vCenter details.

12. Select **Add vCenter Server** to add vCenter information. Enter the server name or IP address of the vCenter and port information. Post that, provide username, password and friendly name and is used to fetch details of [virtual machine managed through the vCenter](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery). The user account details will be encrypted and stored locally in the machine.

13. After successfully saving the vCenter information, select **Add virtual machine credentials** to provide user details of the VMs discovered through the vCenter. For Linux OS, ensure to provide root credentials and for Windows OS, a user account with admin privileges should be added, these credentials will be used to push mobility agent on to the source VM during enable replication operation. The credentials can be chosen per VM in the Azure portal during enable replication workflow.

14. After successfully adding the details, select continue to install all Azure Site Recovery replication appliance components and register with Azure services. This activity can take up to 30 min. Ensure to not close the browser while configuration is in progress.

## View Azure Site Recovery replication appliance in Azure portal

After successful configuration of Azure Site Recovery replication appliance, navigate to Azure portal, Recovery Services Vault.

If you select **Prepare infrastructure** under **Getting started**, you can see that an Azure Site Recovery replication appliance is already registered with this vault. Now you are all set! Start protecting your source machines through this replication appliance.

Upon Selecting  *1 appliance(s)*, you will be re-directed to Azure Site Recovery replication appliance view to see the list of appliances registered to this vault.

[![Register recovery service vault](./media/deploy-vmware-azure-replication-appliance-preview/register-recovery-services-vault-inline.png)](./media/deploy-vmware-azure-replication-appliance-preview/register-recovery-services-vault-expanded.png#lightbox)


## Deploy replication appliance

To set up a new appliance, you can follow either of the following methods:

### Create appliance using pre-configured virtual machine image

1. Download the OVF template to set up an appliance on your on-premises environment.

  We recommend this approach as all prerequisite configurations are handled by the template.  

  The OVF template spins up a machine with the required specifications.

2. After the deployment is complete, power on the VM to accept Microsoft Evaluation license and click **Next**.
3. In the next screen, provide the password for the administrator user.
4. Select **Finalize**.

  The system restarts and you can login with the administrator user account.

### Set up appliance on a virtual machine or physical server through PowerShell
 In case of any organizational restrictions, you can manually set up the Azure Site Recovery replication appliance through PowerShell.  

1. Ensure a machine with the required [hardware](#hardware-requirements) and [software](#software-requirements) configuration is created.
2. [Download](https://aka.ms/V2ARcmApplianceCreationPowershellZip) the zip that contains the required installers and place this folder on the Azure Site Recovery replication appliance.
3.	After successfully copying the zip folder, unzip and extract the components of the folder.
4. Go to the path where the folder is extracted to and execute the following PowerShell script as an administrator:
```powershell
   DRInstaller.ps1
```

## Sizing and capacity
You can create and use multiple replication appliances in a vault.

- You can perform discovery of all the machines in a vCenter server, using any of the replication appliances in the vault.

- You can switch a protected machine, between different appliances in the same vault, given the selected appliance is healthy.

## Next steps
Set up disaster recovery of [VMware VMs](vmware-azure-tutorial.md) to Azure.
