---
title: "Azure Operator Distributed Services: Interact with AKS-Hybrid Cluster"
description: Learn how to manage (view, list, update, delete) AKS-Hybrid clusters.
author: pvapheas
ms.author: pvapheas
ms.service: Azure Operator Distributed Services
ms.topic: how-to
ms.date: 02/02/2023
ms.custom: template-how-to
---

# How To Interact with AKS-Hybrid Cluster

This document shows how to manage an AKS-Hybrid cluster that you use for your CNF workloads.

## Before you begin

You'll need:

- You should have created an [AKS-Hybrid Cluster](./quickstarts-tenant-workload-deployment.md#how-to-create-aks-hybrid-cluster-for-cnf-workloads)
- <`YourAKS-HybridClusterName`>: the name of your previously created AKS-Hybrid cluster
- <`YourSubscription`>: your subscription name or ID where the AKS-Hybrid cluster was created
- <`YourResourceGroupName`>: the name of the Resource group where the AKS-Hybrid cluster was created
- <`tags`>: space-separated key=value tags: key[=value] [key[=value] ...]. Use '' to clear existing tags
- You should have installed the [CLI extensions](./howto-install-networkcloud-cli-extensions.md)

## List command

To get a list of AKS_Hybrid clusters in your Resource group:

```azurecli
    az hybridaks list -o table \
    --resource-group "<YourResourceGroupName>" \
    --subscription "<YourSubscription>"
```

## Show command

To see the properties of AKS-Hybrid cluster named `YourAKS-HybridClustername`:

```azurecli
  az hybridaks show --name "<YourAKS-HybridClusterName>" \
    --resource-group "< YourResourceGroupName >" \
    --subscription "< YourSubscription >"
```

## Update command

To update the properties of your AKS-Hybrid cluster:

```azurecli
    az hybridaks update --name "<YourAKS-HybridClustername>" \
    --resource-group "<YourResourceGroupName>" \
    --subscription "< YourSubscription>" \
    --tags "<YourAKS-HybridClusterTags>"
```

## Delete command

To delete the AKS-Hybrid cluster named `YourAKS-HybridClustername`:

```azurecli
    az hybridaks delete --name "<YourAKS-HybridClustername>" \
    --resource-group "<YourResourceGroupName >" \
    --subscription "<YourSubscription>"
```
