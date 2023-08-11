---
title: Configure Application Gateway with a frontend public IPv6 address using Azure PowerShell (Preview)
description: Learn how to configure Application Gateway with a frontend public IPv6 address using Azure PowerShell.
services: application-gateway
author: greg-lindsay
ms.service: application-gateway
ms.topic: how-to
ms.date: 08/07/2023
ms.author: greglin
ms.custom: mvc, devx-track-azurepowershell
---

# Configure Application Gateway with a frontend public IPv6 address using Azure PowerShell (Preview)

## Overview 

[Azure Application Gateway](overview.md) now supports dual stack (IPv4 and IPv6) frontend connections, providing greater flexibility and connectivity. If you're currently using Application Gateway with IPv4 addresses, you can continue to do so without any changes. However, if you want to take advantage of the benefits of IPv6 addressing, you can configure your gateway to use IPv6 addresses. IPv6 backend connections aren't currently supported.

To support IPv6 connectivity, you must create a dual-stack VNet. This dual-stack VNet has subnets for both IPv4 and IPv6. Azure VNets already [provide dual-stack capability](../virtual-network/ip-services/ipv6-overview.md). 

> [!IMPORTANT]
> Application Gateway IPv6 frontend is currently in PREVIEW.<br>
> See the [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) for legal terms that apply to Azure features that are in beta, preview, or otherwise not yet released into general availability.

## In this guide

You learn how to:
* [Register](#register-to-the-preview) and [unregister](#unregister-from-the-preview) from the preview
* Set up the [dual-stack network](#create-a-dual-stack-subnet)
* Create an application gateway with [IPv6 frontend](#create-application-gateway-frontend-public-ip-addresses)
* Create a virtual machine scale set with the default [backend pool](#create-the-backend-pool-and-settings)

Azure PowerShell is used to create an IPv6 Azure Application Gateway and perform testing to ensure it works correctly. Application gateway can manage and secure web traffic to servers that you maintain. A [virtual machine scale set](../virtual-machine-scale-sets/overview.md) is for backend servers to manage web traffic. The scale set contains two virtual machine instances that are added to the default backend pool of the application gateway. For more information about the components of an application gateway, see [Application gateway components](application-gateway-components.md). 

You can also complete this quickstart using the [Azure portal](ipv6-application-gateway-portal.md)

## Prerequisites

If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.

If you choose to install and use PowerShell locally, this article requires the Azure PowerShell module version 1.0.0 or later. To find the version, run `Get-Module -ListAvailable Az`. If you need to upgrade, see [Install Azure PowerShell module](/powershell/azure/install-azure-powershell). If you're running PowerShell locally, you also need to run `Login-AzAccount` to create a connection with Azure.

## Regions and availability

The IPv6 Application Gateway preview is available to all public cloud regions where Application Gateway v2 SKU is supported.

## Limitations

* Supported for Application Gateway Standard V2 only
* Not supported for upgrading an existing gateway to IPv6
* Not supported for IPv6 private addresses
* Not supported for IPv6 backend
* Not supported for IPv6 Private Link

## Register to the preview

> [!NOTE]
> When you join the preview, all new Application Gateways provision with the ability to define a dual stack frontend connection.  If you wish to opt out from the new functionality and return to the current generally available functionality of Application Gateway, you can [unregister from the preview](#unregister-from-the-preview).

For more information about preview features, see [Set up preview features in Azure subscription](../azure-resource-manager/management/preview-features.md)

Use the following commands to enroll into the public preview for IPv6 Application Gateway:

```azurepowershell
Register-AzProviderFeature -FeatureName "AllowApplicationGatewayIPv6" -ProviderNamespace "Microsoft.Network"
```

To view registration status of the feature, use the Get-AzProviderFeature cmdlet.
```Output
FeatureName                                ProviderName        RegistrationState
-----------                                ------------        -----------------
AllowApplicationGatewayIPv6   Microsoft.Network   Registered
```

## Create a resource group

A resource group is a logical container into which Azure resources are deployed and managed. Create an Azure resource group using [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).  

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

## Create a dual-stack subnet

Configure the subnets named *myBackendSubnet* and *myAGSubnet*  using [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig).


```azurepowershell-interactive

$AppGwSubnetPrefix = @("10.0.0.0/24", "ace:cab:deca::/64")

$appgwSubnet = New-AzVirtualNetworkSubnetConfig `
-Name myAGSubnet ` 
-AddressPrefix $AppGwSubnetPrefix

$backendSubnet = New-AzVirtualNetworkSubnetConfig `
-Name myBackendSubnet `
-AddressPrefix  10.0.1.0/24
```


## Create a dual stack virtual network

```azurepowershell-interactive

$VnetPrefix = @("10.0.0.0/16", "ace:cab:deca::/48")

$vnet = New-AzVirtualNetwork `
-Name myVNet `
-ResourceGroupName myResourceGroupAG `
-Location eastus `   
-AddressPrefix $VnetPrefix `    
-Subnet @($appgwSubnet, $backendSubnet)
```

## Create Application Gateway Frontend public IP addresses

```azurepowershell-interactive

$pipv4 = New-AzPublicIpAddress `   
-Name myAGPublicIPAddress4 `  
-ResourceGroupName myResourceGroupAG `   
-Location eastus `   
-Sku 'Standard' `  
-AllocationMethod 'Static' `    
-IpAddressVersion 'IPv4'  
-Force

$pipv6 = New-AzPublicIpAddress `    
-Name myAGPublicIPAddress6 `   
-ResourceGroupName myResourceGroupAG `
-Location eastus `
-Sku 'Standard' `   
-AllocationMethod 'Static' `   
-IpAddressVersion 'IPv6' `    
-Force
```

### Create the IP configurations, ports and listeners

Associate *myAGSubnet* that you previously created to the application gateway using [New-AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration). Assign *myAGPublicIPAddress* to the application gateway using [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig).

```azurepowershell-interactive
$vnet   = Get-AzVirtualNetwork ` 
-ResourceGroupName myResourceGroupAG `  
-Name myVNet

$subnet = Get-AzVirtualNetworkSubnetConfig `
-VirtualNetwork $vnet `
-Name myAGSubnet

$gipconfig = New-AzApplicationGatewayIPConfiguration `  
-Name myAGIPConfig `  
-Subnet $subnet

$fipconfigv4 = New-AzApplicationGatewayFrontendIPConfig `   
-Name myAGFrontendIPv4Config `  
-PublicIPAddress $pipv4

$fipconfigv6 = New-AzApplicationGatewayFrontendIPConfig `  
-Name myAGFrontendIPv6Config `  
-PublicIPAddress $pipv6

$frontendport = New-AzApplicationGatewayFrontendPort `
-Name myAGFrontendIPv6Config `
-Port 80

```

### Create the backend pool and settings

Create the backend pool named *appGatewayBackendPool* for the application gateway using [New-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool). Configure the settings for the backend address pools using [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting).

```azurepowershell-interactive
$backendPool = New-AzApplicationGatewayBackendAddressPool `   
-Name myAGBackendPool
$poolSettings = New-AzApplicationGatewayBackendHttpSetting `   
-Name myPoolSettings `   
-Port 80 `
-Protocol Http `   
-CookieBasedAffinity Enabled `  
-RequestTimeout 30

```

### Create the default listener and rule

A listener is required to enable the application gateway to route traffic appropriately to the backend pool. In this example, you create a basic listener that listens for traffic at the root URL. 

Create a listener named *mydefaultListener* using [New-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) with the frontend configuration and frontend port that you previously created. A rule is required for the listener to know which backend pool to use for incoming traffic. Create a basic rule named *rule1* using [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule).

```azurepowershell-interactive
$listenerv4 = New-AzApplicationGatewayHttpListener `    
-Name myAGListnerv4 ` 
-Protocol Http `  
-FrontendIPConfiguration $fipconfigv4 `  
-FrontendPort $frontendport

$listenerv6 = New-AzApplicationGatewayHttpListener `
-Name myAGListnerv6 `    
-Protocol Http `    
-FrontendIPConfiguration $fipconfigv6 `
-FrontendPort $frontendport

$frontendRulev4 = New-AzApplicationGatewayRequestRoutingRule `   
-Name ruleIPv4 ` 
-RuleType Basic `   
-Priority 10 `  
-HttpListener $listenerv4 `
-BackendAddressPool $backendPool `   
-BackendHttpSettings $poolSettings 
 
 
$frontendRulev6 = New-AzApplicationGatewayRequestRoutingRule `  
-Name ruleIPv6 ` 
-RuleType Basic `
-Priority 1 `  
-HttpListener $listenerv6 `  
-BackendAddressPool $backendPool `   
-BackendHttpSettings $poolsettings

```

### Create the application gateway

Now that you created the necessary supporting resources, specify parameters for the application gateway using [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku), and then create it using [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway).

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name Standard_v2 `
  -Tier Standard_v2 `
  -Capacity 2

New-AzApplicationGateway `   
-Name myipv6AppGW `  
-ResourceGroupName myResourceGroupAG `  
-Location eastus `  
-BackendAddressPools $backendPool `  
-BackendHttpSettingsCollection $poolsettings `  
-FrontendIpConfigurations @($fipconfigv4, $fipconfigv6) `  
-GatewayIpConfigurations $gipconfig `  
-FrontendPorts $frontendport `  
-HttpListeners @($listenerv4, $listenerv6) `   
-RequestRoutingRules @($frontendRulev4, $frontendRulev6) `
-Sku $sku `   
-Force
```

## Backend servers

Now that you have created the Application Gateway, create the backend virtual machines which host the websites. A backend can be composed of NICs, virtual machine scale sets, public IP address, internal IP address, fully qualified domain names (FQDN), and multi-tenant backends like Azure App Service.

In this example, you create two virtual machines to use as backend servers for the application gateway. You also install IIS on the virtual machines to verify that Azure successfully created the application gateway.

## Create two virtual machines

In this example, you create a virtual machine scale set to provide servers for the backend pool in the application gateway. You assign the scale set to the backend pool when you configure the IP settings.

Get the recently created Application Gateway backend pool configuration with *Get-AzApplicationGatewayBackendAddressPool*.
* Create a network interface with *New-AzNetworkInterface*.
* Create a virtual machine configuration with *New-AzVMConfig*.
* Create the virtual machine with *New-AzVM*.
* When you run the following code sample to create the virtual machines, Azure prompts you    for credentials. Enter a user name and a password:​

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway -ResourceGroupName myResourceGroupAG -Name myAppGateway
$backendPool = Get-AzApplicationGatewayBackendAddressPool -Name myAGBackendPool -ApplicationGateway $appgw
$vnet   = Get-AzVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name myBackendSubnet
$cred = Get-Credential
for ($i=1; $i -le 2; $i++)
{
  $nic = New-AzNetworkInterface `
    -Name myNic$i `
    -ResourceGroupName myResourceGroupAG `
    -Location EastUS `
    -Subnet $subnet `
    -ApplicationGatewayBackendAddressPool $backendpool
  $vm = New-AzVMConfig `
    -VMName myVM$i `
    -VMSize Standard_DS2_v2
  Set-AzVMOperatingSystem `
    -VM $vm `
    -Windows `
    -ComputerName myVM$i `
    -Credential $cred
  Set-AzVMSourceImage `
    -VM $vm `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest
  Add-AzVMNetworkInterface `
    -VM $vm `
    -Id $nic.Id
  Set-AzVMBootDiagnostic `
    -VM $vm `
    -Disable
  New-AzVM -ResourceGroupName myResourceGroupAG -Location EastUS -VM $vm
  Set-AzVMExtension `
    -ResourceGroupName myResourceGroupAG `
    -ExtensionName IIS `
    -VMName myVM$i `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
    -Location EastUS
}
```
## Find the public IP address of Application Gateway

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

## Update DNS with IPv4 and IPv6 addresses

Test the Application: https://{yourdomain.com}

![Application Test](./media/application-gateway-create-gateway-powershell-ipv6/Browser_out.png)

## Clean up resources

When no longer needed, remove the resource group, application gateway, and all related resources using [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## Unregister from the preview

Use the following commands to opt out of the public preview for IPv6 Application Gateway:

```azurepowershell
Unregister-AzProviderFeature -FeatureName "AllowApplicationGatewayIPv6" -ProviderNamespace "Microsoft.Network"
```

To view registration status of the feature, use the Get-AzProviderFeature cmdlet.
```Output
FeatureName                   ProviderName        RegistrationState
-----------                   ------------        -----------------
AllowApplicationGatewayIPv6   Microsoft.Network   Unregistered
```

## Next steps

[Troubleshoot Bad Gateway](application-gateway-troubleshooting-502.md)
