---
title: 'Azure PowerShell script sample - Configure P2S VPN with RADIUS authentication'
titleSuffix: Azure VPN Gateway
description: PowerShell sample to configure point-to-site VPN with RADIUS username/password authentication
services: vpn-gateway
author: cherylmc

ms.service: vpn-gateway
ms.topic: sample
ms.date: 02/10/2021
ms.author: alzam

---

# Create a VPN gateway with P2S RADIUS authentication - PowerShell script sample

This script sample creates a route-based VPN gateway and adds point-to-site configuration using RADIUS username/password authentication.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

```azurepowershell-interactive
# Declare variables
  $VNetName  = "VNet1"
  $FESubName = "FrontEnd"
  $VNetPrefix1 = "10.1.0.0/16"
  $FESubPrefix = "10.1.0.0/24"
  $GWSubPrefix = "10.1.255.0/27"
  $VPNClientAddressPool = "172.16.201.0/24"
  $RG = "TestRG1"
  $Location = "East US"
  $GWName = "VNet1GW"
  $GWIPName = "VNet1GWIP"
  $RSAddress = "10.51.0.15"

# Create a resource group
New-AzResourceGroup -Name $RG -Location $Location
# Create a virtual network
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName $RG `
  -Location $Location `
  -Name $VNetName `
  -AddressPrefix $VNetPrefix1
# Create a subnet configuration
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name $FESubName `
  -AddressPrefix $FESubPrefix `
  -VirtualNetwork $virtualNetwork
# Set the subnet configuration for the virtual network
$virtualNetwork | Set-AzVirtualNetwork
# Add a gateway subnet
$vnet = Get-AzVirtualNetwork -ResourceGroupName $RG -Name $VNetName
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix $GWSubPrefix -VirtualNetwork $vnet
# Set the subnet configuration for the virtual network
$vnet | Set-AzVirtualNetwork
# Request a public IP address
$gwpip= New-AzPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location `
 -AllocationMethod Dynamic
# Create the gateway IP address configuration
$vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
# Create the VPN gateway
New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
 -Location $Location -IpConfigurations $gwipconfig -GatewayType Vpn `
 -VpnType RouteBased -GatewaySku VpnGw1 -VpnClientProtocol "IKEv2"
# Create a secure string for the RADIUS secret
$Secure_Secret=Read-Host -AsSecureString -Prompt "RadiusSecret"

# Add the VPN client address pool and the RADIUS server information
$Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
 -VpnClientAddressPool $VPNClientAddressPool -VpnClientProtocol @( "SSTP", "IkeV2" ) `
 -RadiusServerAddress $RSAddress -RadiusServerSecret $Secure_Secret
```

## Clean up resources

When you no longer need the resources you created, use the [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) command to delete the resource group. This will delete the resource group and all of the resources it contains.

```azurepowershell-interactive
Remove-AzResourceGroup -Name TestRG1
```

## Script explanation

This script uses the following commands to create the deployment. Each item in the table links to command specific documentation.

| Command | Notes |
|---|---|
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Adds a subnet configuration. This configuration is used with the virtual network creation process. |
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Gets virtual network details. |
| [Get-AzVirtualNetworkGateway](/powershell/module/az.network/get-azvirtualnetworkgateway) | Gets a virtual network gateway details. |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Gets the virtual network subnet configuration details. |
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Creates a resource group in which all resources are stored. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Creates a subnet configuration. This configuration is used with the virtual network creation process. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Creates a virtual network. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Creates a public IP address. |
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Creates a new gateway ip configuration. |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Creates a VPN gateway. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Removes a resource group and all resources contained within. |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Sets the subnet configuration for the virtual network. |
| [Set-AzVirtualNetworkGateway](/powershell/module/az.network/set-azvirtualnetworkgateway) | Sets the configuration for the VPN gateway. |

## Next steps

For more information on the Azure PowerShell module, see [Azure PowerShell documentation](/powershell/azure/).
