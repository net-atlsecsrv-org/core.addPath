---
title: Azure Firewall DNS settings
description: You can configure Azure Firewall with DNS server and DNS proxy settings.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 11/06/2020
ms.author: victorh
---

# Azure Firewall DNS settings

You can configure a custom DNS server and enable DNS proxy for Azure Firewall. Configure these settings when you deploy the firewall, or configure them later from the **DNS settings** page.

## DNS servers

A DNS server maintains and resolves domain names to IP addresses. By default, Azure Firewall uses Azure DNS for name resolution. The **DNS server** setting lets you configure your own DNS servers for Azure Firewall name resolution. You can configure a single server or multiple servers.

> [!NOTE]
> For instances of Azure Firewall that are managed by using Azure Firewall Manager, the DNS settings are configured in the associated Azure Firewall policy.

### Configure custom DNS servers - Azure portal

1. Under Azure Firewall **Settings**, select **DNS Settings**.
2. Under **DNS servers**, you can type or add existing DNS servers that have been previously specified in your virtual network.
3. Select **Save**.

The firewall now directs DNS traffic to the specified DNS servers for name resolution.

:::image type="content" source="media/dns-settings/dns-servers.png" alt-text="Screenshot showing settings for D N S servers.":::

### Configure custom DNS servers - Azure CLI

The following example updates Azure Firewall with custom DNS servers by using the Azure CLI.

```azurecli-interactive
az network firewall update \
    --name fwName \ 
    --resource-group fwRG \
    --dns-servers 10.1.0.4 10.1.0.5
```

> [!IMPORTANT]
> The command `az network firewall` requires the Azure CLI extension `azure-firewall` to be installed. You can install it by using the command `az extension add --name azure-firewall`. 

### Configure custom DNS servers - Azure PowerShell

The following example updates Azure Firewall with custom DNS servers by using Azure PowerShell.

```azurepowershell
$dnsServers = @("10.1.0.4", "10.1.0.5")
$azFw = Get-AzFirewall -Name "fwName" -ResourceGroupName "fwRG"
$azFw.DNSServer = $dnsServers

$azFw | Set-AzFirewall
```

## DNS proxy

You can configure Azure Firewall to act as a DNS proxy. A DNS proxy is an intermediary for DNS requests from client virtual machines to a DNS server. If you configure a custom DNS server, then enable DNS proxy to avoid a DNS resolution mismatch, and enable FQDN (fully qualified domain name) filtering in the network rules.

If you don't enable DNS proxy, then DNS requests from the client might travel to a DNS server at a different time or return a different response compared to that of the firewall. DNS proxy puts Azure Firewall in the path of the client requests to avoid inconsistency.

When Azure Firewall is a DNS proxy, two caching function types are possible:

- **Positive cache**: DNS resolution is successful. The firewall uses the TTL (time to live) of the packet or object. 

- **Negative cache**: DNS resolution results in no response or no resolution. The firewall caches this information for one hour.

The DNS proxy stores all resolved IP addresses from FQDNs in network rules. As a best practice, use  FQDNs that resolve to one IP address.  

### DNS proxy configuration

DNS proxy configuration requires three steps:
1. Enable the DNS proxy in Azure Firewall DNS settings.
2. Optionally, configure your custom DNS server or use the provided default.
3. Configure the Azure Firewall private IP address as a custom DNS address in your virtual network DNS server settings. This setting ensures DNS traffic is directed to Azure Firewall.

#### Configure DNS proxy - Azure portal

To configure DNS proxy, you must configure your virtual network DNS servers setting to use the firewall private IP address. Then enable the DNS proxy in the Azure Firewall **DNS settings**.

##### Configure virtual network DNS servers 

1. Select the virtual network where the DNS traffic will be routed through the Azure Firewall instance.
2. Under **Settings**, select **DNS servers**.
3. Under **DNS servers**, select **Custom**.
4. Enter the firewall's private IP address.
5. Select **Save**.
6. Restart the VMs that are connected to the virtual network so they're assigned the new DNS server settings. VMs continue to use their current DNS settings until they're restarted.

##### Enable DNS proxy

1. Select your Azure Firewall instance.
2. Under **Settings**, select **DNS settings**.
3. By default, **DNS Proxy** is disabled. When this setting is enabled, the firewall listens on port 53 and forwards DNS requests to the configured DNS servers.
4. Review the **DNS servers** configuration to make sure that the settings are appropriate for your environment.
5. Select **Save**.

:::image type="content" source="media/dns-settings/dns-proxy.png" alt-text="Screenshot showing settings for the D N S proxy.":::

#### Configure DNS proxy - Azure CLI

You can use the Azure CLI to configure DNS proxy settings in Azure Firewall. You can also use it to update virtual networks to use Azure Firewall as the DNS server.

##### Configure virtual network DNS servers

The following example configures the virtual network to use Azure Firewall as the DNS server.
 
```azurecli-interactive
az network vnet update \
    --name VNetName \ 
    --resource-group VNetRG \
    --dns-servers <firewall-private-IP>
```

##### Enable DNS proxy

The following example enables the DNS proxy feature in Azure Firewall.

```azurecli-interactive
az network firewall update \
    --name fwName \ 
    --resource-group fwRG \
    --enable-dns-proxy true
```

#### Configure DNS proxy - Azure PowerShell

You can use Azure PowerShell to configure DNS proxy settings in Azure Firewall. You can also use it to update virtual networks to use Azure Firewall as the DNS server.

##### Configure virtual network DNS servers

The following example configures the virtual network to use Azure Firewall as a DNS server.

```azurepowershell
$dnsServers = @("<firewall-private-IP>")
$VNet = Get-AzVirtualNetwork -Name "VNetName" -ResourceGroupName "VNetRG"
$VNet.DhcpOptions.DnsServers = $dnsServers

$VNet | Set-AzVirtualNetwork
```

##### Enable DNS proxy

The following example enables the DNS proxy feature in Azure Firewall.

```azurepowershell
$azFw = Get-AzFirewall -Name "fwName" -ResourceGroupName "fwRG"
$azFw.DNSEnableProxy = $true

$azFw | Set-AzFirewall
```

## Next steps

[FQDN filtering in network rules](fqdn-filtering-network-rules.md)
