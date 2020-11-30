---
title: Deploy a Cloud Service (extended support) - PowerShell
description: Deploy a Cloud Service (extended support) using PowerShell
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: 
---

# Create a Cloud Service (extended)

This article shows how to use the `Az.CloudService` PowerShell module to deploy a Cloud Service (extended support) in Azure that has multiple roles (WebRole and WorkerRole) and RDP extension. 

> [!IMPORTANT]
> Cloud Services (extended support) is currently in public preview.
> This preview version is provided without a service level agreement, and it's not recommended for production workloads. Certain features might not be supported or might have constrained capabilities. 
> For more information, see [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## Register the feature for your subscription
Cloud Services (extended support) is currently in preview. Register the feature for your subscription as follows:

```azurepowershell
Register-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute
```

## Create a Resource Group

Create an Azure resource group using  `New-AzResourceGroup`.

```powershell
New-AzResourceGroup -Name 'ContosoOrg' -Location 'East US'
```

## Create a Key Vault

Create a Key Vault that will be used to store certificates associated to the Cloud Service roles.The Key Vault must use a unique name.

```powershell
$keyVault = New-AzKeyVault -Name 'ContosoKeyVault' -ResourceGroupName 'ContosoOrg' -Location 'East US' -EnabledForDeployment
```

## Give user accounts permissions to manage certificates in Key Vault

Use the `Set-AzKeyVaultAccessPolicy` cmdlet to update the Key Vault access policy and grant certificate permissions to the user accounts.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoOrg' -UserPrincipalName 'user@domain.com' -PermissionsToCertificates create,get,list,delete
```

Optionally, you can set access policy using the ObjectId. This can be obtained by using the `Get-AzADUser` cmdlet.

```PowerShell
Set-AzKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoOrg' -ObjectId 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' -PermissionsToCertificates create,get,list,delete
```

## Add a certificate to the Key Vault

> [!NOTE]
> The certificate thumbprint needs to be added in Cloud Service configuration (cscfg) file for deployment on Cloud Service roles.

```powershell
$policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName "Self" -ValidityInMonths 6 -ReuseKeyOnRenewal
Add-AzKeyVaultCertificate -VaultName 'ContosoKeyVault' -Name 'ContosoCert' -CertificatePolicy $policy
```

## Create a storage account and container

Create a storage account and container that will be used to store the Cloud Service package (cspkg).

> [!NOTE]
> You must use a unique name for storage account name.

```powershell
$storageAccount = New-AzStorageAccount -ResourceGroupName 'ContosoOrg' -Name 'contosostorageaccount' -Location 'East US' -SkuName 'Standard_RAGRS' -Kind 'StorageV2'
$container = New-AzStorageContainer -Name 'contosocontainer' -Context $storageAccount.Context -Permission blob
```

## Upload the Cloud Service package (cspkg) to the storage account

Upload the Cloud Service package (cspkg) to the storage account. Later the SAS URL of the package will be generated which will be used for creating Cloud Service.

```powershell
$cspkgFilePath = '<Path to cspkg file>'
$blob = Set-AzStorageBlobContent -File $cspkgFilePath -Container 'contosocontainer' -Blob 'ContosoCS.cspkg' -Context $storageAccount.Context
```

## Create a virtual network

Create a virtual network and virtual network subnet as per configuration defined in Cloud Service configuration (cscfg). For this example, we have defined single virtual network subnet for both Cloud Service roles (WebRole and WorkerRole).

> [!NOTE]
> Virtual Network name and Virtual Network Subnet name should be same as defined in Cloud Service configuration (cscfg) file.

```powershell
$subnet = New-AzVirtualNetworkSubnetConfig -Name 'ContosoSubnet' -AddressPrefix '10.0.0.0/24'
New-AzVirtualNetwork -Name 'ContosoVNet' -ResourceGroupName 'ContosoOrg' -Location 'East US' -AddressPrefix '10.0.0.0/24' -Subnet $subnet
```

## Create a public IP address

Create a public IP address to be associated with the load balancer of Cloud Service.

```powershell
$publicIp = New-AzPublicIpAddress -Name 'ContosoPublicIP' -ResourceGroupName 'ContosoOrg' -Location 'East US' -AllocationMethod 'Dynamic' -IpAddressVersion 'IPv4' -DomainNameLabel 'contosocloudservice' -Sku 'Basic'
```

## Create an OS profile

Create an OS Profile in-memory object. OS Profile specifies the certificates which are associated to Cloud Service roles. Over here you will use the certificates that were created in previous steps.

```powershell
$certificate = Get-AzKeyVaultCertificate -VaultName 'ContosoKeyVault' -Name 'ContosoCert'
$secretGroup = New-AzCloudServiceVaultSecretGroupObject -Id $keyVault.ResourceId -CertificateUrl $certificate.SecretId
```

## Create a role profile

Create a Role Profile in-memory object. Role profile defines role's sku specific properties such as name, capacity and tier. For this example, we have defined two roles: WebRole and WorkerRole.

> [!NOTE]
> Role profile information should match the role configuration defined in configuration (cscfg) file and service definition (csdef) file.

```powershell
$role1 = New-AzCloudServiceRoleProfilePropertiesObject -Name 'WebRole' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2
$role2 = New-AzCloudServiceRoleProfilePropertiesObject -Name 'WorkerRole' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2
$roles = @($role1, $role2)
```

## Create a network profile

Create a Network Profile in-memory object. Network profile specifies the load balancer related configuration including the public IP address.

```powershell
$feIpConfig = New-AzCloudServiceLoadBalancerFrontendIPConfigurationObject -Name 'ContosoFE' -PublicIPAddressId $publicIp.Id
$loadBalancerConfig = New-AzCloudServiceLoadBalancerConfigurationObject -Name 'ContosoLB' -FrontendIPConfiguration $feIpConfig
```

## Create an extension profile

Create an Extension Profile in-memory object that you want to add to your Cloud Service. For this example we will add RDP extension and Geneva monitoring extension.

```powershell
# RDP extension
$credential = Get-Credential
$expiration = (Get-Date).AddYears(1)
$rdpExtension = New-AzCloudServiceRemoteDesktopExtensionObject -Name 'RDPExtension' -Credential $credential -Expiration $expiration -TypeHandlerVersion '1.2.1'

# Geneva extension
$genevaExtension = New-AzCloudServiceExtensionObject -Name 'GenevaExtension' -Publisher 'Microsoft.Azure.Geneva' -Type 'GenevaMonitoringPaaS' -TypeHandlerVersion '2.14.0.2'
$extensions = @($rdpExtension, $genevaExtension)
```

## (Optional) Add tags to your Cloud Service

Define Tags as PowerShell hash table which you want to add to your Cloud Service.

```powershell
$tag=@{"Owner" = "Contoso"; "Client" = "PowerShell"}
```

## Read configuration (cscfg) file and generate package (cspkg) SAS URL

Read the Cloud Service configuration (cscfg) file and generate the SAS URL of Cloud Service package (cspkg) that was uploaded to storage account in previous steps.

```powershell
# Read Configuration File
$cscfgFilePath = '<Path to cscfg file>'
$cscfgContent = Get-Content $cscfgFilePath | Out-String

# Generate a SAS token for Cloud Service package
$token = New-AzStorageBlobSASToken -Container 'contosocontainer' -Blob 'ContosoCS.cspkg' -Permission rwd -Context $storageAccount.Context
$cspkgUrl = $blob.ICloudBlob.Uri.AbsoluteUri + $token
```

## Create the Cloud Service (extended support) deployment

```powershell
$cloudService = New-AzCloudService `
-Name 'ContosoCS' `
-ResourceGroupName 'ContosoOrg' `
-Location 'East US' `
-PackageUrl $cspkgUrl `
-Configuration $cscfgContent `
-UpgradeMode 'Auto' `
-RoleProfileRole $roles `
-NetworkProfileLoadBalancerConfiguration $loadBalancerConfig `
-ExtensionProfileExtension $extensions `
-OSProfileSecret $secretGroup `
-Tag $tag
```

## Get Remote desktop file

Get the RDP file using Get-AzCloudServiceRoleInstanceRemoteDesktopFile. Log in into the role instance using the credentials specified while creating RDP extension.<br>

In this example RDP extension is installed on all role instances, thus you can get RDP file for any of the role instances. In below command we are downloading RDP file for WebRole instance 0.

```powershell
Get-AzCloudServiceRoleInstanceRemoteDesktopFile -ResourceGroupName "ContosoOrg" -CloudServiceName "ContosoCS" -RoleInstanceName "WebRole_IN_0" -OutFile "C:\temp\WebRole_IN_0.rdp"
```

## Next steps
For more information, see [Cloud Services (extended support) Reference Documentation](https://docs.microsoft.com/rest/api/compute/cloudservices/) 
