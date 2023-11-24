---
title: Create and configure SFTP Ingestion Agents
description: Learn how to create and configure SFTP Ingestion Agents for Azure Operator Insights 
author: rcdun
ms.author: rdunstan
ms.service: operator-insights
ms.topic: how-to #Required; leave this attribute/value as-is
ms.date: 12/06/2023
---

# Create and configure SFTP Ingestion Agents for Azure Operator Insights

The SFTP agent is a software package that is installed onto a Linux Virtual Machine (VM) owned and managed by you. The agent pulls files from an SFTP server, and forwards them to Azure Operator Insights.  

## Prerequisites

- You must have a configured SFTP server where the files which will be forwarded to Azure Operator Insights are uploaded to.
    - The SFTP server must be accessible from the VM where the agent is installed.
- You must have an Azure Operator Insights Data product deployment.
- You must provide one or more VMs with the following specifications to run the agent:
  - OS - Red Hat Enterprise Linux 8.6 or later
  - Minimum hardware - 4 vCPU,  8-GB RAM, 30-GB disk
  - Network - connectivity to the SFTP server and to Azure
  - Software - systemd and logrotate installed
  - SSH or alternative access to run shell commands
  - (Preferable) Ability to resolve public DNS.  If not, you need to perform additional manual steps to resolve Azure locations. Refer to [Running without public DNS](#running-without-public-dns) for instructions.

The number of VMs needed depends on the scale and redundancy characteristics of your deployment. Each agent instance must run on its own VM. Talk to the Microsoft Support Team to determine your requirements.

## Deploy the agent on your VMs

To deploy the agent on your VMs, follow the procedures outlined in the following sections.

### Authentication

You must have a service principal with a certificate credential that can access the Azure Key Vault created by the Data Product to retrieve storage credentials. Each agent must also have a copy of a valid certificate and private key for the service principal stored on this virtual machine.

#### Create a service principal

> [!IMPORTANT]
> You may need a Microsoft Entra tenant administrator in your organization to perform this set up for you.

1. Create or obtain a Microsoft Entra ID service principal. Follow the instructions detailed in [Create a Microsoft Entra app and service principal in the portal](/entra/identity-platform/howto-create-service-principal-portal).
1. Note the Application (client) ID, and your Microsoft Entra Directory (tenant) ID (these IDs are UUIDs of the form xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx, where each character is a hexadecimal digit).

#### Prepare certificates

It's up to you whether you use the same certificate and key for each VM, or use a unique certificate and key for each.  Using a certificate per VM provides better security and has a smaller impact if a key is leaked or the certificate expires. However, this method adds a higher maintainability and operational complexity.

1. Obtain a certificate. We strongly recommend using trusted certificate(s) from a certificate authority.
1. Add the certificate(s) as credential(s) to your service principal, following [Create a Microsoft Entra app and service principal in the portal](/entra/identity-platform/howto-create-service-principal-portal).
1. We **strongly recommend** additionally storing the certificates in a secure location such as Azure Key vault.  Doing so allows you to configure expiry alerting and gives you time to regenerate new certificates and apply them to your ingestion agents before they expire.  Once a certificate has expired, the agent  is unable to authenticate to Azure and no longer uploads data.  For details of this approach see [Renew your Azure Key Vault certificates Azure portal](../key-vault/certificates/overview-renew-certificate.md).
    - You will need the 'Key Vault Certificates Officer' role on the Azure Key Vault in order to add the certificate to the Key Vault. See [Assign Azure roles using the Azure portal](../role-based-access-control/role-assignments-portal.md) for details of how to assign roles in Azure.

1. Ensure the certificate(s) are available in pkcs12 format, with no passphrase protecting them. On Linux, you can convert a certificate and key from PEM format using openssl:

    `openssl pkcs12 -nodes -export -in $certificate\_pem\_filename -inkey $key\_pem\_filename -out $pkcs12\_filename`

5. Ensure the certificate(s) are base64 encoded. On Linux, you can based64 encode a pkcs12-formatted certificate by using the command:

    `base64 -w 0 $pkcs12\_filename &gt; $base64filename`

#### Permissions

1. Find the Azure Key Vault that holds the storage credentials for the input storage account. This Key vault is in a resource group named *\<data-product-name\>-HostedResources-\<unique-id\>*.
1. Grant your service principal the 'Key Vault Secrets User' role on this Key Vault.  You need Owner level permissions on your Azure subscription.  See [Assign Azure roles using the Azure portal](../role-based-access-control/role-assignments-portal.md) for details of how to assign roles in Azure.
1. Note the name of the Key Vault.

### Prepare the VMs

Repeat these steps for each VM onto which you want to install the agent:

1. Ensure you have an SSH session open to the VM, and that you have `sudo` permissions.
1. Create a directory which will be used for storing secrets for the agent. Note the path of this directory. This is the directory where the secrets for connecting to the SFTP server will be stored, and is referenced in this document as the 'secrets directory'.
1. Verify that the VM has the Port 443/TCP outbound to Azure open
    - This port must be open both in cloud network security groups and in any firewall running on the VM itself (such as firewalld or iptables).
1. Install systemd, logrotate and zip on the VM, if not already present.
1. Obtain the ingestion agent RPM and copy it to the VM.
1. Copy the pkcs12-formatted base64-encoded certificate (created in the [Prepare certificates](#prepare-certificates) step) to an accessible location on the VM (such as /etc/az-sftp-uploader).
1. Ensure the SFTP server's public SSH key is listed on the VM's `known_hosts` file.

> [!TIP]
> A servers SSH key can be added to a VM's `known_hosts` file manually using the `ssh-keygen` command on linux. E.g. `ssh-keygen -192.0.2.0 ~/.ssh/known_hosts`

### Configure the connection between the SFTP server and VM:
Do the following on the SFTP server:

1. Ensure port 22 is outbound to the VM is open.
1. Create a new user, or determine an existing user on the SFTP server that will be used by the SFTP agent to forward files to Azure Operator Insights.
1. Determine the authentication method that will be used by the agent to connect to the SFTP server.  The agent supports two methods:
    - Password authentication
    - SSH key authentication
1. Create a file to store the secret value in the secrets directory on the agent VM, which was created in the [Prepare the VMs](#prepare-the-vms) step.
   - The file must not have a file extension.
   - Choose an appropriate name for the secret file, and note this for later.  This name is referenced in the agent configuration.
   - The secret file must contain only the secret value, with no additional characters or whitespace.
1. If you are using an SSH key that has a passphrase to authenticate, create a separate secret file that contains the passphrase using the same method.

### Running without public DNS

If your agent VMs don't have access to public DNS, then you need to add entries on each agent VM to map the Azure host names to IP addresses.

This process assumes that you're connecting to Azure over ExpressRoute and are using Private Links and/or Service Endpoints. If you're connecting over public IP addressing,  you **cannot** use this workaround and must use public DNS.

Create the following from a virtual network that is peered to your ingestion agents:

- A Service Endpoint to Azure Storage
- A Private Link  or Service Endpoint to the Key Vault created by your Data Product.  The Key Vault is the same one you found in step 5 of [Prepare the certificates](#prepare-certificates).

Steps:

1. Note the IPs of these two connections.
1. Note the domain of your Azure Operator Insights Input Storage Account.  You can find the domain on your Data Product overview page in the Azure portal, in the form of *\<account name\>.blob.core.windows.net*
1. Note the domain of the Key Vault.  The domain appears as *\<vault name\>.vault. azure.net*
1. Add a line to */etc/hosts* on the VM linking the two values in this format, for each of the storage and Key Vault:

    *\<Storage private IP\>*   *\<Storage hostname\>*

    *\<Key Vault private IP\>*  *\<Key Vault hostname\>*


### Acquire the agent RPM

A link to download the SFTP agent RPM is provided as part of the Azure Operator Insights onboarding process. See [How do I get access to Azure Operator Insights?](/articles/operator-insights/overview.md#how-do-i-get-access-to-azure-operator-insights) for details.

### Install agent software

Repeat these steps for each VM onto which you want to install the agent:

1. In an SSH session, change to the directory where the RPM was copied.
1. Install the RPM:  `sudo dnf install \*.rpm`.  Answer 'y' when prompted.  If there are any missing dependencies, the RPM will not be installed.
1. Change to the configuration directory: cd /etc/az-sftp-uploader
1. Make a copy of the default configuration file:  `sudo cp example\_config.yaml config.yaml`
1. Edit the *config.yaml* and fill out the fields.  Many of them are set to default values and do not require input.  The full reference for each parameter is described in [SFTP Ingestion Agents configuration reference](sftp-agent-configuration.md). The following parameters must be set:

    1. **site\_id** should be changed to a unique identifier for your on-premises site – for example, the name of the city or state for this site.  This name becomes searchable metadata in Operator Insights for all data ingested by this agent. Reserved URL characters must be percent-encoded.
    1. For the secret provider with name `data_product_keyvault`, set the following:
        1. **provider.vault\_name** must be the name of the Key Vault for your Data Product  
        1. **provider.auth** must be filled out with:

            1. **tenant\_id** as your Microsoft Entra ID tenant.

            2. **identity\_name** as the application ID of your service principal

            3. **cert\_path** as the path on disk to the location of the base64-encoded certificate and private key for the service principal to authenticate with.
    1. For the secret provider with name `local_file_system`, set the following:

        1. **provider.auth.secrets_directory** the absolute path to the secrets directory on the agent VM, which was created in the [Prepare the VMs](#prepare-the-vms) step.
    

    1. **file_sources** a list of file source details, which specifies the configured SFTP server and configures which files should be uploaded, where they should be uploaded, and how often. Multiple file sources can be specified but only a single source is required. Must be filled out with the following:

        1. **source_id** a unique identifier for the file source. Any URL reserved characters in source_id must be percent-encoded.

        1. **source.sftp** must be filled out with:

            1. **host** the hostname or IP address of the SFTP server.

            1. **base\_path** the path on the SFTP server to copy files from.

            1. **known\_hosts\_file** the path on the VM to the 'known_hosts' file for the SFTP server.  This file must be in SSH format and contain details of any public SSH keys used by the SFTP server.

            1. **user** the name of the user on the SFTP server which the agent should use to connect.

            1. **auth** must be filled according to which authentication method was chosen in the [Configure the connection between the SFTP server and VM](#configure-the-connection-between-the-sftp-server-and-vm) step. The required fields will be different depending on which authentication type is specified:

                - Password:

                    1. **type** set to `password`

                    1. **secret\_name** is the name of the file containing the password in the `secrets_directory` folder.

                - SSH key:

                    1. **type** set to `ssh_key`

                    1. **key\_secret** is the name of the file containing the SSH key in the `secrets_directory` folder.

                    1. **passphrase\_secret\_name** is the name of the file containing the passphrase for the SSH key in the secrets_directory folder. If the SSH key does not have a passphrase, do not include this field.

        1. **sink.container\_name** a string giving the name of the Azure Blob Storage container to use with this file source. This is determined by the type of Data Product that has been deployed. TODO: refer to this doc.

> [!TIP]
> The agent supports additional optional configuration for the following:
> - Specifying a pattern of files in the `base_path` folder which will be uploaded (by default all files in the folder are uploaded)
> - Specifying a pattern of files in the `base_path` folder which should not be uploaded
> - A time and date before which files in the `base_path` folder will not be uploaded
> - How often the SFTP agent uploads files (by default, every 1 hour)
> - A settling time, which is a time period after a file is last modified that the agent will wait before it is uploaded (by default, 1 minute)
> For more information about these configuration options, see [SFTP Ingestion Agents configuration reference](sftp-agent-configuration.md).

6. Start the agent: `sudo systemctl start az-sftp-uploader`

1. Check that the agent is running: `sudo systemctl status az-sftp-uploader`

    1. If you see any status other than "active (running)", look at the logs as described in the [Monitor and troubleshoot SFTP Ingestion Agents for Azure Operator Insights](troubleshoot-sftp-agent.md) article to understand the error.  It's likely that some configuration is incorrect.

    2. Once you resolve the issue,  attempt to start the agent again.

    3. If issues persist, raise a support ticket.

1. Once the agent is running, ensure it will automatically start on a reboot: `sudo systemctl enable az-sftp-uploader.service`

1. Save a copy of the delivered RPM – you'll need it to reinstall or to back out any future upgrades.

## Important considerations

### Security

The VM used for the SFTP agent should be set up following best practice for security. For example:

- Networking - Only allow network traffic on the ports that are required to run the agent and maintain the VM.

- OS version - Keep the OS version up-to-date to avoid known vulnerabilities.

- Access - Limit access to the VM to a minimal set of users, and set up audit logging for their actions. For the SFTP agent, we recommend that the following are restricted:

  - Admin access to the VM (for example, to stop/start/install the SFTP agent software)

  - Access to the directory where the logs are stored *(/var/log/az-sftp-uploader/)*

  - Access to the certificate and private key for the service principal

### Deploying for fault tolerance

The SFTP agent is designed to be highly reliable and resilient to low levels of network disruption. If an unexpected error occurs, the agent restarts and provides service again as soon as it's running.

If a persistent error or extended connectivity problems occur, some files may not be uploaded. However, if the error suggests that the upload may be successful if retried, the agent will attempt the upload the file on the next scheduled run.

## Related content

[Manage SFTP Ingestion Agents for Azure Operator Insights](how-to-manage-sftp-agent.md)

[Monitor and troubleshoot SFTP Ingestion Agents for Azure Operator Insights](troubleshoot-sftp-agent.md)
