---
title: Update a cluster to use certificate common name 
description: Learn how to switch a Service Fabric cluster from using certificate thumbprints to using certificate common name.

ms.topic: conceptual
ms.date: 09/06/2019
---
# Change cluster from certificate thumbprint to common name
No two certificates can have the same thumbprint, which makes cluster certificate rollover or management difficult. Multiple certificates, however, can have the same common name or subject.  Switching a deployed cluster from using certificate thumbprints to using certificate common names makes certificate management much simpler. This article describes how to update a running Service Fabric cluster to use the certificate common name instead of the certificate thumbprint.

>[!NOTE]
> If you have two thumbprint's declared in your template, you need to perform two deployments.  The first deployment is done before following the steps in this article.  The first deployment sets your **thumbprint** property in the template to the certificate being used and removes the **thumbprintSecondary** property.  For the second deployment, follow the steps in this article.
 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## Get a certificate
First, get a certificate from a [certificate authority (CA)](https://wikipedia.org/wiki/Certificate_authority).  The common name of the certificate should be for the custom domain you own, and bought from a domain registrar. For example, "azureservicefabricbestpractices.com"; those whom are not Microsoft employees can not provision certs for MS domains, so you can not use the DNS names of your LB or Traffic Manager as common names for your certificate, and you will need to provision a [Azure DNS Zone](../dns/dns-delegate-domain-azure-dns.md) if your custom domain to be resolvable in Azure. You will also want to declare your custom domain you own as your cluster's "managementEndpoint" if you want portal to reflect the custom domain alias for your cluster.

For testing purposes, you could get a CA signed certificate from a free or open certificate authority.

> [!NOTE]
> Self-signed certificates, including those generated when deploying a Service Fabric cluster in the Azure portal, are not supported. 

## Upload the certificate and install it in the scale set
In Azure, a Service Fabric cluster is deployed on a virtual machine scale set.  Upload the certificate to a key vault and then install it on the virtual machine scale set that the cluster is running on.

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "mykeyvault"
$certFilename = "C:\users\sfuser\myclustercert.pfx"
$certname = "myclustercert"
$Password  = "P@ssw0rd!123"
$VmssResourceGroupName     = "myclustergroup"
$VmssName                  = "prnninnxj"

# Create new Resource Group 
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName `
    -Location $region -EnabledForDeployment 
$resourceId = $newKeyVault.ResourceId 

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzKeyVaultCertificate -VaultName $vaultName -Name $certName `
    -FilePath $certFilename -Password $PasswordSec

$CertificateThumbprint = $KVSecret.Thumbprint
$CertificateURL = $KVSecret.SecretId
$SourceVault = $resourceId
$CommName    = $KVSecret.Certificate.SubjectName.Name

Write-Host "CertificateThumbprint    :"  $CertificateThumbprint
Write-Host "CertificateURL           :"  $CertificateURL
Write-Host "SourceVault              :"  $SourceVault
Write-Host "Common Name              :"  $CommName    

Set-StrictMode -Version 3
$ErrorActionPreference = "Stop"

$certConfig = New-AzVmssVaultCertificateConfig -CertificateUrl $CertificateURL -CertificateStore "My"

# Get current VM scale set 
$vmss = Get-AzVmss -ResourceGroupName $VmssResourceGroupName -VMScaleSetName $VmssName

# Add new secret to the VM scale set.
$vmss = Add-AzVmssSecret -VirtualMachineScaleSet $vmss -SourceVaultId $SourceVault `
    -VaultCertificate $certConfig

# Update the VM scale set 
Update-AzVmss -ResourceGroupName $VmssResourceGroupName -Verbose `
    -Name $VmssName -VirtualMachineScaleSet $vmss 
```

>[!NOTE]
> Scale set secrets do not support the same resource ID for two separate secrets, as each secret is a versioned, unique resource. 

## Download and update the template from the portal
The certificate has been installed on the underlying scale set, but you also need to update the Service Fabric cluster to use that certificate and its common name.  Now, download the template for your cluster deployment.  Sign in to the [Azure portal](https://portal.azure.com) and navigate to the resource group hosting the cluster.  In **Settings**, select **Deployments**.  Select the most recent deployment and click **View template**.

![View templates][image1]

Download the template and parameters JSON files to your local computer.

First, open the parameters file in a text editor and add the following parameter value:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
```

Next, open the template file in a text editor and make three updates to support certificate common name.

1. In the **parameters** section, add a *certificateCommonName* parameter:
    ```json
    "certificateCommonName": {
        "type": "string",
        "metadata": {
            "description": "Certificate Commonname"
        }
    },
    ```

    Also consider removing the *certificateThumbprint*, it may no longer be referenced in the Resource Manager template.

2. In the **Microsoft.Compute/virtualMachineScaleSets** resource, update the virtual machine extension to use the common name in certificate settings instead of the thumbprint.  In **virtualMachineProfile**->**extensionProfile**->**extensions**->**properties**->**settings**->**certificate**, add `"commonNames": ["[parameters('certificateCommonName')]"],` and remove `"thumbprint": "[parameters('certificateThumbprint')]",`.
    ```json
        "virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
                    "properties": {
                        "type": "ServiceFabricNode",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                            "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                            "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
                        },
                        "publisher": "Microsoft.Azure.ServiceFabric",
                        "settings": {
                            "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                            "nodeTypeRef": "[variables('vmNodeType0Name')]",
                            "dataPath": "D:\\SvcFab",
                            "durabilityLevel": "Bronze",
                            "enableParallelJobs": true,
                            "nicPrefixOverride": "[variables('subnet0Prefix')]",
                            "certificate": {
                                "commonNames": [
                                    "[parameters('certificateCommonName')]"
                                ],
                                "x509StoreName": "[parameters('certificateStoreValue')]"
                            }
                        },
                        "typeHandlerVersion": "1.0"
                    }
                },
    ```

3.  In the **Microsoft.ServiceFabric/clusters** resource, update the API version to "2018-02-01".  Also add a **certificateCommonNames** setting with a **commonNames** property and remove the **certificate** setting (with the thumbprint property) as in the following example:
    ```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
        ],
        "properties": {
            "addonFeatures": [
                "DnsService",
                "RepairManager"
            ],
            "certificateCommonNames": {
                "commonNames": [
                    {
                        "certificateCommonName": "[parameters('certificateCommonName')]",
                        "certificateIssuerThumbprint": ""
                    }
                ],
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
        ...
    ```

For additional information see [Deploy a Service Fabric cluster that uses certificate common name instead of thumbprint.](./service-fabric-create-cluster-using-cert-cn.md)

## Deploy the updated template
Redeploy the updated template after making the changes.

```powershell
$groupname = "sfclustertutorialgroup"

New-AzResourceGroupDeployment -ResourceGroupName $groupname -Verbose `
    -TemplateParameterFile "C:\temp\cluster\parameters.json" -TemplateFile "C:\temp\cluster\template.json" 
```

## Next steps
* Learn about [cluster security](service-fabric-cluster-security.md).
* Learn how to [rollover a cluster certificate](service-fabric-cluster-rollover-cert-cn.md)
* [Update and Manage cluster certificates](service-fabric-cluster-security-update-certs-azure.md)

[image1]: ./media/service-fabric-cluster-change-cert-thumbprint-to-cn/PortalViewTemplates.png
