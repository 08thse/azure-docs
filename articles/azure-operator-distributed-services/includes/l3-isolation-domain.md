---
title: "Azure Operator Distributed Services: Creation of L3 Isolation Domain"
description: Learn how create L3 Isolation Domain. 
author: jwheeler60 #Required; your GitHub user alias, with correct capitalization.
ms.author: johnwheeler #Required; microsoft alias of author; optional team alias.
ms.service:  Azure Operator Distributed Services #Required;
ms.topic: include #Required; leave this attribute/value as-is.
ms.date: 01/26/2023 #Required; mm/dd/yyyy format.
---

* Create a L3 isolation domain

```azurecli
  az nf l3domain create --resource-name "<YourL3IsolationDomainName>" \
    --resource-group "<YourResourceGroupName>" \
    --subscription "<YourSubscription>" \
    --nf-id "<NetworkFabricResourceId >" \
    --location "<ClusterLocation>"
```

* Create an `internalnetwork` resource for every VLAN/subnet that you need to include in your L3 isolation domain

L3 isolation domain, you'll need to create an `internalnetwork` resource
>[!NOTE]
The following example uses the minimal configuration you will need to create a valid internal network. The optional parameters are not shown.

```azurecli
  az managednetworkfabric internalnetwork create  --name "<L3IsolationDomainInternalNetworkName>" \
    --resource-group "<YourResourceGroupName>" \
    --subscription "<YourSubscription>" \
    --l3-isolation-domain-name "<YourL3IsolationDomainName>" \
    --vlan-id < YourNetworkVlan> \
    --mtu "<MtuOfThisNetwork> \
    --peer-asn <PeerAsNumber> \
    --fabric-asn "<FabricAsNumber > \
    --ipv4-neighbor-address <YourSubetInfoHere > \
    --connected-i-pv4-subnets prefix="<YourSubnetCIDR> gateway="<YourGatewayIp>
```

* (Optional) Repeat, as needed, for any other `internalnetwork(s)` that have to be added to this L3 isolation domain
* Enable the L3 isolation domain after all `internalnetworks` have been created

```azurecli
  az managednetworkfabric l3isolationdomain update-admin-state \
    --name "<YourL3IsolationDomainName>" \
    --resource-group "<YourResourceGroupName>" \
    --subscription "<YourSubscription>" \
    --state Enable
```

* Repeat to create more L3 isolation domain(s).
