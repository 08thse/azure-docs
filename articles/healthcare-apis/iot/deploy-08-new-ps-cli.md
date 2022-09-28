---
title: Using Azure PowerShell and Azure CLI to deploy the MedTech service using Azure Resource Manager templates - Azure Health Data Services
description: In this article, you'll learn how to use Azure PowerShell and Azure CLI to deploy the MedTech service using an Azure Resource Manager template.
author: mcevoy-building7
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 09/19/2022
ms.author: v-smcevoy
---

# Using Azure PowerShell and Azure CLI to deploy the MedTech service using Azure Resource Manager templates

In this quickstart, you'll learn how to use Azure PowerShell and Azure CLI to deploy the MedTech service using an Azure Resource Manager (ARM) template. For more information about ARM templates, see [What are ARM templates?](./../../azure-resource-manager/templates/overview.md).

## Location of MedTech service ARM template

The ARM template used in this MedTech service quickstart is from the [Azure Quickstart Templates](https://azure.microsoft.com/resources/templates/iotconnectors/) site using the **azuredeploy.json** file located on [GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.healthcareapis/workspaces/iotconnectors/azuredeploy.json).

## Regions where the MedTech service is available

See the [Products available by region](https://azure.microsoft.com/global-infrastructure/services/?products=health-data-services) site for the current Azure regions where the Azure Health Data Services is supported.

You can also review the **location** section of the **azuredeploy.json** file on [GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.healthcareapis/workspaces/iotconnectors/azuredeploy.json) for Azure regions where the Azure Health Data Services is publicly available.

## Resources created by the ARM template

The ARM template used in this quickstart configures and deploys the following resources for Azure and Azure Health Data Services:

- Azure Event Hubs namespace and device message event hub (the device message event hub is named: **devicedata**).
- Azure event hub consumer group (the consumer group is named: **$Default**).
- Azure event hub sender role (the sender role is named: **devicedatasender**).
- Azure Health Data Services workspace.
- Azure Health Data Services Fast Healthcare Interoperability Resources (FHIR&#174;) service.
- Azure Health Data Services MedTech service. This includes setup for:
  - the necessary [system-assigned managed identity](../../active-directory/managed-identities-azure-resources/overview.md) access roles needed to read from the device message event hub (the device message event hub is named: **Azure Events Hubs Receiver**)
  - and necessary system-assigned managed identity access roles needed to read and write to the FHIR service (the FHIR service is named: **FHIR Data Writer**).
- An output file with the ARM template deployment results (this file is named **medtech_service_ARM_template_deployment_results.txt** and is located in the directory from which you ran the scripts).

## Azure PowerShell prerequisites

Fulfill these prerequisites for Azure PowerShell:

- Have an Azure account with an active subscription. [If you don't have an Azure subscription, see [Subscription decision guide](/azure/cloud-adoption-framework/decision-guides/subscriptions/).

- If you want to run the code locally, use [Azure PowerShell](/powershell/azure/install-az-ps).

## Azure CLI prerequisites

Fulfill these prerequisites for Azure CLI:

- Have an Azure account with an active subscription. If you don't have an Azure subscription, see [Subscription decision guide](/azure/cloud-adoption-framework/decision-guides/subscriptions/).

- If you want to run the code locally:

  - Use a Bash shell (such as Git Bash, which is included in [Git for Windows](https://gitforwindows.org).

  - Use [Azure CLI](/cli/azure/install-azure-cli).

## Deploy MedTech service with the ARM template and Azure PowerShell

Complete the following five steps to deploy the MedTech service using Azure PowerShell:

1. First you need to confirm the region you want to deploy in:

    If you need a list of the Azure regions location names, you can use this code to display a list.

   ```azurepowershell
   Get-AzLocation | Format-Table -Property DisplayName,Location
   ```

2. If the `Microsoft.EventHub` resource provider isn't already registered with your subscription, you can use this code to register it.

   ```azurepowershell
   Register-AzResourceProvider -ProviderNamespace Microsoft.EventHub
   ```

3. If the `Microsoft.HealthcareApis` resource provider isn't already registered with you subscription, you can use this code to register it.

   ```azurepowershell
   Register-AzResourceProvider -ProviderNamespace Microsoft.HealthcareApis
   ```

4. If you don't already have a resource group created for this quickstart, you can use this code to create one.

   ```azurepowershell
   New-AzResourceGroup -name <ResourceGroupName> -location <AzureRegion>
   ```

   For example: `New-AzResourceGroup -name ArmTestDeployment -location southcentralus`

   > [!IMPORTANT]
   >
   > For a successful deployment of the MedTech service, you'll need to use numbers and lowercase letters for the basename of your resources. The minimum basename requirement is three characters with a maximum of 16 characters.

5. Use the following code to deploy the MedTech service using the ARM template.

   ```azurepowershell
   New-AzResourceGroupDeployment -ResourceGroupName <ResourceGroupName> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.healthcareapis/workspaces/iotconnectors/azuredeploy.json -basename <BaseName> -location <AzureRegion> | Out-File medtech_service_ARM_template_deployment_results.txt
   ```

   For example: `New-AzResourceGroupDeployment -ResourceGroupName ArmTestDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.healthcareapis/workspaces/iotconnectors/azuredeploy.json -basename abc123 -location southcentralus | Out-File medtech_service_ARM_template_deployment_results.txt`

> [!NOTE]
> If you want to run the Azure PowerShell commands locally, first enter `Connect-AzAccount` into your PowerShell command-line shell and enter your Azure credentials.

## Deploy MedTech service with the ARM template and Azure CLI

Complete the following five steps to deploy the MedTech service using Azure CLI:

1. First you need to confirm the region you want to deploy in:

    If you need a list of the Azure regions location names, you can use this code to display a list.

   ```azurecli
   az account list-locations -o table
   ```

2. If the `Microsoft.EventHub` resource provider isn't already registered with your subscription, you can use this code to register it.

   ```azurecli
   az provider register --name Microsoft.EventHub
   ```

3. If the `Microsoft.HealthcareApis` resource provider isn't already registered with your subscription, you can use this code to register it.

   ```azurecli
   az provider register --name Microsoft.HealthcareApis
   ```

4. If you don't already have a resource group created for this quickstart, you can use this code to create one.

   ```azurecli
   az group create --resource-group <ResourceGroupName> --location <AzureRegion>
   ```

   For example: `az group create --resource-group ArmTestDeployment --location southcentralus`

   > [!IMPORTANT]
   >
   > For a successful deployment of the MedTech service, you'll need to use numbers and lowercase letters for the basename of your resources.

5. Use the following code to deploy the MedTech service using the ARM template.

   ```azurecli
   az deployment group create --resource-group <ResourceGroupName> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.healthcareapis/workspaces/iotconnectors/azuredeploy.json --parameters basename=<YourBaseName> location=<AzureRegion | Out-File medtech_service_ARM_template_deployment_results.txt
   ```

   For example: `az deployment group create --resource-group ArmTestDeployment --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.healthcareapis/workspaces/iotconnectors/azuredeploy.json --parameters basename=abc123 location=southcentralus | Out-File medtech_service_ARM_template_deployment_results.txt`

> [!NOTE]
> If you want to run the Azure CLI scripts commands locally, first enter `az login` into your PowerShell command-line shell and enter your Azure credentials.

## Deployment completion

The deployment takes a few minutes to complete.

When completed, you can view the **medtech_service_ARM_template_deployment_results.txt** file for the results of the ARM template
deployment.

## Post-deployment mapping

After a successful deployment of the MedTech service, you'll still have to provide a valid device and FHIR destination mapping.

To learn more about the device mapping, see [How to use device mappings](how-to-use-device-mappings.md).

To learn more about the FHIR destination mapping, see [How to use the FHIR destination mappings](how-to-use-fhir-mappings.md).

## Clean up Azure PowerShell resources

When your resource group and deployed ARM template resources are no longer needed, delete the resource group, which deletes the resources in the resource group.

```azurepowershell
Remove-AzResourceGroup -Name <ResourceGroupName>
 ```

For example: `Remove-AzResourceGroup -Name ArmTestDeployment`

## Clean up Azure CLI resources

When your resource group and deployed ARM template resources are no longer needed, delete the resource group, which deletes the resources in the resource group.

```azurecli
az group delete --name <ResourceGroupName>
```

For example: `az group delete --resource-group ArmTestDeployment`

> [!TIP]
>
> For a step-by-step tutorial that guides you through the process of creating an ARM template, see [Tutorial: Create and deploy your first ARM template](../../azure-resource-manager/templates/template-tutorial-create-first-template.md)

## Next steps

In this article, you learned how to use Azure PowerShell and Azure CLI to deploy the MedTech service using an Azure Resource Manager (ARM) template. To learn more about other methods of deployment, see

>[!div class="nextstepaction"]
>[Choosing a method of deployment for MedTech service in Azure](deploy-iot-connector-in-azure.md)

FHIR&#174; is a registered trademark of Health Level Seven International, registered in the U.S. Trademark Office and is used with their permission.
