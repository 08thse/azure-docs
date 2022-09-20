---
title: Increase the size of an Azure Elastic SAN
description: Learn how to increase the size of an Azure Elastic SAN and its volumes with the Azure portal, Azure PowerShell module, or Azure CLI.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/12/2022
ms.author: rogarana
ms.subservice: elastic-san
---

# Increase the size of an Elastic SAN

This article covers increasing the size of an Elastic SAN and an individual volume, if you need additional storage or performance. Be sure you need the storage or performance before you increase the size because decreasing the size isn't supported, to prevent data loss.

## Expand SAN size

First, increase the size of your Elastic SAN

# [PowerShell](#tab/azure-powershell)

```azurepowershell

# You can either update the base size or the additional size.
# This command updates the base size, to update the additional size, replace -BaseSizeTiB $newBaseSizeTib with -ExtendedCapacitySizeTib $newExtendedSizeTib

Update-AzElasticSan -ResourceGroupName $resourceGroupName -Name $sanName -BaseSizeTib $newBaseSizeTib

```

# [Azure CLI](#tab/azure-cli)

```azurecli
# You can either update the base size or the additional size.
# This command updates the base size, to update the additional size, replace -base-size-tib $newBaseSizeTib with –extended-capacity-size-tib $newExtendedCapacitySizeTib

az elastic-san update -e $sanName -g $resourceGroupName –base-size-tib $newBaseSizeTib
```

---

## Expand volume size

Once you've expanded the size of your SAN, you can either create an additional volume, or expand the size of an existing volume.

# [PowerShell](#tab/azure-powershell)

```azurepowershell
Update-AzElasticSanVolume -ResourceGroupName $resourceGroupName -ElasticSanName $sanName -GroupName $volumeGroupName -Name $volumeName -sizeGib $newVolumeSize
```

# [Azure CLI](#tab/azure-cli)

```azurecli
az elastic-san update -e $sanName -g $resourceGroupName -v $volumeGroupName -n $volumeName –size-gib $newVolumeSize
```

---

## Create a new volume

# [PowerShell](#tab/azure-powershell)

```azurepowershell
## Create the volume, this command only creates one.
New-AzElasticSanVolume -ResourceGroupName $rgName -ElasticSanName $sanName -GroupName $volGroupName -Name "volumeName" -sizeGiB 2000
```

# [Azure CLI](#tab/azure-cli)

```azurecli
az elastic-san volume-group create --elastic-san-name $sanName -g $resourceGroupName -v volumeGroupName -n $volumeName –size-gib 2000
```

---