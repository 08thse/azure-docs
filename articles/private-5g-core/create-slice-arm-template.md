---
title: Create a slice - ARM template
titleSuffix: Azure Private 5G Core Preview
description: This how-to guide shows how to create a slice in your private mobile network using an Azure Resource Manager (ARM) template. 
author: b-branco
ms.author: biancabranco
ms.service: private-5g-core
ms.topic: how-to
ms.date: 09/30/2022
ms.custom: template-how-to 
---

# Create a slice using an ARM template

*Network slices* allow you to multiplex independent logical networks on the same Azure Private 5G Core deployment. Slices are assigned to SIM policies and static IP addresses, providing isolated end-to-end networks that can be customized for different bandwidth and latency requirements.

In this how-to guide, you'll learn how to create a slice in your private mobile network using an Azure Resource Manager template (ARM template). You can configure a slice/service type (SST) and slice differentiator (SD) for slices associated with SIMs that will be provisioned on a 5G site. If a SIM is provisioned on a 4G site, the slice associated with its SIM policy must contain an empty SD and a value of 1 for the SST.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

If your environment meets the prerequisites and you're familiar with using ARM templates, select the **Deploy to Azure** button. The template will open in the Azure portal.

<!-- TODO: check link
[![Deploy to Azure.](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.mobilenetwork%2Fmobilenetwork-create-new-slice%2Fazuredeploy.json)
 -->
## Prerequisites

- Identify the name of the Mobile Network resource corresponding to your private mobile network.
- Collect the information in [Collect the required information for a network slice](collect-required-information-for-private-mobile-network.md#collect-the-required-information-for-a-network-slice). If the slice will be used by 4G UEs, you don't need to collect SST and SD values.
- Ensure you can sign in to the Azure portal using an account with access to the active subscription you used to create your private mobile network. This account must have the built-in Contributor or Owner role at the subscription scope.

## Review the template

The template used in this how-to guide is from [Azure Quickstart Templates](https://azure.microsoft.com/resources/templates/mobilenetwork-create-new-slice).
<!-- TODO: check link
:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.mobilenetwork/mobilenetwork-create-new-slice/azuredeploy.json":::
-->
The following Azure resource is defined in the template.

- [**Microsoft.MobileNetwork/mobileNetworks/slices**](/azure/templates/microsoft.mobilenetwork/mobilenetworks/slices): a resource representing a network slice.

## Deploy the template

1. Select the following link to sign in to Azure and open a template.
<!-- TODO: check link
    [![Deploy to Azure.](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.mobilenetwork%2Fmobilenetwork-create-new-slice%2Fazuredeploy.json)
 -->

1. Select or enter the following values, using the information you retrieved in [Prerequisites](#prerequisites).

    - **Existing Mobile Network Name:** enter the name of the Mobile Network resource representing your private mobile network.
    - **Slice name:** enter the name of the network slice.
    - **Slice Service Type:** enter the slice/service type (SST) value. If the slice will be used by 4G UEs, enter a value of 1.
    - **Slice Differentiator:** enter the slice differentiator (SD) value. If the slice will be used by 4G UEs, leave this field blank.

2. Select **Review + create**.
3. Azure will now validate the configuration values you've entered. You should see a message indicating that your values have passed validation.

    If the validation fails, you'll see an error message and the **Configuration** tab(s) containing the invalid configuration will be flagged. Select the flagged tab(s) and use the error messages to correct invalid configuration before returning to the **Review + create** tab.

4. Once your configuration has been validated, you can select **Create** to create the slice. The Azure portal will display a confirmation screen when the slice has been created.

## Review deployed resources

1. On the confirmation screen, select **Go to resource group**.

    :::image type="content" source="media/template-deployment-confirmation.png" alt-text="Screenshot of the Azure portal showing a deployment confirmation for the ARM template.":::

1. Confirm that the resource group contains a new **Slice** resource representing the network slice.

## Next steps

See [Policy control](policy-control.md) to learn more about designing the policy control configuration for your private mobile network.
