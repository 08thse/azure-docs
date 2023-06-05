---
title: Map DNS for applications in multiple Azure Spring Apps service instances in the same VNET
description: Learn how to Map DNS for applications in multiple Azure Spring Apps service instances in the same virtual network.
author: KarlErickson
ms.author: wenhaozhang
ms.service: spring-apps
ms.topic: how-to
ms.date: 6/5/2023
ms.custom: devx-track-java, devx-track-azurecli, event-tier1-build-2022, engagement-fy23
---

# Map DNS for applications in multiple Azure Spring Apps service instances in the same VNET

> [!NOTE]
> Azure Spring Apps is the new name for the Azure Spring Cloud service. Although the service has a new name, you'll see the old name in some places for a while as we work to update assets such as screenshots, videos, and diagrams.

**This article applies to:** ✔️ Basic/Standard ✔️ Enterprise

This article shows how to map DNS entries for applications to access multiple Azure Spring Apps service instances in the same VNET.

## Prerequisites

- An Azure subscription. If you don't have a subscription, create a [free account](https://azure.microsoft.com/free/) before you begin.
- [Azure CLI](/cli/azure/install-azure-cli) version 2.45.0 or higher. Use the following command to install the Azure Spring Apps extension: `az extension add --name spring`
- A virtual network deployed in an instance of Azure Spring Apps. For more information, see [Deploy Azure Spring Apps in your Azure virtual network (VNet injection)](./how-to-deploy-in-azure-virtual-network.md).

## Overview

This article describes two approaches for mapping DNS:

- Use the Microsoft provided fully qualified domain name (FQDN).
  Using the Microsoft provided fully qualified domain name (FQDN) is relatively simple and lightweight way to map DNS method compared to the custom domain approach. This approach is recommended if you don't need a wildcard approach in your DNS zone.

  This approach requires a DNS record for each application.

- Use the custom domain.
  If you already have a custom domain or you want a wildcard approach to work in a multi instance scenario, use this approach.

  This approach requires a DNS record for each Azure Spring Apps service instance, and a custom domain configured for each application.

As an example, this article uses `azure-spring-apps-1` and `azure-spring-apps-2` as the names for two instances of Azure Spring Apps in the same VNET.

Begin with the [Preliminary steps for DNS mapping](#preliminary-steps-for-dns-mapping) and then proceed with your preferred approach:

- [DNS mapping with Microsoft provided FQDN](#dns-mapping-with-microsoft-provided-fqdn)
- [DNS mapping with a custom domain](#dns-mapping-with-a-custom-domain)

Then test the mapping as described in the [Access private FQDN URLs for private applications](#access-private-fqdn-urls-for-private-applications) section.

## Preliminary steps for DNS mapping

Complete the steps in this section for both the FDQN and the custom domain approaches to mapping DNS.

### Find the IP for your applications

#### [Azure portal](#tab/azure-portal)

1. Select the virtual network you created for an Azure Spring Apps instance, and then select **Connected devices** in the navigation pane.

1. On the **Connected devices** page, search for *kubernetes-internal*.

1. In the results, find the **Device** connected to the service runtime **Subnet** of those service instance, and copy its **IP Address**. In the following screenshot example, the IP Address of azure-spring-apps-1 is *10.1.0.6*, and the IP Address of azure-spring-apps-2 is *10.1.2.6*.

   :::image type="content" source="media/how-to-map-dns-virtual-network/create-dns-record.png" alt-text="Create DNS record" lightbox="media/how-to-map-dns-virtual-network/create-dns-record.png":::

#### [Azure CLI](#tab/azure-CLI)

1. Define variables for your subscription, resource group, and Azure Spring Apps instance. Customize the values based on your real environment.

   ```azurecli
   SUBSCRIPTION='<subscription-id>'
   RESOURCE_GROUP='<resource group name>'
   VIRTUAL_NETWORK_NAME='<Azure-Spring-Apps-VNET-name>'
   ```

1. Find the IP Address for each of your Azure Spring Apps service instances. Customize the value of your Azure Spring Apps instance name based on your environment.

   ```azurecli
   AZURE_SPRING_APPS_NAME='<Azure-Spring-Apps-instance-name>'
   SERVICE_RUNTIME_RG=`az spring show \
       --resource-group $RESOURCE_GROUP \
       --name $AZURE_SPRING_APPS_NAME \
       --query "properties.networkProfile.serviceRuntimeNetworkResourceGroup" \
       --output tsv`
   IP_ADDRESS=`az network lb frontend-ip list \
       --lb-name kubernetes-internal \
       --resource-group $SERVICE_RUNTIME_RG \
       --query "[0].privateIpAddress" \
       --output tsv`
   ```

---

### Create a private DNS zone

Create a private DNS zone for an application in the private network.

> [!NOTE]
> If you are using Azure China, please replace `private.azuremicroservices.io` with `private.microservices.azure.cn` in this article. Learn more about [Check Endpoints in Azure](/azure/china/resources-developer-guide#check-endpoints-in-azure).

#### [Azure portal](#tab/azure-portal)

1. In the top search box, search for **Private DNS zones**.

1. On the **Private DNS zones** page, select **Create**.

1. Fill out the form on the **Create Private DNS zone** page. For the instance **Name** enter *private.azuremicroservices.io* as the **Name** for of the zone.

1. Select **Review create**.

1. Select **Create**.

#### [Azure CLI](#tab/azure-CLI)

1. Use the following commands to sign in to the Azure CLI and set your active subscription:

   ```azurecli
   az login
   az account set --subscription ${SUBSCRIPTION}
   ```

1. Create the private DNS zone.

   ```azurecli
   az network private-dns zone create \
       --resource-group $RESOURCE_GROUP \
       --name private.azuremicroservices.io
   ```

---

It may take a few minutes to create the zone.

### Link the virtual network

You must create a virtual network link to link the private DNS zone to the virtual network.

#### [Azure portal](#tab/azure-portal)

1. Select the private DNS zone resource you created earlier: `private.azuremicroservices.io`.

1. On the navigation pane, select **Virtual network links**, then select **Add**.

1. For the **Link name**, enter *azure-spring-apps-dns-link*.

1. For **Virtual network**, select the virtual network you created for [Prerequisites](#prerequisites).

   :::image type="content" source="media/how-to-map-dns-virtual-network/add-virtual-network-link.png" alt-text="Add virtual network link" lightbox="media/how-to-map-dns-virtual-network/add-virtual-network-link.png":::

1. Select **OK**.

#### [Azure CLI](#tab/azure-CLI)

Use the following command to link the private DNS zone you created to the virtual network that holds your Azure Spring Apps services.

   ```azurecli
   az network private-dns link vnet create \
       --resource-group $RESOURCE_GROUP \
       --name azure-spring-apps-dns-link \
       --zone-name private.azuremicroservices.io \
       --virtual-network $VIRTUAL_NETWORK_NAME \
       --registration-enabled false
   ```

---

### Assign private FQDN for your applications

You can assign a private FQDN for your application. For more information, see [Deploy Azure Spring Apps in a virtual network](./how-to-deploy-in-azure-virtual-network.md).

#### [Azure portal](#tab/azure-portal)

1. Open the Azure Spring Apps instance deployed in your virtual network, and then select **Apps** in the navigation pane.

1. Select the application to show the **Overview** page.

1. Select **Assign Endpoint** to assign a private FQDN to your application. Assigning an FQDN can take a few minutes.

   :::image type="content" source="media/how-to-map-dns-virtual-network/assign-private-endpoint.png" alt-text="Assign private endpoint" lightbox="media/how-to-map-dns-virtual-network/assign-private-endpoint.png":::

#### [Azure CLI](#tab/azure-CLI)

Use the following command to update your app with an assigned endpoint. Customize the value of your app name based on your real environment.

```azurecli
SPRING_CLOUD_APP='<app-name>'
az spring app update \
    --resource-group $RESOURCE_GROUP \
    --name $SPRING_CLOUD_APP \
    --service $SPRING_CLOUD_NAME \
    --assign-endpoint true
```

---

## DNS mapping with Microsoft provided FQDN

Using the Microsoft provided fully qualified domain name (FQDN) requires you to add DNS record for each application. For a core understanding of this process, see [Access your application in a private network](access-app-virtual-network.md).

When an application in an Azure Spring Apps service instance with assigned endpoint is deployed in the virtual network, the endpoint is a private FQDN. By default, the fully qualified domain name is unique for each app across service instances. The FQDN format is:  `<service\-name>-<app\-name>.private.azuremicroservices.io`.

In this approach, we need to create a DNS record for each application.

### Create DNS record for all the applications

To use the private DNS zone to translate and resolve DNS, you must create an "A" type record in the zone for each of your applications. In this example, the app name is `hello-vnet` and the Azure Springs Apps service instance name is `azure-spring-apps-1`.

You'll need the IP address for each application. Copy it as described in the [Find the IP for your application](access-app-virtual-network.md#find-the-ip-for-your-application) section of [Access your application in a private network](access-app-virtual-network.md). In this example, the IP address is `10.1.0.6`.

#### [Azure portal](#tab/azure-portal)

1. Select the private DNS zone you created earlier: `private.azuremicroservices.io`

1. Select **Record set**.

1. In the **Add record set** pane, enter the values from the following table.

| Setting    | Value                            |
|------------|----------------------------------|
| Name       | *azure-spring-apps-1-hello-vnet* |
| Type       | **A**                            |
| TTL        | 1                                |
| TTL unit   | **Hours**                        |
| IP address | (paste from copy)                |

1. Select **OK**.

   :::image type="content" source="media/how-to-map-dns-virtual-network/private-dns-zone-add-record-fqdn.png" alt-text="Add private DNS zone record FQDN" lightbox="media/how-to-map-dns-virtual-network/private-dns-zone-add-record-fqdn.png":::

#### [Azure CLI](#tab/azure-CLI)

Use the following command to create a DNS record.

   ```azurecli
   az network private-dns record-set a add-record \
     --resource-group $RESOURCE_GROUP \
     --zone-name private.azuremicroservices.io \
     --record-set-name 'azure-spring-apps-1-hello-vnet' \
     --ipv4-address $IP_ADDRESS
   ```

---

Repeat these steps to add a DSN record for each application.

## DNS mapping with a custom domain

With the custom domain approach, you only need to add DNS record for each Azure Spring Apps instance but you must configure the custom domain for each application. For a core understanding of this process, see [Tutorial: Map an existing custom domain to Azure Spring Apps](tutorial-custom-domain.md).

This example reuses the private DNS zone `private.azuremicroservices.io` to add a custom domain related DNS record. The private FQDN has the format `<app\-name>.<service\-name>.private.azuremicroservices.io`. 

Technically, you can use any private fully qualified domain name you want. In that case, you have to create a new private DNS zone corresponding to the fully qualified domain name you choose.

### Map your custom domain to an app in an Azure Spring Apps instance

You must map your custom domain to each of the applications in the Azure Spring Apps instance.

#### [Azure portal](#tab/Azure-portal)

1. Open the Azure Spring Apps instance and select **Apps** in the navigation pane.
1. On the **Apps** page, select an application.
1. Select **Custom domain** in the navigation pane.
1. Select **Add Custom domain**.
1. On the **Add custom domain** pane, enter the FQDN you want to use and make sure it's corresponding to the certificate to use for SSL binding later. This example uses `hello-vnet.azure-spring-apps-1.private.azuremicroservices.io`. You can disregard the CNAME part.
1. Select **Validate**.
1. If validated, select **Add**.

   :::image type="content" source="./media/how-to-map-dns-virtual-network/add-custom-domain.png" alt-text="Add custom domain" lightbox="./media/how-to-map-dns-virtual-network/add-custom-domain.png":::

When the custom domain is successfully mapped to the app, it appears in the custom domain table.

   :::image type="content" source="./media/how-to-map-dns-virtual-network/custom-domain-table.png" alt-text="Custom domain table" lightbox="./media/how-to-map-dns-virtual-network/custom-domain-table.png":::

#### [Azure CLI](#tab/Azure-CLI)

1. Use the following command to bind a custom domain to an app:
  
   ```azurecli
   az spring app custom-domain bind \
       --resource-group <resource-group-name> \
       --service <Azure-Spring-Apps-instance-name> \
       --domain-name <fqdn-domain-name> \
       --app <app-name>
   ```

1. Use the following command to list all custom domains of an app:

   ```azurecli
   az spring app custom-domain list \
       --resource-group <resource-group-name> \
       --service <Azure-Spring-Apps-instance-name> \
       --app <app name> \
   ```

---

> [!NOTE]
> A **Not Secure** label for your custom domain means that it's not yet bound to an SSL certificate. Any HTTPS request from a browser to your custom domain will receive an error or warning.

### Add SSL binding

Before doing this step, please make sure you have prepared your certificates and import them into Azure Spring Apps. For more information, see [Tutorial: Map an existing custom domain to Azure Spring Apps](tutorial-custom-domain.md).

#### [Azure portal](#tab/Azure-portal)

1. Open the Azure Spring Apps instance and select **Apps** in the navigation pane.
1. On the **Apps** page, select an application.
1. Select **Custom domain** in the navigation pane.
1. Select **TLS/SSL binding**.
1. On the **TLS/SSL binding pane**, select **Certificate** and select the certificate or you can import the certificate.
1. Select **Save**.

   :::image type="content" source="./media/how-to-map-dns-virtual-network/add-ssl-binding.png" alt-text="Add SSL binding 1" lightbox="./media/how-to-map-dns-virtual-network/add-ssl-binding.png":::

After you successfully add SSL binding, the domain state will be secure: **Healthy**.

:::image type="content" source="./media/how-to-map-dns-virtual-network/secured-domain-state.png" alt-text="Add SSL binding 2" lightbox="./media/how-to-map-dns-virtual-network/secured-domain-state.png":::

#### [Azure CLI](#tab/Azure-CLI)

Use the following command to update a custom domain of an app.

```azurecli
az spring app custom-domain update \
    --resource-group <resource-group-name> \
    --service <Azure-Spring-Apps-instance-name> \
    --domain-name <domain name> \
    --certificate <cert name> \
    --app <app name> 
```

---

### Configure the custom domain for all applications

To use the private DNS zone to translate and resolve DNS, you must create an "A" type record in the zone for each of your Azure Spring Apps service instances. In this example, the app name is `hello-vnet` and the Azure Springs Apps service instance name is `azure-spring-apps-1`.

You'll need the IP address for each application. Copy it as described in the [Find the IP for your application](access-app-virtual-network.md#find-the-ip-for-your-application) section of [Access your application in a private network](access-app-virtual-network.md). In this example, the IP address is `10.1.0.6`.

#### [Azure portal](#tab/azure-portal)

1. Select the private DNS zone you created earlier: `private.azuremicroservices.io`

1. Select **Record set**.

1. In the **Add record set** pane, enter the values from the following table.

| Setting    | Value                            |
|------------|----------------------------------|
| Name       | *azure-spring-apps-1-hello-vnet* |
| Type       | **A**                            |
| TTL        | 1                                |
| TTL unit   | **Hours**                        |
| IP address | (paste from copy)                |

1. Select **OK**.

   :::image type="content" source="media/how-to-map-dns-virtual-network/private-dns-zone-add-record-custom-domain.png" alt-text="Add private DNS zone record custom domain" lightbox="media/how-to-map-dns-virtual-network/private-dns-zone-add-record-custom-domain.png":::

#### [Azure CLI](#tab/azure-CLI)

Use the [IP address](#find-the-ip-for-your-applications) to create the A record in your DNS zone.

   ```azurecli
   az network private-dns record-set a add-record \
     --resource-group $RESOURCE_GROUP \
     --zone-name private.azuremicroservices.io \
     --record-set-name '*.azure-spring-apps-1' \
     --ipv4-address $IP_ADDRESS
   ```

---

## Access private FQDN for applications

After the FQDN assignments and DNS mappings for both approaches, you can access all the applications' private FQDN in the private network. For example, you can create a jumpbox or virtual machine in the same virtual network or in a peered virtual network and have access to all the private FQDN of the applications.

Examples for the FQDN approach:

- `https://hello-vnet.azure-spring-apps-1.private.azuremicroservices.io`
- `https://hello-vnet.azure-spring-apps-2.private.azuremicroservices.io`

Examples for the Custom domain approach:

- `https://azure-spring-apps-1-hello-vnet.private.azuremicroservices.io`
- `https://azure-spring-apps-2-hello-vnet.private.azuremicroservices.io`

:::image type="content" source="media/how-to-map-dns-virtual-network/access-private-endpoint-1-fqdn.png" alt-text="Access private endpoint in vnet FQDN 1" lightbox="media/how-to-map-dns-virtual-network/access-private-endpoint-1-fqdn.png":::

## Clean up resources

If you plan to continue working with subsequent articles, you might want to leave these resources in place. When no longer needed, delete the resource group, which deletes the resources in the resource group. To delete the resource group by using Azure CLI, use the following command:

```azurecli
az group delete --name $RESOURCE_GROUP
```

## Next steps

- [Deploy Azure Spring Apps in your Azure virtual network (VNet injection)](./how-to-deploy-in-azure-virtual-network.md)
- [Access your application in a private network](access-app-virtual-network.md)
