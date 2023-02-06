---
title: "Azure Operator Distributed Services: How to configure the L2 and L3 Isolation Domains in AODS instances"
description: Learn to create, view, list, update, delete commands for Layer 2 and Layer Isolation Domains in AODS instances
author: surajmb #Required
ms.author: surmb #Required
ms.service: Azure Operator Distributed Services #Required
ms.topic: how-to #Required; leave this attribute/value as-is.
ms.date: 02/02/2023 #Required; mm/dd/yyyy format.
ms.custom: template-how-to #Required; leave this attribute/value as-is.
---

# Configure L2 and L3 Isolation Domains using managed network fabric services

This article describes how you can manage your Layer 2 and Layer 3 Isolation Domains using the Azure Command Line Interface (AzureCLI). create, status, update, delete and check status of Layer 2 and Layer 3 Isolation Domains.

## Prerequisites

- Ensure Network Fabric Controller and Network Fabric have been created.
- Install the latest version of [needed CLIs](./howto-install-networkcloud-cli-extensions.md)

## Register providers for Managed Network Fabric

This step can be skipped if already done during Network Fabric Controller Creation

1. In Azure CLI, enter the command: `az provider register --namespace Microsoft.ManagedNetworkFabric`
1. Monitor the registration process. Registration may take up to 10 minutes: `az provider show -n Microsoft.ManagedNetworkFabric -o table`
1. Once registered, you should see the `RegistrationState` change to `Registered`: `az provider show -n Microsoft.ManagedNetworkFabric -o table`.

## Isolation Domains

You create Isolation Domains to enable layer 2 and layer 3 connectivity between AODS workloads. The Isolation Domains enable communication between workloads hosted in the same rack (intra-rack communication) or different racks (inter-rack communication). You can use the CLI for creating L2 and L3 Isolation Domains.

> [!NOTE]
> AODS reserves VLANs <=500 for Platform use, and therefore VLANs in this range can't be used for your (tenant) workload networks. You should use VLAN values between 501 and 4095.

### Sign-in to your Azure account

Sign-in to your Azure account and set the subscription to your Azure subscription ID. This ID should be the same subscription ID used across all AODS resources.

```azurecli
az login
az account set --subscription ********-****-****-****-*********
```

### Parameters for Isolation Domain management

| Parameter           | Description                                                                                                                                                                                                                         |
| :------------------ | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| vlan-id             | VLAN identifier value. VLANs 1-500 are reserved and can't be used. The VLAN identifier value can't be changed once specified. The Isolation Domain must be deleted and recreated if the VLAN identifier value needs to be modified. |
| administrativeState | Indicate administrative state of the Isolation Domain                                                                                                                                                                               |
| provisioningState   | Indicates provisioning state                                                                                                                                                                                                        |
| subscriptionId      | Your Azure subscriptionId for your AODS instance.                                                                                                                                                                                   |
| resourceGroupName   | Use the corresponding NFC resource group name                                                                                                                                                                                       |
| resource-name       | Resource Name of the Isolation Domain                                                                                                                                                                                               |
| nf-id               | ARM ID of the Network Fabric                                                                                                                                                                                                        |
| location            | Azure region where the resource is being created                                                                                                                                                                                    |

### Create L2 Isolation Domain

You use an L2 Isolation Domain to establish layer 2 connectivity between workloads running on AODS compute nodes.

```azurecli
az nf l2domain create \
--resource-group "NFCresourcegroupname" \
--resource-name "example-l2domain" \
--location "eastus" \
--nf-id "/subscriptions/xxxxxx-xxxxxx-xxxx-xxxx-xxxxxx/resourceGroups/NFresourcegroupname/providers/Microsoft.ManagedNetworkFabric/NetworkFabrics/NFname" \
--vlan-id  501\
--mtu 1500
```

Expected output:

```json
{
  "administrativeState": "Disabled",
  "annotation": null,
  "disabledOnResources": null,
  "id": "/subscriptions/xxxxxx-xxxxxx-xxxx-xxxx-xxxxxx/resourceGroups/NFCresourcegroupname/providers/Microsoft.ManagedNetworkFabric/l2IsolationDomains/example-l2domain",
  "location": "eastus2euap",
  "mtu": 1500,
  "name": "example-l2domain",
  "networkFabricId": "/subscriptions/xxxxxx-xxxxxx-xxxx-xxxx-xxxxxx/NFresourceGroups/resourcegroupname/providers/Microsoft.ManagedNetworkFabric/networkFabrics/NFName",
  "provisioningState": "Succeeded",
  "resourceGroup": "NFCresourcegroupname",
  "systemData": {
    "createdAt": "2022-11-02T05:59:00.534027+00:00",
    "createdBy": "email@address.com",
    "createdByType": "User",
    "lastModifiedAt": "2022-11-02T05:59:00.534027+00:00",
    "lastModifiedBy": "email@address.com",
    "lastModifiedByType": "User"
  },
  "tags": null,
  "type": "microsoft.managednetworkfabric/l2isolationdomains",
  "vlanId": 501
}
```

### Show L2 Isolation Domains

This command shows L2 Isolation Domain details and administrative state of Isolation Domain.

```azurecli
az nf l2domain show --resource-group "resourcegroupname" --resource-name "example-l2domain"
```

Expected Output

```json
{
  "administrativeState": "Disabled",
  "annotation": null,
  "disabledOnResources": null,
  "id": "/subscriptions/xxxxxx-xxxxxx-xxxx-xxxx-xxxxxx/resourceGroups/NFCresourcegroupname/providers/Microsoft.ManagedNetworkFabric/l2IsolationDomains/example-l2domain",
  "location": "eastus2euap",
  "mtu": 1500,
  "name": "example-l2domain",
  "networkFabricId": "/subscriptions/xxxxxx-xxxxxx-xxxx-xxxx-xxxxxx/NFresourceGroups/resourcegroupname/providers/Microsoft.ManagedNetworkFabric/networkFabrics/NFName",
  "provisioningState": "Succeeded",
  "resourceGroup": "NFCresourcegroupname",
  "systemData": {
    "createdAt": "2022-11-02T05:59:00.534027+00:00",
    "createdBy": "email@address.com",
    "createdByType": "User",
    "lastModifiedAt": "2022-11-02T05:59:00.534027+00:00",
    "lastModifiedBy": "email@address.com",
    "lastModifiedByType": "User"
  },
  "tags": null,
  "type": "microsoft.managednetworkfabric/l2isolationdomains",
  "vlanId": 2026
}
```

### List All L2 Isolation Domains

This command lists all l2 Isolation Domains available in resource group.

```azurecli
az nf l2domain list --resource-group "resourcegroupname"
```

Expected Output

```json
 {
    "administrativeState": "Enabled",
    "annotation": null,
    "disabledOnResources": null,
    "id": "/subscriptions/xxxxxx-xxxxxx-xxxx-xxxx-xxxxxx/resourceGroups/NFCresourcegroupname/providers/Microsoft.ManagedNetworkFabric/l2IsolationDomains/example-l2domain",
    "location": "eastus",
    "mtu": 1500,
    "name": "example-l2domain",
    "networkFabricId": "/subscriptions/xxxxxx-xxxxxx-xxxx-xxxx-xxxxxx/NFresourceGroups/resourcegroupname/providers/Microsoft.ManagedNetworkFabric/networkFabrics/NFName",
    "provisioningState": "Succeeded",
    "resourceGroup": "NFCresourcegroupname",
    "systemData": {
      "createdAt": "2022-10-24T22:26:33.065672+00:00",
      "createdBy": "email@address.com",
      "createdByType": "User",
      "lastModifiedAt": "2022-10-26T14:46:45.753165+00:00",
      "lastModifiedBy": "d1bd24c7-b27f-477e-86dd-939e107873d7",
      "lastModifiedByType": "Application"
    },
    "tags": null,
    "type": "microsoft.managednetworkfabric/l2isolationdomains",
    "vlanId": 501
  },
  {
    "administrativeState": "Enabled",
    "annotation": null,
    "disabledOnResources": null,
    "id": "/subscriptions/xxxxxx-xxxxxx-xxxx-xxxx-xxxxxx/resourceGroups/NFCresourcegroupname/providers/Microsoft.ManagedNetworkFabric/l2IsolationDomains/example-l2domain",
    "location": "eastus",
    "mtu": 1500,
    "name": "example-l2domain",
    "networkFabricId": "/subscriptions/xxxxxx-xxxxxx-xxxx-xxxx-xxxxxx/resourceGroups/NFresourcegroupname/providers/Microsoft.ManagedNetworkFabric/networkFabrics/NFName",
    "provisioningState": "Succeeded",
    "resourceGroup": "NFCresourcegroupname",
    "systemData": {
      "createdAt": "2022-10-27T03:03:15.099007+00:00",
      "createdBy": "email@address.com",
      "createdByType": "User",
      "lastModifiedAt": "2022-10-27T03:45:31.864152+00:00",
      "lastModifiedBy": "d1bd24c7-b27f-477e-86dd-939e107873d7",
      "lastModifiedByType": "Application"
    },
    "tags": null,
    "type": "microsoft.managednetworkfabric/l2isolationdomains",
    "vlanId": 501
  },
```

### Enable/Disable L2 Isolation Domain

This command is used to change the administrative state of the Isolation Domain.

**Note:**
Only after the Isolation Domain is Enabled, that the layer 2 Isolation Domain configuration is pushed to the Network Fabric devices.

```azurecli
az nf l2domain update-admin-state --resource-group "NFCresourcegroupname" --resource-name "example-l2domain" --state Enable/Disable
```

Expected Output

```json
{
  "administrativeState": "Enabled",
  "annotation": null,
  "disabledOnResources": null,
  "id": "/subscriptions/xxxxxx-xxxxxx-xxxx-xxxx-xxxxxx/resourceGroups/NFCresourcegroupname/providers/Microsoft.ManagedNetworkFabric/l2IsolationDomains/example-l2domain",
  "location": "eastus2euap",
  "mtu": 1500,
  "name": "example-l2domain",
  "networkFabricId": "/subscriptions/xxxxxx-xxxxxx-xxxx-xxxx-xxxxxx/resourceGroups/NFresourcegroupname/providers/Microsoft.ManagedNetworkFabric/networkFabrics/NFName",
  "provisioningState": "Succeeded",
  "resourceGroup": "NFCresourcegroupname",
  "systemData": {
    "createdAt": "2022-11-02T05:59:00.534027+00:00",
    "createdBy": "email@address.com",
    "createdByType": "User",
    "lastModifiedAt": "2022-11-02T06:01:03.552772+00:00",
    "lastModifiedBy": "d1bd24c7-b27f-477e-86dd-939e107873d7",
    "lastModifiedByType": "Application"
  },
  "tags": null,
  "type": "microsoft.managednetworkfabric/l2isolationdomains",
  "vlanId": 501
}
```

### Delete L2 Isolation Domain

This command is used to delete L2 Isolation Domain

```azurecli
az nf l2domain delete --resource-group "resourcegroupname" --resource-name "example-l2domain"
```

Expected output:

```output
Please use show or list command to validate that isolation domain is deleted. Deleted resources will not appear in result
```

### Create L3 Isolation Domain

- Layer 3 Isolation Domain enables layer 3 connectivity between workloads running on AODS compute nodes. The L3 Isolation Domain enables the workloads to exchange layer 3 information with Network Fabric devices.
- Layer 3 isolation domain has two components - Internal and External Networks
- At least one or more internal networks are required to be created. The internal networks define layer 3 connectivity between NFs running in AODS compute nodes and an optional external network. The external network provides connectivity between the internet and internal networks via your PEs.
- L3 isolation domain enables deploying workloads that advertise service IPs to the fabric via BGP
- Fabric ASN refers to the ASN of the network devices on the Fabric. The Fabric ASN is specified while creating the Network Fabric.
- Peer ASN refers to ASN of the Network Functions in AODS, and it can't be same as Fabric ASN.
- The workflow for a successful provisioning of an L3 Isolation Domain is as follows:
  - **Create a L3 Isolation Domain**
  - **Create one or more Internal Networks**
  - **Enable a L3 Isolation Domain**
- To make changes to the L3 Isolation Domain, first Disable the L3 Isolation Domain (Administrative state). Re-enable the L3 Isolation Domain (AdministrativeState state) once the changes are completed:
  - **Disable the L3 Isolation Domain**
  - **Make changes to the L3 Isolation Domain**
  - **Re-enable the L3 Isolation Domain**
- Procedure to show, enable/disable and delete IPv6 based Isolation Domains is same as used for IPv4.

```azurecli
az nf l3domain create
--resource-group "NFCresourcegroupname"
--resource-name "example-l3domain"
--location "eastus"
--nf-id "/subscriptions/xxxxxx-xxxxxx-xxxx-xxxx-xxxxxx/resourceGroups/NFresourcegroupname/providers/Microsoft.ManagedNetworkFabric/NetworkFabrics/NFName"
--external '{"optionBConfiguration": {"importRouteTargets": ["1234:1235"], "exportRouteTargets": ["1234:1234"]}}'
```

> [!NOTE]
> For MPLS Option 10 (B) connectivity to external networks via PE devices, you can specify option (B) parameters while creating an Isolation Domain.

Expected Output

```json
{
  "administrativeState": "Disabled",
  "annotation": null,
  "description": null,
  "disabledOnResources": null,
  "external": null,
  "id": "/subscriptions/xxxxxx-xxxxxx-xxxx-xxxx-xxxxxx/resourceGroups/resourcegroupname/providers/Microsoft.ManagedNetworkFabric/l3IsolationDomains/example-l3domain",
  "internal": null,
  "location": "eastus2euap",
  "name": "example-l3domain",
  "networkFabricId": "/subscriptions/xxxxxx-xxxxxx-xxxx-xxxx-xxxxxx/NFresourceGroups/resourcegroupname/providers/Microsoft.ManagedNetworkFabric/networkFabrics/NFName",
  "optionBDisabledOnResources": null,
  "provisioningState": "Accepted",
  "resourceGroup": "resourcegroupname",
  "systemData": {
    "createdAt": "2022-11-02T06:23:43.372461+00:00",
    "createdBy": "email@address.com",
    "createdByType": "User",
    "lastModifiedAt": "2022-11-02T06:23:43.372461+00:00",
    "lastModifiedBy": "email@address.com",
    "lastModifiedByType": "User"
  },
  "tags": null,
  "type": "microsoft.managednetworkfabric/l3isolationdomains"
}
```

### Show L3 Isolation Domains

You can get the L3 Isolation Domains details and administrative state.

```azurecli
az nf l3domain show --resource-group "resourcegroupname" --resource-name "example-l3domain"
```

Expected Output

```json
{
  "administrativeState": "Disabled",
  "annotation": null,
  "description": null,
  "disabledOnResources": null,
  "external": null,
  "id": "/subscriptions/xxxxxx-xxxxxx-xxxx-xxxx-xxxxxx/resourceGroups/resourcegroupname/providers/Microsoft.ManagedNetworkFabric/l3IsolationDomains/example-l3domain",
  "internal": null,
  "location": "eastus2euap",
  "name": "example-l3domain",
  "networkFabricId": "/subscriptions/xxxxxx-xxxxxx-xxxx-xxxx-xxxxxx/resourceGroups/NFresourcegroupname/providers/Microsoft.ManagedNetworkFabric/networkFabrics/NFName",
  "optionBDisabledOnResources": null,
  "provisioningState": "Accepted",
  "resourceGroup": "resourcegroupname",
  "systemData": {
    "createdAt": "2022-11-02T06:23:43.372461+00:00",
    "createdBy": "email@address.com",
    "createdByType": "User",
    "lastModifiedAt": "2022-11-02T06:23:43.372461+00:00",
    "lastModifiedBy": "email@address.com",
    "lastModifiedByType": "User"
  },
  "tags": null,
  "type": "microsoft.managednetworkfabric/l3isolationdomains"
}
```

### List All L3 Isolation Domains

You can get a list of all L3 Isolation Domains available in a resource group.

```azurecli
az nf l3domain list --resource-group "resourcegroupname"
```

Expected Output

```json
{
  "administrativeState": "Disabled",
  "annotation": null,
  "description": null,
  "disabledOnResources": null,
  "external": null,
  "id": "/subscriptions/xxxxxx-xxxxxx-xxxx-xxxx-xxxxxx/resourceGroups/resourcegroupname/providers/Microsoft.ManagedNetworkFabric/l3IsolationDomains/example-l3domain",
  "internal": null,
  "location": "eastus2euap",
  "name": "example-l3domain",
  "networkFabricId": "/subscriptions/xxxxxx-xxxxxx-xxxx-xxxx-xxxxxx/resourceGroups/NFresourcegroupname/providers/Microsoft.ManagedNetworkFabric/networkFabrics/NFName",
  "optionBDisabledOnResources": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "resourcegroupname",
  "systemData": {
    "createdAt": "2022-11-02T06:23:43.372461+00:00",
    "createdBy": "email@address.com",
    "createdByType": "User",
    "lastModifiedAt": "2022-11-02T06:23:43.372461+00:00",
    "lastModifiedBy": "email@address.com",
    "lastModifiedByType": "User"
  },
  "tags": null,
  "type": "microsoft.managednetworkfabric/l3isolationdomains"
}
```

once the Isolation Domain is created successfully, the next step is to create an internal network.

## Internal Network Creation

| Parameter                    | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  | Example     | Required | type   |
| :--------------------------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :---------- | :------- | :----- |
| vlanId                       | VLAN identifier                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              | 1001        | True     | string |
| connectedIPv4Subnets/Prefix  | IP subnet used by the HAKS cluster's workloads                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | 10.0.0.0/24 | True     | string |
| connectedIPv4Subnets/gateway | IPv4 subnet gateway used by the HAKS cluster's workloads                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     | 10.0.0.1    | True     | string |
| staticIPv4Routes/Prefix      | IPv4 Prefix of the static route                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              | NA          |
| staticIPv4Routes/nexthop     | IPv4 next hop address                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        | NA          |
| defaultRouteOriginate        | True/False "Enables default route to be originated when advertising routes via BGP"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| fabricASN                    | ASN of Network Fabric                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        | 65048       | True     | string |
| peerASN                      | Peer ASN of Network Function                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | 65047       | True     | string |
| IPv4Prefix                   | IPv4 Prefix of NFs for BGP peering (range).<br />The maximum length of the prefix is /28. For example, in 10.1.0.0/28, 10.1.0.0 to 10.1.0.7 are reserved and can't be used by workloads. 10.1.0.1 is assigned as VIP on both CEs. 10.1.0.2 is assigned on CE1 and 10.1.0.3 is assigned on CE2.<br />Workloads must peer to CE1 and CE2. The IP addresses of workloads can start from 10.0.0.8.<br />When only the prefix is configured, and `ipv4NeighborAddresses` isn't specified, the fabric configures the valid addresses in the prefix as part of the listen range. If `ipv4NeighborAddresses` is specified, the fabric configures the specified addresses as neighbors.<br />A smaller prefix than /28, for example /29 or /30 can also be configured | NA          |          |

This command creates an internal network with BGP configuration and specified peering address.

**Note:** You need to create an internal network before you enable an L3 Isolation Domain.

```azurecli
az nf internalnetwork create \
--resource-group "resourcegroupname" \
--l3-isolation-domain-name "example-l3domain" \
--resource-name "example-internalnetwork" \
--location "eastus"
--vlan-id 1001 \
--connected-ipv4-subnets '[{"prefix":"10.0.0.0/24", "gateway":"10.0.0.1"}]' \
--mtu 1500 \
--bgp-configuration '{"fabricASN": 65048, "defaultRouteOriginate":true, "peerASN": 65047 ,"ipv4NeighborAddress":[{"address": "10.0.0.11"}]}'

```

Expected Output

```json
{
  "administrativeState": "Enabled",
  "annotation": null,
  "bfdDisabledOnResources": null,
  "bfdForStaticRoutesDisabledOnResources": null,
  "bgpConfiguration": {
    "annotation": null,
    "bfdConfiguration": null,
    "defaultRouteOriginate": false,
    "fabricAsn": 65048,
    "ipv4NeighborAddress": [
      {
        "address": "10.0.0.11",
        "operationalState": null
      }
    ],
    "ipv4Prefix": null,
    "ipv6NeighborAddress": null,
    "ipv6Prefix": null,
    "peerAsn": 65047
  },
  "bgpDisabledOnResources": null,
  "connectedIPv4Subnets": [
    {
      "annotation": null,
      "gateway": null,
      "prefix": "10.0.0.0/24"
    }
  ],
  "connectedIPv6Subnets": null,
  "disabledOnResources": null,
  "id": "/subscriptions/xxxxxx-xxxxxx-xxxx-xxxx-xxxxxx/resourceGroups/resourcegroupname/providers/Microsoft.ManagedNetworkFabric/l3IsolationDomains/example-l3domain/internalNetworks/example-internalnetwork",
  "mtu": 1500,
  "name": "example-internalnetwork",
  "provisioningState": "Succeeded",
  "resourceGroup": "resourcegroupname",
  "staticRouteConfiguration": null,
  "systemData": {
    "createdAt": "2022-11-02T06:25:05.983557+00:00",
    "createdBy": "email@address.com",
    "createdByType": "User",
    "lastModifiedAt": "2022-11-02T06:25:05.983557+00:00",
    "lastModifiedBy": "email@address.com",
    "lastModifiedByType": "User"
  },
  "type": "microsoft.managednetworkfabric/l3isolationdomains/internalnetworks",
  "vlanId": 1001
}
```

**Note:** This command creates an Internal network where the BGP speakers of the NFs will be in the range 10.0.0.8 through 10.0.0.15

```azurecli
az nf internalnetwork create \
--resource-group "resourcegroupname" \
--l3-isolation-domain-name "example-l3domain" \
--name "example-internalnetwork" \
--vlan-id 1000  \
--connected-ipv4-subnets '[{"prefix":"10.0.0.0/24", "gateway":"10.0.0.1"}]' \
--mtu 1500
--bgp-configuration '{"fabricASN": 65048, "defaultRouteOriginate":true, "peerASN": 5001 ,"ipv4Prefix": "10.0.0.0/28"}'

```

### Internal Network Creation using IPv6

```azurecli
az nf internalnetwork create \
--resource-group "resourcegroupname" \
--l3-isolation-domain-name "example-l3domain" \
--resource-name "example-internalipv6network" \
--location "eastus"
--vlan-id 1090 \
--connected-ipv6-subnets '[{"prefix":"10:101:1::0/64", "gateway":"10:101:1::1"}]'
 --mtu 1500
 --bgp-configuration '{"fabricASN": 65048, "defaultRouteOriginate":true, "peerASN": 65020 ,"ipv6NeighborAddress":[{"address": "10:101:1::11"}]}
```

Expected Output

```json
{
  "administrativeState": "Enabled",
  "annotation": null,
  "bfdDisabledOnResources": null,
  "bfdForStaticRoutesDisabledOnResources": null,
  "bgpConfiguration": {
    "annotation": null,
    "bfdConfiguration": null,
    "defaultRouteOriginate": true,
    "fabricAsn": 65048,
    "ipv4NeighborAddress": null,
    "ipv4Prefix": null,
    "ipv6NeighborAddress": [
      {
        "address": "10:101:1::11",
        "operationalState": null
      }
    ],
    "ipv6Prefix": null,
    "peerAsn": 65020
  },
  "bgpDisabledOnResources": null,
  "connectedIPv4Subnets": null,
  "connectedIPv6Subnets": [
    {
      "annotation": null,
      "gateway": "10:101:1::1",
      "prefix": "10:101:1::0/64"
    }
  ],
  "disabledOnResources": null,
  "exportRoutePolicyId": null,
  "id": "/subscriptions//xxxxxx-xxxxxx-xxxx-xxxx-xxxxxx7/resourceGroups/fab1nfrg121322/providers/Microsoft.ManagedNetworkFabric/l3IsolationDomains/fab1-l3domain121822/internalNetworks/fab1-internalnetworkv16",
  "importRoutePolicyId": null,
  "mtu": 1500,
  "name": "example-internalipv6network",
  "provisioningState": "Succeeded",
  "resourceGroup": "resourcegroupname",
  "staticRouteConfiguration": null,
  "systemData": {
    "createdAt": "2022-12-15T12:10:34.364393+00:00",
    "createdBy": "email@address.com",
    "createdByType": "User",
    "lastModifiedAt": "2022-12-15T12:10:34.364393+00:00",
    "lastModifiedBy": "email@address.com",
    "lastModifiedByType": "User"
  },
  "type": "microsoft.managednetworkfabric/l3isolationdomains/internalnetworks",
  "vlanId": 1090
}
```

## External Network Creation

This command creates an External network using Azure CLI.

**Note:** For Option A You need to create an external network before you enable the L3 Isolation Domain. An external is dependent on Internal network, so an external can't be enabled without an internal network. The vlan-id value should be between 501 and 4095

```azurecli
az nf externalnetwork create
--resource-group "resourcegroupname"
--l3-isolation-domain-name "example-l3domain"
--name "example-externalnetwork"
--location "eastus"
--vlan-id 515
--fabric-asn 65025
--peer-asn 65026
--primary-ipv4-prefix "10.1.1.0/30"
--secondary-ipv4-prefix "10.1.1.4/30"
```

Expected Output

```json
{
  "administrativeState": null,
  "annotation": null,
  "bfdConfiguration": null,
  "bfdDisabledOnResources": null,
  "bgpDisabledOnResources": null,
  "disabledOnResources": null,
  "fabricAsn": 65025,
  "id": "/subscriptions/xxxxxx-xxxxxx-xxxx-xxxx-xxxxxx/resourceGroups/NFresourcegroupname/providers/Microsoft.ManagedNetworkFabric/l3IsolationDomains/l3domainNEWS3/externalNetworks/example-l3domain",
  "mtu": 0,
  "name": "example-l3domain",
  "peerAsn": 65026,
  "primaryIpv4Prefix": "10.1.1.0/30",
  "primaryIpv6Prefix": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "resourcegroupname",
  "secondaryIpv4Prefix": "10.1.1.4/30",
  "secondaryIpv6Prefix": null,
  "systemData": {
    "createdAt": "2022-10-29T17:24:32.077026+00:00",
    "createdBy": "email@address.com",
    "createdByType": "User",
    "lastModifiedAt": "2022-11-07T09:28:18.873754+00:00",
    "lastModifiedBy": "email@address.com",
    "lastModifiedByType": "User"
  },
  "type": "microsoft.managednetworkfabric/l3isolationdomains/externalnetworks",
  "vlanId": 515
}
```

### External Network Creation using Ipv6

```azurecli
az nf externalnetwork create
--resource-group " resourcegroupname "
--l3-isolation-domain-name " example-l3domain"
--resource-name " example-externalipv6network"
--location "westus3"
--vlan-id 516
--fabric-asn 65048
--peer-asn 65022
 --primary-ipv4-prefix "10:101:2::0/127"
--secondary-ipv6-prefix "10:101:3::0/127"
```

**Note:** Primary and Secondary IPv6 supported in this release is /127

Expected Output

```json
{
  "administrativeState": null,
  "annotation": null,
  "bfdConfiguration": null,
  "bfdDisabledOnResources": null,
  "bgpDisabledOnResources": null,
  "disabledOnResources": null,
  "exportRoutePolicyId": null,
  "fabricAsn": 65048,
  "id": "/subscriptions//xxxxxx-xxxxxx-xxxx-xxxx-xxxxxx/resourceGroups/fab1nfrg121322/providers/Microsoft.ManagedNetworkFabric/l3IsolationDomains/fab1-l3domain121822/externalNetworks/fab1-externalnetworkv6",
  "importRoutePolicyId": null,
  "mtu": 1500,
  "name": "example-externalipv6network",
  "peerAsn": 65022,
  "primaryIpv4Prefix": "10:101:2::0/127",
  "primaryIpv6Prefix": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "resourcegroupname",
  "secondaryIpv4Prefix": null,
  "secondaryIpv6Prefix": "10:101:3::0/127",
  "systemData": {
    "createdAt": "2022-12-16T07:52:26.366069+00:00",
    "createdBy": "email@address.com",
    "createdByType": "User",
    "lastModifiedAt": "2022-12-16T07:52:26.366069+00:00",
    "lastModifiedBy": "",
    "lastModifiedByType": "User"
  },
  "type": "microsoft.managednetworkfabric/l3isolationdomains/externalnetworks",
  "vlanId": 516
}
```

## Enable/Disable L3 Isolation Domains

This command is used change administrative state of L3 Isolation Domain, you have to run the az show command to verify if the Administrative state has changed to Enabled or not.

```azurecli
az nf l3domain update-admin-state --resource-group "resourcegroupname" --resource-name "example-l3domain" --state Enable/Disable
```

Expected Output

```json
{
  "administrativeState": "Enabled",
  "annotation": null,
  "description": null,
  "disabledOnResources": null,
  "external": null,
  "id": "/subscriptions/xxxxxx-xxxxxx-xxxx-xxxx-xxxxxx/resourceGroups/resourcegroupname/providers/Microsoft.ManagedNetworkFabric/l3IsolationDomains/example-l3domain",
  "internal": null,
  "location": "eastus2euap",
  "name": "example-l3domain",
  "networkFabricId": "/subscriptions/xxxxxx-xxxxxx-xxxx-xxxx-xxxxxx/NFresourceGroups/resourcegroupname/providers/Microsoft.ManagedNetworkFabric/networkFabrics/NFName",
  "optionBDisabledOnResources": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "resourcegroupname",
  "systemData": {
    "createdAt": "2022-11-02T06:23:43.372461+00:00",
    "createdBy": "email@address.com",
    "createdByType": "User",
    "lastModifiedAt": "2022-11-02T06:25:53.240975+00:00",
    "lastModifiedBy": "d1bd24c7-b27f-477e-86dd-939e107873d7",
    "lastModifiedByType": "Application"
  },
  "tags": null,
  "type": "microsoft.managednetworkfabric/l3isolationdomains"
}
```

## Delete L3 Isolation Domains

This command is used to delete L3 Isolation Domain

```azurecli
az nf l3domain delete --resource-group "fab1-nf" --resource-name "example-l3domain"
```

Use the `show` or `list` commands to validate that the Isolation Domain has been deleted.

## Create Networks in L3 Isolation Domain

- Internal networks enable layer 3 inter-rack and intra-rack communication between workloads via exchanging routes with the fabric. An L3 Isolation Domain can support multiple internal networks, each on a separate VLAN

- External networks enable workloads to have layer 3 connectivity with your provider edge. It also allows for workloads to interact with external services like firewall and DNS.

- Fabric ASN is provided by you when creating external networks

## Example Isolation Domain operations for Network Function

:::image type="content" source="media/Network-Function.png" alt-text="Network Function networking diagram":::
Figure Network Function networking diagram

The diagram represents an example Network Function, with three different internal networks Trust, Untrust and Management (`Mgmt`). Each of the internal networks is created in its own L3 Isolation Domain (`L3 ISD`).

### Create required L3 Isolation Domains

**Create an L3 Isolation Domain `l3untrust`**

```azurecli
az nf l3domain create --resource-group "resourcegroupname" --resource-name "l3untrust" --location "eastus" --nf-id "/subscriptions/xxxxxx-xxxxxx-xxxx-xxxx-xxxxxx/resourceGroups/NFresourcegroupname/providers/Microsoft.ManagedNetworkFabric/networkFabrics/NFName"
```

**Create an L3 Isolation Domain `l3trust`**

```azurecli
az nf l3domain create --resource-group "resourcegroupname" --resource-name "l3trust" --location "eastus" --nf-id "/subscriptions/xxxxxx-xxxxxx-xxxx-xxxx-xxxxxx/resourceGroups/NFresourcegroupname/providers/Microsoft.ManagedNetworkFabric/networkFabrics/NFName"
```

**Create an L3 Isolation Domain `l3mgmt`**

```azurecli
az nf l3domain create --resource-group "resourcegroupname" --resource-name "l3mgmt" --location "eastus" --nf-id "/subscriptions/xxxxxx-xxxxxx-xxxx-xxxx-xxxxxx/resourceGroups/NFresourcegroupname/providers/Microsoft.ManagedNetworkFabric/networkFabrics/NFName"
```

### Create required Internal Networks

Now that the required L3 Isolation Domains have been created, you can create the three (3) Internal Networks. The `IPv4 prefix` for these networks are:

- Trusted network: 10.151.1.11/24
- Management network: 10.151.2.11/24
- Untrusted network: 10.151.3.11/24

**Create Internal Network in `l3untrust` L3 Isolation Domain**

```azurecli
az nf internalnetwork create --resource-group "resourcegroupname" --l3-isolation-domain-name l3untrust --resource-name untrustnetwork --location "eastus" --vlan-id 502 --fabric-asn 65048 --peer-asn 65047--connected-i-pv4-subnets prefix="10.151.3.11/24" --mtu 1500
```

**Create Internal Network in `l3trust` L3 Isolation Domain**

```azurecli
az nf internalnetwork create --resource-group "resourcegroupname" --l3-isolation-domain-name l3trust --resource-name trustnetwork --location "eastus" --vlan-id 503 --fabric-asn 65048 --peer-asn 65047--connected-i-pv4-subnets prefix="10.151.1.11/24" --mtu 1500
```

**Create Internal Network in `l3mgmt` L3 Isolation Domain**

```azurecli
az nf internalnetwork create --resource-group "resourcegroupname" --l3-isolation-domain-name l3mgmt --resource-name mgmtnetwork --location "eastus" --vlan-id 504 --fabric-asn 65048 --peer-asn 65047--connected-i-pv4-subnets prefix="10.151.2.11/24" --mtu 1500
```

### Enable the L3 Isolation Domains

You've created the required L3 Isolation Domains and the associated internal network. You now need to enable these Isolation Domains.

**Enable L3 Isolation Domain `l3untrust`**

```azurecli
az nf l3domain update-admin-state --resource-group "resourcegroupname" --resource-name "l3untrust" --state Enable
```

**Enable L3 Isolation Domain `l3trust`**

```azurecli
az nf l3domain update-admin-state --resource-group "resourcegroupname" --resource-name "l3trust" --state Enable
```

**Enable L3 Isolation Domain `l3mgmt`**

```azurecli
az nf l3domain update-admin-state --resource-group "resourcegroupname" --resource-name "l3mgmt" --state Enable
```

## Example L2 Isolation Domain creation for a workload

First, you need to create the `l2HAnetwork` L2 Isolation Domain and then enable it.

**Create `l2HAnetwork` L2 Isolation Domain**

```azurecli
az nf l2domain create --resource-group "resourcegroupname" --resource-name "l2HAnetwork" --location "eastus" --nf-id "/subscriptions/xxxxxx-xxxxxx-xxxx-xxxx-xxxxxx/resourceGroups/NFresourcegroupname/providers/Microsoft.ManagedNetworkFabric/networkFabrics/NFName" --vlan-id 505 --mtu 1500
```

**Enable `l2HAnetwork` L2 Isolation Domain**

```azurecli
az nf l2domain update-administrative-state --resource-group "resourcegroupname" --resource-name "l2HAnetwork" --state Enable
```
