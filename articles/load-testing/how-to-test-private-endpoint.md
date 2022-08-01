---
title: Load test private endpoints
titleSuffix: Azure Load Testing
description: Learn how to deploy Azure Load Testing in a virtual network (VNET injection) to test private application endpoints and hybrid deployments.
services: load-testing
ms.service: load-testing
ms.author: nicktrog
author: ntrogh
ms.date: 08/01/2022
ms.topic: how-to

---

# Test private endpoints by deploying Azure Load Testing in an Azure virtual network

In this article, learn how to test private application endpoints with Azure Load Testing Preview. You'll create an Azure Load Testing resource and enable it to attach load test engines in your virtual network (VNET injection).

This functionality enables the following usage scenarios:

- Generate load to a web service exposed to an Azure Virtual Network.
- Generate load to an Azure-hosted public endpoint with access restrictions, such as restricting client IP addresses.
- Generate load to an on-premises service, not publicly accessible, that is connected to Azure via ExpressRoute.

Learn more about the scenarios for [deploying Azure Load Testing in your virtual network](./concept-azure-load-testing-vnet-injection.md).

The following diagram provides a technical overview:

:::image type="content" source="media/how-to-test-private-endpoint/azure-load-testing-vnet-injection.png" alt-text="Diagram that shows the Azure Load Testing VNET injection technical overview.":::

> [!IMPORTANT]
> Azure Load Testing is currently in preview. For legal terms that apply to Azure features that are in beta, in preview, or otherwise not yet released into general availability, see the [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## Prerequisites

- An Azure subscription that is allow-listed for the VNET injection feature.
- An existing virtual network and subnet to use with your Azure Load Testing agents.
- The virtual network must be in the same subscription as the Azure Load Testing resource.
- The subnet used for the load testing must have enough unassigned IP addresses to accommodate the number of load test engines.
- If you plan to secure the virtual network by restricting traffic, see the [Required public internet access](#required-public-internet-access) section.
- The subnet shouldn't be delegated to any other Azure service. For example, it shouldn't be delegated to Azure Container Instances (ACI). Learn more about [subnet delegation](/azure/virtual-network/subnet-delegation-overview).

## Required public internet access

Azure Load Testing requires both inbound and outbound access to the public internet. The following tables provide an overview of what access is required and what it is for.

|Direction  |Ports  |Service tag/IP address  |Purpose  |
|---------|---------|---------|---------|
|Inbound     | 29876-29877  | BatchNodeManagement        | Create, update, and delete of Azure Load Testing compute instances. |
|Inbound     | 8080         | AzureLoadTestingInstanceManagement   | Create, update, and delete of Azure Load Testing compute instances. |
|Outbound     | *        | *        | Used for various operations involved in orchestrating a load tests |

## Configure your load test script

The test engines, which run the Apache JMeter script, are attached to the virtual network that contains the application endpoint. To load test the application endpoint, you can refer to it in the JMX file by using the private IP address or [name resolution in your network](/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances).

For example, for an endpoint in a virtual network with subnet range 10.179.0.0/18, the JMX file could have this information:

```xml
<HTTPSamplerProxy guiclass="HttpTestSampleGui" testclass="HTTPSamplerProxy" testname="Internal service homepage" enabled="true">
  <elementProp name="HTTPsampler.Arguments" elementType="Arguments" guiclass="HTTPArgumentsPanel" testclass="Arguments" testname="Service homepage" enabled="true">
    <collectionProp name="Arguments.arguments"/>
  </elementProp>
  <stringProp name="HTTPSampler.domain">10.179.0.7</stringProp>
  <stringProp name="HTTPSampler.port">8081</stringProp>
  <stringProp name="HTTPSampler.protocol"></stringProp>
  <stringProp name="HTTPSampler.contentEncoding"></stringProp>
  <stringProp name="HTTPSampler.path"></stringProp>
  <stringProp name="HTTPSampler.method">GET</stringProp>
</HTTPSamplerProxy>
```

## Deploy Azure Load Testing in your virtual network

You can attach load test engines to an Azure Virtual Network for a new load test. You use the Azure portal to add a new load test to an Azure Load Testing resource, and configure it for your virtual network in the creation wizard.

1. Sign in to the [Azure portal](https://portal.azure.com) by using the credentials for your Azure subscription.
    
1. Go to your Azure Load Testing resource, select **Tests** from the left pane, and then select **+ Create new test**.

    :::image type="content" source="media/how-to-test-private-endpoint/create-new-test.png" alt-text="Screenshot that shows the Azure Load Testing page and the button for creating a new test.":::

1. On the **Basics** tab, enter the **Test name** and **Test description** information. Optionally, you can select the **Run test after creation** checkbox.

    :::image type="content" source="media/how-to-test-private-endpoint/create-new-test-basics.png" alt-text="Screenshot that shows the 'Basics' tab for creating a test.":::

1. On the **Test plan** tab, select your Apache JMeter script, and then select **Upload** to upload the file to Azure.

    You can select and upload additional Apache JMeter configuration files or other files that are referenced in the JMX file. For example, if your test script uses CSV data sets, you can upload the corresponding *.csv* file(s).

1. On the **Load** tab, select **Private** traffic mode, and then select your virtual network and subnet.

    :::image type="content" source="media/how-to-test-private-endpoint/create-new-test-load-vnet.png" alt-text="Screenshot that shows the 'Load' tab for creating a test.":::

    > [!IMPORTANT]
    > When you deploy Azure Load Testing in a virtual network, you'll incur additional charges. Azure Load Testing deploys an [Azure Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/) and a [Public IP address](https://azure.microsoft.com/pricing/details/ip-addresses/) in your subscription and there might be a cost for generated traffic. For more information, see the [Virtual Network pricing information](https://azure.microsoft.com/pricing/details/virtual-network).
        
1. Select **Review + create**. Review all settings, and then select **Create** to create the load test.

While your load test runs, Azure Load Testing creates the following resources in the virtual network. These resources are ephemeral and exist only during the load test run.

- IP address
- Network Security Group (NSG)
- Azure Load Balancer

## Troubleshooting

### Starting the load test fails with `Test cannot be started`

To start a load test, you must have sufficient permissions to deploy Azure Load Testing to the virtual network. You require the [Network Contributor](/azure/role-based-access-control/built-in-roles#network-contributor) role, or a parent of this role, on the virtual network. See [Check access for a user to Azure resources](/azure/role-based-access-control/check-access) to verify your permissions.

If you're using the Azure Load Testing REST API to start a load test, check that you're using a valid subnet ID. The subnet must be in the same Azure region as your Azure Load Testing resource.

### The load test is stuck in `Provisioning` state and then goes to `Failed`

1. Verify that your subscription is registered with `Microsoft.Batch`.

    Run the following Azure CLI command to verify the status. The result should be `Registered`.

    ```azurecli
    az provider show --namespace Microsoft.Batch --query registrationState
    ```

1. Verify that Microsoft Batch node management and the Azure Load Testing IPs can make inbound connections to the test engine VMs.

    1. Enable [Network Watcher](/azure/network-watcher/network-watcher-monitoring-overview) for the virtual network region.

        ```azurecli
        az network watcher configure \
                  --resource-group NetworkWatcherRG \
                  --locations eastus \
                  --enabled
        ```

    1. Create a temporary VM  with a Public IP in the subnet you're using for the Azure Load Testing service. You'll only use this VM to diagnose the network connectivity and delete it afterwards. The VM can be of any type.

        ```azurecli
        az vm create \
              --resource-group myResourceGroup \
              --name myVm \
              --image UbuntuLTS \
              --generate-ssh-keys \
              --subnet mySubnet
        ```

    1. Test the inbound connectivity to the temporary VM from the `BatchNodeManagement` service tag.

        1. In the [Azure portal](https://portal.azure.com), go to **Network Watcher**.
        1. On the left pane, select **NSG Diagnostic**.
        1. Enter the details of the VM you created in the previous step.
        1. Select **Service Tag** for the **Source type**, and then select **BatchNodeManagement** for the **Service tag**.
        1. The **Destination IP address** is the IP address of the VM you created in previous step.
        1. For **Destination port**, you have to validate two ports: **29876** and **29877**. Enter one value at a time and move to the next step.
        1. Press **Check** to verify that the network security group isn't blocking traffic.

            :::image type="content" source="media/how-to-test-private-endpoint/test-nsg-connectivity.png" alt-text="Screenshot that shows the N S G Diagnotic page to test network connectivity.":::

            If the traffic status is **Denied**, you'll have to [modify the Network Security Group (NSG) rules](/azure/virtual-network/manage-network-security-group) to allow traffic for the **BatchNodeManagement** service tag.

    1. Test the inbound connectivity from the Azure Load Testing service.

        1. Stay on the **NSG Diagnostic** page.
        1. Enter the details of the VM you created in the previous step.
        1. Select **IPv4 address/CIDR** for the **Source type**.
        1. For **IPv4 address/CIDR**, enter the [IP address of the Azure Load Testing](#azure-load-testing-outbound-ip-addresses-per-region) service region you're using.
        1. For **Destination IP address**, enter the IP address of the VM you created in previous step.
        1. For **Destination port**, enter **8080**.
        1. Press **Check** to verify that the network security group isn't blocking traffic.

    1. Delete the temporary VM you created earlier.

### The test executes and results in a 100% error rate

Possible cause: there are connectivity issues between the subnet in which you deployed Azure Load Testing and the subnet in which the application endpoint is hosted.

1. You might deploy a temporary VM in the subnet used by Azure Load Testing and then use the [curl](https://curl.se/) tool to test connectivity to the application endpoint. Verify that there are no firewall or NSG rules that are blocking traffic.

1. Verify the [Azure Load Testing results file](./how-to-export-test-results.md) for error response messages:

    |Response message  | Action  |
    |---------|---------|
    | **Non http response code java.net.unknownhostexception** | Possible cause is a DNS resolution issue. If you’re using Azure Private DNS, verify that the DNS is set up correctly for the subnet in which Azure Load Testing instances are injected, and for the application subnet. |
    | **Non http response code SocketTimeout**     | Possible cause is when there’s a firewall blocking connections from the subnet in which Azure Load Testing instances are injected to your application subnet.  |

## Next steps

- Learn more about the [scenarios for deploying Azure Load Testing in a virtual network](./concept-azure-load-testing-vnet-injection.md).
- Learn how to [Monitor server-side application metrics](./how-to-monitor-server-side-metrics.md).
