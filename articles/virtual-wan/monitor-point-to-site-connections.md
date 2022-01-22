---
title: 'How to monitor P2S connections'
titleSuffix: Azure Virtual WAN
description: Learn how to set up an Azure workbook for P2S monitoring
services: virtual-wan
author: siddomala
ms.service: virtual-wan
ms.topic: how-to
ms.date: 01/18/2022
ms.author: siddomala
---

# How to monitor point-to-site connections for Virtual WAN

This section documents how to create an Azure Workbook that shows relevant data of VPN clients connecting to Azure Virtual WAN using a User VPN (P2S) configuration.

## Before you begin

To complete the steps in this article, you need to have a virtual WAN,  a hub, and a User VPN Gateway. To create these resources, follow the steps in this article:

* [Create virtual WAN, a hub, and a gateway](virtual-wan-point-to-site-portal.md#P2S)


## Workbook solution architecture

- AzureDiagnostics: Enable P2S Debugging through Azure Monitor debug settings, GatewayDiagnosticLog, IKEDiagnosticLog, P2SDiagnosticLog, AllMetrics. Notice that some logs are very noisy and thereby rather costly in regards to Log Analytics cost, specially IKEDiagnostics
- Azure Metrics : Can be used directly from within the workbook, but the metrics available are limited
- Get-AzP2sVpnGatewayDetailedConnectionHealth which is a separate PowerShell command to get active sessions and this command only supports storing data in a storage account based on a SAS Key.

When you work with Azure Virtual WAN and look at metrics, it is most often done from within the context of an Azure workbook. You could also choose to extract data and use PowerBI which is another great tool to visualize data. In this case we choose to make a solution based on Azure Workbook and use what is already available and enrich it with more details, especially about active connections. 

The figure below shows the involved components in the suggested solution. 

![workbookarchitecture.png](/.attachments/workbookarchitecture.png)

The VPN service is running in the Azure vWAN P2S VPN gateway and has associated metrics and debug settings that we can read and act on directly from within an Azure workbook. To get the extra information that the PowerShell command can provide, we choose to execute this command in an Azure FunctionApp and from there store the output it the required Azure storage account.

The output stored in the storage account is fetched from within the workbook by using a special function called "externaldata".

Below is a description of the various components.

## Create Azure storage account

1. In the portal, in the **Search resources** bar, type **Storage accounts**.

1. Select **Storage accounts** from the results. On the Storage accounts page, select **+ Create** to open the **Create a storage account** page.
The Azure storage account has the following configuration settings

1. On the **Create WAN** page, on the **Basics** tab, fill in the fields. Modify the example values to apply to your environment.

   :::image type="content" source="./media/monitor-point-to-site-connections/storage-account-basics.png" alt-text="Screenshot shows the basics section of creating a storage account.":::

   * **Subscription**: Select the subscription that you want to use.
   * **Resource group**: Create new or use existing.
   * **Storage account name**: Type the name you want to call your storage account.
   * **Region**: Select a region for your storage account
   * **Performance**: Standard or Premium. **Standard** is adequate for our monitoring purposes. 
   * **Redundancy**: Choose between Locally-redundant storage, Geo-redundant storage, Zone-redundant storage, and Geo-zone-redundant storage.

1. After you finish filling out the fields, at the bottom of the page, select **Next: Advanced>**.
    :::image type="content" source="./media/monitor-point-to-site-connections/storage-account-advanced.png" alt-text="Screenshot shows advanced section of creating a storage account.":::

    * **Require secure transfer for REST API operations**: Choose **Enabled**
    * **Enable blob public access**: Choose **Disabled**
    * **Enable storage account key access**: Choose **Enabled**
    * **Default to Azure Active Directory authorization in the Azure portal**: Choose **Enabled**
    * **Minimum TLS version**: Choose **Version 1.2** 

1. Click **Review + create** at the bottom to run validation

1. Once validation passes, click **Create** to create the storage account.

## Create container

1. Once the deployment is complete, go to the resource
1. On the left-hand panel, click **Containers** under **Data storage**
:::image type="content" source="./media/monitor-point-to-site-connections/container-create.png" alt-text="Screenshot shows the initial container page.":::
1. Click **+ Container** to create a new container
1. Type a **Name** for your container and click **Create**

## Create and upload blob to container
1. On your machine, open a text editor application, such as **Notepad**
   :::image type="content" source="./media/monitor-point-to-site-connections/notepad.png" alt-text="Screenshot shows how to open notepad.":::
1. Leave the text file empty and click **File -> Save As**
1. Save the empty text file with a name of your choice followed by the **.json** extension
    :::image type="content" source="./media/monitor-point-to-site-connections/empty-json.png" alt-text="Screenshot shows how to save json file.":::
1. Go back to the **Containers** section in portal
   :::image type="content" source="./media/monitor-point-to-site-connections/container-after.png" alt-text="Screenshot shows the container section after creating new container.":::
1. Click on the second row, which corrersponds to the container you created (not $logs)
1. If you see this red warning message saying ""**You do not have permission...**"", then click **Switch to Access key** as your authentication method. This is located right below the red warning box.
1. Click **Upload**
    :::image type="content" source="./media/monitor-point-to-site-connections/specific-container.png" alt-text="Screenshot shows the specific container that was created by user"::: 
1. Select the file corresponding to your empty JSON file on your machine and click **Upload**
1. After the file gets uploaded, click on the JSON file and navigate to the **Generate SAS** tab
:::image type="content" source="./media/monitor-point-to-site-connections/generate-sas.png" alt-text="Screenshot shows the Generate SAS field for blob"::: 
1. Under **Signing method**, choose **Account key**
1. Under **Permissions**, give the key the following permissions: **Read, Add, Create, and Write**
1. Choose an **Expiry** date and time for the key
1. Click **Generate SAS token and URL**
1. Copy down the **Blob SAS token** and **Blob SAS URL** to a secure location

## Create Azure FunctionApp

The FunctionApp has the following configuration settings

FunctionApp name: <functionappname>
Identity: system assigned: Status: On (we enable the managed/built-in service identity to be able to access Azure without having to use a username/password or service principal based on secrets). Note down the object id of this identity (to be used in permission assignment
)
Configuration:
Application settings
- resource group: <resourcegroupname>
- sasuri: @Microsoft.KeyVault(SecretUri=https://<keyvaultname>.vault.azure.net/secrets/sasuri/<version>) (update accordingly after keyvault is created.
- storageaccountname: <storageaccountname>
- storagecontainer: <storagecontainername>
- subscription: <subscriptionid>
- tenantname: <tenantname>
- vpngw: <vpngw> This name is something like <guid>-eastus-ps2-gw . You can get this from the vWAN HUB User VPN settings.

Function:
- FunctionName: <FunctionName>, choose for example a trigger type function to be executed each 5 minutes
- Code: see below 

------------------------------------------------------------------------------------------------------------------------

param($Timer)
$currentUTCtime = (Get-Date).ToUniversalTime()
if ($Timer.IsPastDue) {
    Write-Host "PowerShell timer is running late!"
}
Write-Host "PowerShell timer trigger function ran! TIME: $currentUTCtime"
$tenantname = $env:appsetting_tenantname
$subscription = $env:appsetting_subscription
$resourceGroup = $env:appsetting_resourcegroup
$storageAccountName = $env:appsetting_storageaccountname
$storageContainer = $env:appsetting_storagecontainer
$vpnstatsfile= $env:appsetting_vpnstatsfile
$vpngw = $env:appsetting_vpngw
$sasuri = $env:appsetting_sasuri
connect-azaccount -tenant $tenantname -identity -subscription $subscription
Get-AzP2sVpnGatewayDetailedConnectionHealth -name $vpngw -ResourceGroupName $resourceGroup -OutputBlobSasUrl $sasuri

----------------------------------------------------------------------------------------------------------------------

For the get-AzP2sVpnGatewayDetailedConnectionHealth command to succeed, you need to have the right permissions to the information. This can be done by creating a custom Azure role. Go the Azure Portal to the resource group used and choose Access Control (IAM) and assign the following role/permissions
Custom Role Name: <custom role name like MicrosoftNetworkP2SGWReader>. We don't want to use built-in role like network-contributor as we only want read-access. 

|**Type**|**Permissions**|**Description**|
|-------|----------------------------------------|-----------------------------------------------|
|Read  |Get P2SVpnGateway| Gets a P2SVpnGateway.|
|Other  | Gets a P2S Vpn Connection health for P2SVpnGateway  |Gets a P2S Vpn Connection health for P2SVpnGateway|
|Other  | Gets a P2S Vpn Connection health detailed for P2SVpnGateway  |  Gets a P2S Vpn Connection health detailed for P2SVpnGateway|
|Read   | Gets P2S Vpn Gateway Log Definitions  | Gets the events for P2S Vpn Gateway|
|Read   | Get P2S Vpn Gateway Diagnostic Settings   | Gets the P2S Vpn Gateway Diagnostic Settings|
|Read   | Read P2S Vpn Gateway metric definitions   | Gets the available metrics for P2S Vpn Gateway |
|Read   | Get Virtual Wan P2SVpnServerConfiguration  |  Gets a virtual Wan P2SVpnServerConfiguration |

Now assign the managed identity (objectid) this role.



## Create Azure Key Vault

1. In the portal, in the **Search resources** bar, type **Key vaults**.

1. Select **+ Create** from the results to open the **Create a key vault** page.

1. On the **Basics** tab, fill in the fields. Modify the example values to apply to your environment.

   :::image type="content" source="./media/monitor-point-to-site-connections/key-vault-basics.png" alt-text="Screenshot shows the basics section of creating a key vault.":::

   * **Subscription**: Select the subscription that you want to use.
   * **Resource group**: Create new or use existing.
   * **Storage account name**: Type the name you want to call your key vault.
   * **Region**: Select a region for your storage account
   * **Pricing tier**: Standard or Premium. **Standard** is adequate for our monitoring purposes. 
1. Click **Next: Access policy >**
1. Under **Permission model**, choose **Vault access policy**
1. Leave the options under **Resource access** as disabled
1. Under **Access policies**, click **+ Create**.
   :::image type="content" source="./media/monitor-point-to-site-connections/create-access-policy-screen1.png" alt-text="Screenshot shows first screen in creating access policy.":::
1. Click **Next** to go to the **Principal** tab. TODO: FIND FUNCTION APP MANAGED IDENTITY
1. Click **Next** twice to get to the fourth tab: **Review + create** and click **Create** at the bottom.
1. You should now see the newly created access policy under the **Access policies** section. Modifying the default values under the **Networking** tab is optional, so click **Review + create** in the bottom left-hand corner. 


KeyVault settings:
Secrets: 
- sasuri: 
- secret value: <SASURI> (storage account SAS key)
Enabled:Yes

New Access Policy: 
Permission model: Vault access policy
- Keys and secret management
- Key Permissions:  Get, List
- Secret Permissions: Get, List
- Select Principal: <FunctionApp managed identity>

Generate SAS token and URL: (SASURI, to be saved in KeyVault and used directly from within the workbook)


You can choose to create two SAS keys, one for read/write access from the Azure FunctionApp and one with read access used from the workbook, but for now, we use the same SAS key and restrict access to the workbook.


##Azure workbook

The Azure workbook is now ready to be created with a mix of built-in functionality and the addition that uses the solution above to give insights to active sessions with more details.

Create a workbook from Azure Monitor or from a Log Analytics workspace.
Give it a name: <workbook name>

###P2S VPN Gateway Metrics

![workbook1.png](/.attachments/workbook1.png)

###Express Route Circuit Metrics

![workbook2.png](/.attachments/workbook2.png)

###Express Route Gateway Metrics

![workbook3.png](/.attachments/workbook3.png)

###P2S User successful connections with IP

![workbook4.png](/.attachments/workbook4.png)

Log Query:

AzureDiagnostics
| where Category == "P2SDiagnosticLog" and Message has "Connection successful" and Message has "Username={UserName}"
| project splitted=split(Message, "Username=")
| mv-expand col1=splitted[0], col2=splitted[1], col3=splitted[2]
| project user=split(col2, " ")
| mv-expand username=user[0]
| project ['user']

###EAP Authentication succeded

![workbook5.png](/.attachments/workbook5.png)

Log Query:

AzureDiagnostics 
| where Category == "P2SDiagnosticLog" and Message has "EAP authentication succeeded" and Message has "Username={UserName}"
| project Message, MessageFields = split(Message, " "), Userinfo = split(Message, "Username=")
| mv-expand MessageId=MessageFields[2],user=split(Userinfo[1]," ")
| project MessageId, Message, Userinfo[1]


###P2S VPN User Info

![workbook6.png](/.attachments/workbook6.png)

Log Query:

AzureDiagnostics
| where Category == "P2SDiagnosticLog" and Message has "Username={UserName}"
| project Message, MessageFields = split(Message, " "), Userinfo = split(Message, "Username=")
| mv-expand MessageId=MessageFields[2], Username=Userinfo[1]
| project MessageId, Message, Username;

###P2S VPN Successful connections per user

![workbook7.png](/.attachments/workbook7.png)

Log Query:

AzureDiagnostics 
| where Category == "P2SDiagnosticLog" and Message has "Connection successful"
| project splitted=split(Message, "Username=")
| mv-expand col1=splitted[0], col2=splitted[1], col3=splitted[2]
| project user=split(col2, " ")
| mv-expand username=user[0]
| project-away ['user']
| summarize count() by tostring(username)
| sort by count_ desc

###P2S VPN Connections

![workbook8.png](/.attachments/workbook8.png)

Log Query:

AzureDiagnostics | where Category == "P2SDiagnosticLog"
| project TimeGenerated, OperationName, Message, Resource, ResourceGroup
| sort by TimeGenerated asc

###Successful P2S VPN Connections

![workbook9.png](/.attachments/workbook9.png)

Log Query:

AzureDiagnostics
| where Category == "P2SDiagnosticLog" and Message has "Connection successful"
| project TimeGenerated, Resource ,Message


###Failed P2S VPN Connections

![workbook10.png](/.attachments/workbook10.png)

Log Query:

AzureDiagnostics
| where Category == "P2SDiagnosticLog" and Message has "Connection failed"
| project TimeGenerated, Resource ,Message

###VPN Connection count by P2SDiagnosticLog

![workbook11.png](/.attachments/workbook11.png)

Log Query: 

AzureDiagnostics 
| where Category == "P2SDiagnosticLog" and Message has "Connection successful" and Message has "Username={UserName}"| count

###IKE Diagnosticsdetails

![workbook12.png](/.attachments/workbook12.png)

Log Query:

AzureDiagnostics
| where Category == "IKEDiagnosticLog"
| extend Message1=Message
| parse Message with * "Remote " RemoteIP ":" * "500: Local " LocalIP ":" * "500: " Message2
| extend Event = iif(Message has "SESSION_ID",Message2,Message1)
| project TimeGenerated, RemoteIP, LocalIP, Event, Level
| sort by TimeGenerated asc

###IKEDiagnosticLog

![workbook13.png](/.attachments/workbook13.png)

###P2S VPN Statistics

![workbook14.png](/.attachments/workbook14.png)

Log Query:

AzureDiagnostics 
| where Category == "P2SDiagnosticLog" and Message has "Statistics"
| project Message, MessageFields = split(Message, " ")
| mv-expand MessageId=MessageFields[2]
| project MessageId, Message;

###P2S VPN Active Sessions Details

![workbook14.png](/.attachments/workbook14.png)

let P2Svpnconnections = (externaldata (resource:string, UserNameVpnConnectionHealths: dynamic) [
    @"SASURI"
] with(format="multijson"));

P2Svpnconnections
| mv-expand UserNameVpnConnectionHealths
| extend Username = parse_json(UserNameVpnConnectionHealths).UserName
| extend VpnConnectionHealths = parse_json(parse_json(UserNameVpnConnectionHealths).VpnConnectionHealths)
| mv-expand VpnConnectionHealths
| extend VpnConnectionId = parse_json(VpnConnectionHealths).VpnConnectionId, VpnConnectionDuration = parse_json(VpnConnectionHealths).VpnConnectionDuration, VpnConnectionTime = parse_json(VpnConnectionHealths).VpnConnectionTime, PublicIpAddress = parse_json(VpnConnectionHealths).PublicIpAddress, PrivateIpAddress = parse_json(VpnConnectionHealths).PrivateIpAddress, MaxBandwidth = parse_json(VpnConnectionHealths).MaxBandwidth, EgressPacketsTransferred = parse_json(VpnConnectionHealths).EgressPacketsTransferred, EgressBytesTransferred = parse_json(VpnConnectionHealths).EgressBytesTransferred, IngressPacketsTransferred = parse_json(VpnConnectionHealths).IngressPacketsTransferred, IngressBytesTransferred = parse_json(VpnConnectionHealths).IngressBytesTransferred, MaxPacketsPerSecond = parse_json(VpnConnectionHealths).MaxPacketsPerSecond
| extend PubIp = tostring(split(PublicIpAddress, ":").[0])
| project Username, VpnConnectionId, VpnConnectionDuration, VpnConnectionTime, PubIp, PublicIpAddress, PrivateIpAddress, MaxBandwidth, EgressPacketsTransferred, EgressBytesTransferred, IngressPacketsTransferred, IngressBytesTransferred, MaxPacketsPerSecond;
