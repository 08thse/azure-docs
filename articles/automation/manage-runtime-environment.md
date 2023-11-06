---
title: Manage runbooks and runtime environment in Azure Automation Runtime environment
description: This article tells how to manage runbooks in Azure Automation Runtime environment.
services: automation
ms.subservice: process-automation
ms.date: 11/06/2023
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
---


# Manage runbooks in Azure Automation

You can test the following scenarios through the Azure portal and REST API

## Prerequisites

1. Sign in to the Azure [portal](https://portal.azure.com).
1. [Create](quickstarts/create-azure-automation-account-portal.md) a new Automation account in the region of your choice (except Central India, Germany North, Italy North, Israel Central, Poland Central, UAE Central). Alternatively, you can use an existing Automation account to test the scenarios.

> [!NOTE]
> Ensure that you are using a non-production Automation accounts to test this feature.

### Create runtime environment

#### [Azure portal](#tab/create-runtime-portal)
1. Sign in to the Azure [portal](https://portal.azure.com) and select your Automation account.
1. Under **Process Automation**, select **Runtime Environments (preview)** and then select **Create**.
1. On **Basics**, provide the following details:
    1. **Name** for the Runtime environment. It must begin with alphabet and can contain only alphabets, numbers, underscores, and dashes.  
    1. From the **Language** drop-down, select the scripting language for Runtime environment.
    1. Choose the **PowerShell** for PowerShell scripting language or **Python** for Python scripting language.
    1. Select **Runtime version** for scripting language.
        - For PowerShell - choose 5.1, 7.1(preview), 7.2(preview)
        - For Python - choose 2.7, 3.8, 3.10 (preview)
    1. Provide the **Description**.
    
       :::image type="content" source="./media/manage-runtime-environment/create-runtime-environment.png" alt-text="Screenshot shows the entries in basics tab of create runtime environment.":::

1. Select **Next** and in the **Packages** tab, you can upload the packages required during the runbook execution. The *Az PowerShell package* is uploaded by default for all PowerShell Runtime environments, which includes all cmdlets for managing Azure resources. You can choose the version of Az package from the dropdown.

   :::image type="content" source="./media/manage-runtime-environment/packages-runtime-environment.png" alt-text="Screenshot shows the selections in packages tab of create runtime environment.":::

    1. To upload more Packages required during runbook execution. Select **Add a file** to add the file(s) stored locally on your computer or select **Add from gallery** to upload packages from PowerShell gallery.
      
       :::image type="content" source="./media/manage-runtime-environment/packages-add-files-runtime-environment.png" alt-text="Screenshot shows how to add files from local computer or upload from gallery." lightbox="./media/manage-runtime-environment/packages-add-files-runtime-environment.png":::
        
  > [!NOTE]
  > - When you import a package, it might take several minutes. 100MB is the maximum total size of the files that you can import.
  > - Use *.zip* files for PowerShell runbook types. 
    > - For Python 2.7 packages, use .tar.gz or .whl files targeting cp24-amd64.  
    > - For Python 3.8 packages, use .tar.gz or .whl files targeting cp38-amd64.  
    > - For Python 3.10 (preview) packages, use .whl files targeting cp310 Linux OS.
  
1. Select **Next** and in the **Review + Create** tab, verify that the settings are correct. When you select **Create**, Azure runs validation on Runtime environment settings that you have chosen. If the validation passes, you can proceed to create Runtime environment else, the portal indicates the settings that you need to modify.

In the **Runtime Environments (preview)** page, you can view all Runtime environments for your Automation account. If you don't find the newly created Runtime environments in the list, select **Refresh**.

#### [REST API](#tab/create-runtime-rest)

You can create a new Runtime environment for PowerShell 7.2 with Az PowerShell module in the Automation
```rest
PUT 
https://management.azure.com/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Automation/automationAccounts/<accountName>/runtimeEnvironments/<runtimeEnvironmentName>?api-version=2023-05-15-preview 

{ 
  "properties": { 
    "runtime": { 
        "language": "PowerShell",  
        "version": "7.2" 
        }, 
        "defaultPackages": { 
            "Az": "7.3.0" 
        } 
     }, 
    "name": "<runtimeEnvironmentName>" 
} 
```
Upload a package Az.Accounts to the Runtime environment.  

```rest
PUT 
https://management.azure.com/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Automation/automationAccounts/<accountName>/runtimeEnvironments/<runtimeEnvironmentName>/packages/Az.Accounts?api-version=2023-05-15-preview 
{ 
  "properties": { 
    "contentLink": { 
        "uri": "https://psg-prod-eastus.azureedge.net/packages/az.accounts.2.12.4.nupkg" 
    } 
  } 
} 
```
---

### View Runtime environment

Get the Runtime environment properties from the Automation account.
```rest
GET 
https://management.azure.com/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Automation/automationAccounts/<accountName>/runtimeEnvironments/<runtimeEnvironmentName>?api-version=2023-05-15-preview 
```

### List runtime environments

To list all the Runtime environments from the Automation account:

#### [Azure portal](#tab/list-runtime-portal)

1. In your Automation account, under **Process Automation**, select **Runtime Environments (preview)**.

   :::image type="content" source="./media/manage-runtime-environment/list-runtime-environment.png" alt-text="Screenshot shows how view the list of all runtime environments." lightbox="./media/manage-runtime-environment/list-runtime-environment.png":::

#### [REST API](#tab/list-runtime-rest) 

```rest
GET 
https://management.azure.com/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Automation/automationAccounts/<accountName>/runtimeEnvironments?api-version=2023-05-15-preview 
```
---

### Delete runtime environment

To delete the Runtime environment from the Automation account, follow these steps:

#### [Azure portal](#tab/delete-runtime-portal)

1. In your Automation account, under **Process Automation**, select **Runtime Environments (preview)**.
1. Select the runtime environment that you want to delete.
1. Select **Delete** to delete the Runtime environment.

   :::image type="content" source="./media/manage-runtime-environment/delete-runtime-environment.png" alt-text="Screenshot shows how to delete the runtime environment." lightbox="./media/manage-runtime-environment/delete-runtime-environment.png":::

#### [REST API](#tab/delete-runtime-rest)

```rest
DELETE  
https://management.azure.com/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Automation/automationAccounts/<accountName>/runtimeEnvironments/<runtimeEnvironmentName>?api-version=2023-05-15-preview 
```
---

### Update runtime environment

Runtime language and Runtime version are immutable properties. However, you can update the version of modules and add or remove packages in the Runtime environment. Runbooks linked to the Runtime environment will automatically get updated with the new settings.

#### [Azure portal](#tab/update-runtime-portal)

1. In your Automation account, under **Process Automation**, select **Runtime Environments (preview)**.
1. Select the Runtime environment that you want to update.
1. Select the version from dropdown to update the version of existing packages.
1. Select **Save**.
   
   :::image type="content" source="./media/manage-runtime-environment/update-runtime-environment.png" alt-text="Screenshot shows how to update the runtime environment." lightbox="./media/manage-runtime-environment/update-runtime-environment.png":::

1. Select **Add a file** to upload packages from your local computer or **Add from gallery** to upload packages from PowerShell Gallery.

   :::image type="content" source="./media/manage-runtime-environment/add-packages-gallery.png" alt-text="Screenshot shows how to upload packages while updating the runtime environment." lightbox="./media/manage-runtime-environment/add-packages-gallery.png":::

   > [!NOTE]
   > You can add up to 10 packages at a time to Runtime environment. Ensure that you **Save** after adding 10 packages.

#### [REST API](#tab/update-runtime-rest)

Update the Az module version of an existing Runtime environment: 

```rest
PATCH 
https://management.azure.com/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Automation/automationAccounts/<accountName>/runtimeEnvironments/<runtimeEnvironmentName>?api-version=2023-05-15-preview 
{ 

  "properties": { 

        "defaultPackages": { 

            "Az": "9.0.0" 

        } 

     } 

} 
```
---


### Create Runbook

You can create a new PowerShell runbook configured with Runtime environment.

#### [Azure portal](#tab/create-runbook-portal)

**Prerequisite**
- Ensure that you have created a Runtime environment before creating a runbook.

To create a new runbook linked to the Runtime environment, follow these steps:

1. In your Automation account, under **Process Automation**, select **Runbooks**.
1. Select **Create**.
1. In the **Basics** tab, you can either create a new runbook or upload a file from your local computer or from PowerShell gallery.
   1. Provide a **Name** for the runbook. It must begin with a letter and can contain only letters, numbers, underscores and dashes.
   1. From the **Runbook type** dropdown, select the type of runbook that you want to create.
   1. Select **Runtime environment** to be configured for the runbook. You can either *Select from existing* Runtime environments or *Create new* Runtime environment and link it to the Runbook. The list of runtime environments is populated on the basis of the *Runbook type* selected in step b.
   1. Provide the **Description**.
  
      :::image type="content" source="./media/manage-runtime-environment/create-runbook.png" alt-text="Screenshot shows how to create runbook in runtime environment." lightbox="./media/manage-runtime-environment/create-runbook.png":::

   > [!NOTE]
   > For **PowerShell Runbook Type**, only the PowerShell Runtime environment would be listed for selection.
   > For **Python Runbook Type**, only the Python Runtime environments would be listed for selection.
 
1. Add **Tags** to the runbook, review the settings and select **Create** to create a new runbook.

This runbook is linked to the selected Runtime environment. All the packages in the chosen Runtime environment would be available during execution of the runbook.

#### [REST API](#tab/create-runbook-rest)

**Prerequisite**
- Configure a PowerShell Runtime environment and provide it as an input for runbook creation. 

```rest
PUT 
https://management.azure.com/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Automation/automationAccounts/<accountName>/runbooks/<runbookName>?api-version=2023-05-15-preview 

{ 
  "properties": { 
        "runbookType": "PowerShell", 
        "runtimeEnvironment": <runtimeEnvironmentName>, 
        "publishContentLink": { 
            "uri": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-automation-runbook-getvms/Runbooks/Get-AzureVMTutorial.ps1" 
        } 
    }, 
   “location”: “East US” 
} 
```
> [!NOTE]
> Similar APIs also work for Python runbook type.
---

### Update Runbook

You can update runbook by changing the Runtime environment linked to the runbook. You can choose single or multiple runbooks for update.

#### [Azure portal](#tab/update-runbook-portal)
1. In your Automation account, under **Process Automation**, select **Runbooks**.
1. Select the checkbox for the runbook(s) that you want to update.
1. Select **Update**.
   :::image type="content" source="./media/manage-runtime-environment/update-runbook.png" alt-text="Screenshot shows how to update runbook in runtime environment." lightbox="./media/manage-runtime-environment/update-runbook.png":::
1. Select the Runtime environment from the dropdown to which you want to link hte runbook(s).
   :::image type="content" source="./media/manage-runtime-environment/update-runbook-runtime-environment.png" alt-text="Screenshot shows how to link runbook in runtime environment." lightbox="./media/manage-runtime-environment/update-runbook-runtime-environment.png":::
1. Select **Update** to update selected runbook(s) with new Runtime environment.
   
Check if the runbook executes as expected after update. If the runbook fails to provide the expected outcome, you can again update the Runtime environment to the old one by following the steps from 1-4.

#### [REST API](#tab/update-runbook-rest)

Update Runtime environment linked to a runbook.  

```rest
PATCH 
https://management.azure.com/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Automation/automationAccounts/<accountName>/runbooks/<runbookName>?api-version=2023-05-15-preview 
{ 
  "properties": { 
    “type”: “PowerShell” 
    "runtimeEnvironment": "<runtimeEnvironmentName>" 
  } 
} 
```
> [!NOTE]
> The View/Delete calls for runbook remains same and can be referenced [Runbook - REST API (Azure Automation) | Microsoft Learn](https://learn.microsoft.com/rest/api/automation/runbook?view=rest-automation-2019-06-01).
---

### Test Runbook update

Run a test job for a runbook with a different Runtime environment. This scenario is useful when a runbook needs to be tested with a Runtime environment before update.  

```rest
PUT 
https://management.azure.com/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Automation/automationAccounts/<accountName>/runbooks/<runbookName>/draft/testJob?api-version=2023-05-15-preview 
{ 
  "properties": { 
    "runtimeEnvironment": "<runtimeEnvironmentName>" 
    "runOn": "" 
  } 
} 
```



### Create Cloud Job

#### [Azure portal](#tab/create-cloud-job-portal)

Currently, runbooks linked to Runtime environment would run on Azure.

#### [REST API](#tab/create-cloud-job-rest)
Jobs inherit the Runtime environment from the runbook. Run a cloud job for a published runbook:  

```rest
PUT 
https://management.azure.com/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Automation/automationAccounts/<accountName>/jobs/<jobName>?api-version=2023-05-15-preview 
{ 
  "properties": { 
    "runbook": { 
      "name": "<runbookName>" 
    }, 
    "runOn": "" 
  } 
} 
```
> [!NOTE]
> Create/View calls for job remains same and can be referenced from [Job-REST API (Azure Automation) | Microsoft Learn](https://learn.microsoft.com/rest/api/automation/job?view=rest-automation-2019-06-01)
---



### Map existing runbooks to System-created Runtime environments

All existing runbooks would be automatically linked to System-generated Runtime environments. Following System-generated Runtime environments are generated by adding Modules/packages already present in the Automation account:  

- PowerShell-5.1 
- PowerShell-7.1 
- PowerShell-7.2 
- Python-2.7 
- Python-3.8 
- Python-3.10 

Existing runbooks are linked from the account to the Runtime environments based on Runtime version. These Runtime environments are non-editable. However, any changes in Modules/Packages blades for the Automation account would automatically get reflected in these Runtime environments. 

:::image type="content" source="./media/manage-runtime-environment/mapping-runbooks.png" alt-text="Screenshot shows how mapping of existing runbooks to the system created runtime environment." lightbox="./media/manage-runtime-environment/mapping-runbooks.png":::

## Next steps

* For an overview [Runtime Environment](runtime-environment-overview.md).
* To troubleshoot issues with runbook execution, see [Troubleshoot runbook issues](troubleshoot/runbooks.md).
