---
title: Batch security and compliance best practices
description: Learn best practices and useful tips for enhancing security with your Azure Batch solutions.
ms.date: 12/18/2020
ms.topic: conceptual
---

# Batch security and compliance best practices

This article provides guidance and best practices for enhancing security when using Azure Batch.

By default, Azure Batch accounts have a public endpoint and are publicly accessible. When an Azure Batch pool is created, the pool is provisioned in a specified subnet of an Azure virtual network. Virtual machines in the Batch pool are accessed through public IP addresses that are created by Batch. Compute nodes in a pool can communicate with each other when needed, such as to run multi-instance tasks, but nodes in a pool can't communicate with virtual machines outside of the pool.

:::image type="content" source="media/security-best-practices/typical-environment.png" alt-text="Diagram showing a typical Batch environment.":::

Many features are available to help you create a more secure Azure Batch deployment. You can restrict access to nodes and reduce the discoverability of the nodes from the internet by [provisioning the pool without public IP addresses](batch-pool-no-public-ip-address.md). The compute nodes can securely communicate with other virtual machines or with an on-premises network by [provisioning the pool in a subnet of an Azure virtual network](batch-virtual-network.md). And you can enable [private access from virtual networks](private-connectivity.md) from a service powered by Azure Private Link.

:::image type="content" source="media/security-best-practices/secure-environment.png" alt-text="Diagram showing a more secure Batch environment.":::

## General security-related best practices

### Pool configuration

Many security features are only available for pools configured using [Virtual Machine Configuration](nodes-and-pools.md#configurations), and not for pools with Cloud Services Configuration. We recommend using Virtual Machine Configuration pools, which utilize [virtual machine scale sets](../virtual-machine-scale-sets/overview.md), whenever possible.

### Batch account authentication

Batch account access supports two methods of authentication: Shared Key and [Azure Active Directory (Azure AD)](batch-aad-auth.md).

We strongly recommend using Azure AD for Batch account authentication. Some Batch capabilities require this method of authentication, including many of the security-related features discussed here.

### Batch account pool allocation mode

When creating a Batch account, you can choose between two [pool allocation modes](accounts.md#batch-accounts):

- **Batch service**: The default option, where the underlying Cloud Service or virtual machine scale set resources used to allocate and manage pool nodes are created in internal subscriptions, and aren't directly visible in the Azure portal. Only the Batch pools and nodes are visible. 
- **User subscription**: The underlying Cloud Service or virtual machine scale set resources are created in the same subscription as the Batch account. These resources are therefore visible in the subscription, in addition to the corresponding Batch resources.

With user subscription mode, Batch VMs and other resources are created directly in your subscription when a pool is created. User subscription mode is required if you want to create Batch pools using Azure Reserved VM Instances, use Azure Policy on virtual machine scale set resources, and/or  manage the core quota on the subscription (shared across all Batch accounts in the subscription). To create a Batch account in user subscription mode, you must also register your subscription with Azure Batch, and associate the account with an Azure Key Vault.

## Restrict network endpoint access

### Batch network endpoints

Be aware that by default, endpoints with public IP addresses are used to communicate with Batch accounts, Batch pools, and pool nodes.

### Batch account API

 When a Batch account is created, a public endpoint is created that is used to invoke most operations for the account using a [REST API](/rest/api/batchservice/). The account endpoint has a base URL using the  format `https://{account-name}.{region-id}.batch.azure.com`. Access to the Batch account is secured, with communication to the account endpoint being encrypted using HTTPS, and each request authenticated using either shared key or Azure Active Directory (Azure AD) authentication.

### Azure Resource Manager

In addition to operations specific to a Batch account, [management operations](/rest/api/batchmanagement/) apply to single and multiple Batch accounts. These management operations are accessed via Azure Resource Manager.

Batch management operations via Azure Resource Manager are encrypted using HTTPS, and each request is authenticated using Azure AD authentication.

### Batch pool nodes

The Batch service communicates with a Batch node agent that runs on each node in the pool. For example, the service instructs the node agent to run a task, stop a task, or get the files for a task. Communication with the node agent is enabled by one or more load balancers, the number of which depends on the number of nodes in a pool. The load balancer forwards the communication to the desired node, with each node being addressed by a unique port number. By default, load balancers have public IP addresses associated with them. You can also remotely access pool nodes via RDP or SSH (this access is enabled by default, with communication via load balancers).

### Restricting access to Batch endpoints 

Several capabilities are available to limit access to the various Batch endpoints, especially when the solution uses a virtual network. 

#### Use private endpoints

[Azure Private Link](../private-link/private-link-overview.md) enables access to Azure PaaS Services and Azure hosted customer-owned/partner services over a private endpoint in your virtual network. You can use Private Link to restrict access to a Batch account from within the virtual network or from any peered virtual network. Resources mapped to Private Link are also accessible on-premises over private peering through VPN or Azure ExpressRoute.

To use private endpoints, a Batch account needs to be configured appropriately when created; public network access configuration must be disabled. Once created, private endpoints can be created and associated with the Batch account. For more information, see [Use private endpoints with Azure Batch accounts](private-connectivity.md).

#### Create pools in virtual networks

Compute nodes in a Batch pool can communicate with each other, such as to run multi-instance tasks, without requiring a virtual network (VNET). However, by default, nodes in a pool can't communicate with virtual machines that are outside of the pool on a virtual network and have private IP addresses, such as license servers or file servers.

To allow compute nodes to communicate securely with other virtual machines, or with an on-premises network, you can configure a pool to be in a subnet of an Azure VNET.

When the pools have public IP endpoints, the subnet must allow inbound communication from the Batch service to be able to schedule tasks and perform other operations on the compute nodes, and outbound communication to communicate with Azure Storage or other resources as needed by your workload. For pools in the Virtual Machine configuration, Batch adds network security groups (NSGs) at the network interface level attached to compute nodes. These NSGs have rules to enable:

- Inbound TCP traffic from Batch service IP addresses
- Inbound TCP traffic for remote access
- Outbound traffic on any port to the virtual network (may be amended per subnet-level NSG rules)
- Outbound traffic on any port to the internet (may be amended per subnet-level NSG rules)

You don't have to specify NSGs at the virtual network subnet level, because Batch configures its own NSGs. If you have an NSG associated with the subnet where Batch compute nodes are deployed, or if you would like to apply custom NSG rules to override the defaults applied, you must configure this NSG with at least the inbound and outbound security rules in order to allow Batch service communication to the pool nodes and pool node communication to Azure Storage.

For more  information, see [Create an Azure Batch pool in a virtual network](batch-virtual-network.md).

#### Create pools with static public IP addresses

By default, the public IP addresses associated with pools are dynamic; they are created when a pool is created and IP addresses can be added or removed when a pool is resized. When the task applications running on pool nodes need to access external services, access to those services may need to be restricted to specific IPs.  In this case, having dynamic IP addresses will not be manageable.

You can create static public IP address resources in the same subscription as the Batch account before pool creation. You can then specify these addresses when creating your pool.

For more information, see [Create an Azure Batch pool with specified public IP addresses](create-pool-public-ip.md).

#### Create pools without public IP addresses

By default, all the compute nodes in an Azure Batch virtual machine configuration pool are assigned one or more public IP addresses. These endpoints are used by the Batch service to schedule tasks and for communication with compute nodes, including outbound access to the internet.

To restrict access to these nodes and reduce the discoverability of these nodes from the internet, you can provision the pool without public IP addresses.

For more information, see [Create a pool without public IP addresses](batch-pool-no-public-ip-address.md).

#### Limit remote access to pool nodes

By default, Batch allows a node user with network connectivity to connect externally to a compute node in a Batch pool by using RDP or SSH.

To limit remote access to nodes, use one of the following methods:

- Configure the [PoolEndpointConfiguration](/rest/api/batchservice/pool/add#poolendpointconfiguration) to deny access. The appropriate network security group (NSG) will be associated with the pool.
- Create your pool [without public IP addresses](batch-pool-no-public-ip-address.md). By default, these pools can't be accessed outside of the VNet.
- Associate an NSG with the VNet to deny access to the RDP or SSH ports.
- Don't create any users on the node. Without any node users, remote access won't be possible.

## Encrypt data

### Encrypt data in transit

All communication to the Batch account endpoint (or via Azure Resource Manager) must use HTTPS. You must use `https://` in the Batch account URLs specified in APIs when connecting to the Batch service.

Clients communicating with the Batch service should be configured to use Transport Layer Security (TLS) 1.2.

### Encrypt Batch data at rest

Some of the information specified in Batch APIs, such as account certificates, job and task metadata, and task command lines, is automatically encrypted when stored by the Batch service. By default, this data is encrypted using Azure Batch platform-managed keys unique to each Batch account.

You can also encrypt this data using [customer-managed keys](batch-customer-managed-key.md). [Azure Key Vault](../key-vault/general/overview.md) is used to generate and store the key, with the key identifier registered with your Batch account.

### Encrypt compute node disks

Batch compute nodes have two disks by default: an OS disk and the local temporary SSD. [Files and directories](files-and-directories.md) managed by Batch are located on the temporary SSD, which is the default location for files such as task output files. Batch task applications can use the default location on the SSD or the OS disk.

For extra security, encrypt these disks using one of these Azure disk encryption capabilities:

- [Managed disk encryption at rest with platform-managed keys](../virtual-machines/windows/disk-encryption.md#platform-managed-keys)
- [Encryption at host using a platform-managed key](../virtual-machines/windows/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)
- [Azure Disk Encryption](disk-encryption.md)

## Securely access services from compute nodes

Batch nodes can [securely access credentials and secrets](credential-access-key-vault.md) stored in [Azure Key Vault](../key-vault/general/overview.md), which can be used by task applications to access other services. A certificate is used to grant the pool nodes access to Key Vault.

## Governance and compliance

### Compliance

To help customers meet their own compliance obligations across regulated industries and markets worldwide, Azure maintains a [large portfolio of compliance offerings](/overview/trusted-cloud/compliance/). 

These  offerings are based on various types of assurances, including formal certifications, attestations, validations, authorizations, and assessments produced by independent third-party auditing firms, as well as contractual amendments, self-assessments, and customer guidance documents produced by Microsoft. Review the [comprehensive overview of compliance offerings](https://aka.ms/AzureCompliance) to determine which ones may be relevant to your Batch solutions.

### Azure Policy

[Azure Policy](../governance/policy/overview.md) helps to enforce organizational standards and to assess compliance at scale. Common use cases for Azure Policy include implementing governance for resource consistency, regulatory compliance, security, cost, and management.

Depending on your pool allocation mode and the resources to which a policy should apply, use Azure Policy with Batch in one of the following ways:

- Directly, using the Microsoft.Batch/batchAccounts resource. A subset of the properties for a Batch account can be used. For example, your policy can include valid Batch account regions, allowed pool allocation mode, and whether a public network is enabled for accounts.
- Indirectly, using the Microsoft.Compute/virtualMachineScaleSets resource. Batch accounts with user subscription pool allocation mode can have policy set on the virtual machine scale set resources that are created in the Batch account subscription. For example, allowed VM sizes and ensure certain extensions are run on each pool node.

## Next steps

- Review the [Azure security baseline for Batch](security-baseline.md).
- Read more [best practices for Azure Batch](best-practices.md).
