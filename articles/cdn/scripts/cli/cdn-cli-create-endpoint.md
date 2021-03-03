---
title: Create an Azure Content Delivery Network (CDN) profile and endpoint 
description: Azure CLI sample to create an Azure CDN profile, endpoint, origin group, and origin. 
author: asudbring
ms.author: allensu
manager: danielgi
ms.date: 03/03/2021
ms.topic: sample
ms.service: azure-cdn
ms.devlang: azurecli 
ms.custom: devx-track-azurecli
---

# Create an Azure CDN profile and endpoint

Manage these Azure Content Delivery Network (CDN) operations with sample Azure CLI commands:

- Create a resource group.
- Create a CDN profile.
- Create a CDN endpoint.
- Create a CDN origin group.
- Create a CDN origin.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../includes/azure-cli-prepare-your-environment.md)]

## Sample script

The following Azure CLI script creates a resource group, CDN profile, and CDN endpoint:

```azurecli
# Create a resource group
az group create --name MyResourceGroup --location eastus

# Create a CDN profile
az cdn profile create --resource-group MyResourceGroup --name MyCDNProfile --sku Standard_Microsoft

# Create a CDN endpoint
az cdn endpoint create --resource-group MyResourceGroup --name MyCDNEndpoint --profile-name MyCDNProfile --origin www.contoso.com
```

The following Azure CLI script creates a CDN origin group, sets the default origin group for an endpoint, and creates a new origin:

```azurecli
# Create an origin group
az cdn origin-group create --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name MyOriginGroup --origins origin-0

# Make the origin group the default group of an endpoint
az cdn endpoint update --resource-group MyResourceGroup --name MyCDNEndpoint --profile-name MyCDNProfile --default-origin-group MyOriginGroup
                           
# Create another origin for an endpoint
az cdn origin create --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name origin-1 --host-name example.contoso.com
```

## Clean up resources

After you've finished running the script sample, use the following command to remove the resource group and all resources associated with it.

```azurecli
az group delete --name MyResourceGroup
```

## Azure CLI references used in this article

- [az cdn endpoint create](https://docs.microsoft.com/cli/azure/cdn/endpoint#az_cdn_endpoint_create)
- [az cdn endpoint update](https://docs.microsoft.com/cli/azure/cdn/endpoint#az_cdn_endpoint_update)
- [az cdn origin create](https://docs.microsoft.com/cli/azure/cdn/origin#az_cdn_origin_create)
- [az cdn origin-group create](https://docs.microsoft.com/cli/azure/cdn/origin-group#az_cdn_origin_group_create)
- [az cdn profile create](https://docs.microsoft.com/cli/azure/cdn/profile#az_cdn_profile_create)
- [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create)