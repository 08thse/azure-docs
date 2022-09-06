---
title: Elastic SAN troubleshooting
description: Learn about the capacity, IOPS, and throughput rates for Azure Elastic SAN.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 08/01/2022
ms.author: rogarana
ms.subservice: elastic-san
---

# Troubleshoot issues with Azure Elastic SAN

## Can't create volumes

If you can create the SAN itself, and volume groups, but not volumes, your SAN might be in a different region than its virtual network.

### workaround

To remedy this, make sure that the **Microsoft.Storage** endpoint is registered in your virtual network's subnet and use the following PowerShell command:

```azurepowershell
Register-AzProviderFeature -FeatureName "AllowGlobalTagsForStorage" -ProviderNamespace "Microsoft.Network"
```