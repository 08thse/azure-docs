---
title: Prerequisites for scheduled patching in update management center (preview).
description: The article describes the new prerequisites to configure scheduled patching in Update management center (preview).
ms.service: update-management-center
ms.date: 04/18/2023
ms.topic: conceptual
author: snehasudhirG
ms.author: sudhirsneha
---

# Configure Azure VMs for enhanced patching

**Applies to:** :heavy_check_mark: Windows VMs :heavy_check_mark: Linux VMs :heavy_check_mark: Azure VMs.

This article is an overview on how to configure Schedule patching and Automatic guest VM patching on Azure VMs using the new prerequisite. The steps to configure both the patching options on Arc VMs continue to remain the same.

Currently, you can enable [Automatic guest VM patching](../virtual-machines/automatic-vm-guest-patching.md) (Autopatch) by setting the patch mode to **Azure-orchestrated** or **AutomaticByPlatform** on Azure portal and using REST API respectively, where patches are automatically applied during off-peak hours. 

For customizing control over your patch installation, you can use [schedule patching](updates-maintenance-schedules.md#scheduled-patching) to define your own maintenance window. You can [enable schedule patching](scheduled-patching.md#schedule-recurring-updates-on-single-vm) by setting the patch mode to **Azure orchestrated**, or **AutomaticByPlatform** and attach a schedule to the Azure VM.

However, in certain cases, when you remove the schedule from a VM, there is a possibility that the VM may be autopatched for critical or security patches and subsequently rebooted. To avoid such accidental or unintentional patching, a new prerequisite has been introduced - **ByPassPlatformSafetyChecksOnUserSchedule**, a VM property that allows you to accurately determine the VMs that must be schedule patched or autopatched.


> [!IMPORTANT]
> For a seamless scheduled patching experience, you must ensure that the new VM property is enabled on all your Azure VMs (existing or new) that have schedules attached to them **before April 30, 2023**. Failing to update will give an error that the prerequisites aren't met.


## Enable schedule patching on Azure VMs

# [Azure portal](#tab/new-prereq-portal)

**Prerequisite**

Patch orchestration = Azure-orchestrated with user managed schedules (Preview).

Select the patch orchestration option as **Azure-orchestrated with user managed schedules(Preview)**.
The new patch orchestration option enables the following VM properties on your behalf after receiving your consent:

  - Patch mode = Azure-orchestrated
  - BypassPlatformSafetyChecksOnUserSchedule = TRUE

**Enable for new VMs**

You can select the patch orchestration option for new VMs that would be associated with the schedules:

To update the patch mode, follow these steps:

1. Sign in to the [Azure portal](https://portal.azure.com)
1. Go to **Virtual machine**, and select **+Create** to open *Create a virtual machine* page.
1. In **Basics** tab, complete all the mandatory fields.
1. In **Management** tab, under **Guest OS updates**, for **Patch orchestration options**, select *Azure-orchestrated with user managed schedules(Preview)*.
1. After you complete the entries in **Monitoring**, **Advanced** and **Tags** tabs.
1. Select **Review + Create** and select **Create** to create a new VM with the appropriate patch orchestration option.

To schedule patch the newly created VMs, follow the procedure from step 2 in **Enable for existing VMs**.


**Enable for existing VMs**

You can update the patch orchestration option for existing VMs that either already have schedules associated or are to be newly associated with a schedule:  

> [!NOTE]
> If the **Patch orchestration** is set as *Azure orchestrated*, the **BypassPlatformSafetyChecksOnUserSchedule** is set to *False* and there is no schedule associated, the VM(s) will be autopatched.

To update the patch mode, follow these steps:

1. Sign in to the [Azure portal](https://portal.azure.com)
1. Go to **Update management center (Preview)**, select **Update Settings**.    
1. In **Change update settings**, select **+Add machine**.
1. In **Select resources**, select your VMs and then select **Add**.
1. In **Change update settings**, under **Patch orchestration**, select *Azure orchestrated with user managed schedules (Preview)* and then select **Save**.

Attach a schedule after you complete the above steps.

# [REST API](#tab/new-prereq-rest-api)

**Prerequisite**

- Patch mode = AutomaticByPlatform
- BypassPlatformSafetyChecksOnUserSchedule = TRUE 

**Enable for new VMs**

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine?api-version=2020-12-01`
```

```json
{
  "location": "<location>",
  "properties": {
    "osProfile": {
      "windowsConfiguration": {
        "provisionVMAgent": true,
        "enableAutomaticUpdates": true,
        "patchSettings": {
          "patchMode": "AutomaticByPlatform"
        }
      }
    }
  }
}
```

**Enable for existing VMs**

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine?api-version=2020-12-01`
```

```json
{
  "location": "<location>",
  "properties": {
    "osProfile": {
      "windowsConfiguration": {
        "provisionVMAgent": true,
        "enableAutomaticUpdates": true,
        "patchSettings": {
          "patchMode": "AutomaticByPlatform"
        }
      }
    }
  }
}
```
---
> [!NOTE]
> Currently, you can only enable the new prerequisite for schedule patching via Azure portal and REST API. It cannot be enabled via Azure CLI and PowerShell.

--- 

## Enable automatic guest VM patching on Azure VMs

To enable automatic guest VM patching on your Azure VMs now, follow these steps:

# [Azure portal](#tab/auto-portal)

**Prerequisite**

Patch mode = Azure-orchestrated

**Enable for new VMs**

You can select the patch orchestration option for new VMs that would be associated with the schedules:

To update the patch mode, follow these steps:

1. Sign in to the [Azure portal](https://portal.azure.com)
1. Go to **Virtual machine**, and select **+Create** to open *Create a virtual machine* page.
1. In **Basics** tab, complete all the mandatory fields.
1. In **Management** tab, under **Guest OS updates**, for **Patch orchestration options**, select *Azure-orchestrated*.
1. After you complete the entries in **Monitoring**, **Advanced** and **Tags** tabs.
1. Select **Review + Create** and select **Create** to create a new VM with the appropriate patch orchestration option.


**Enable for existing VMs**

To update the patch mode, follow these steps:

1. Sign in to the [Azure portal](https://portal.azure.com)
1. Go to **Update management center (Preview)**, select **Update Settings**. 
1. In **Change update settings**, select **+Add machine**.
1. In **Select resources**, select your VMs and then select **Add**.
1. In **Change update settings**, under **Patch orchestration**, select *Azure-orchestrated Global safe deployment* and then select **Save**.


# [REST API](#tab/auto-rest-api)

**Prerequisites**

- Patch mode = AutomaticByPlatform
- BypassPlatformSafetyChecksOnUserSchedule = FALSE

**Enable for new VMs**

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine?api-version=2020-12-01`
```

```json
{
  "location": "<location>",
  "properties": {
    "osProfile": {
      "windowsConfiguration": {
        "provisionVMAgent": true,
        "enableAutomaticUpdates": true,
        "patchSettings": {
          "patchMode": "AutomaticByPlatform"
        }
      }
    }
  }
}
```

**Enable for existing VMs**

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine?api-version=2020-12-01`
```

```json
{
  "location": "<location>",
  "properties": {
    "osProfile": {
      "windowsConfiguration": {
        "provisionVMAgent": true,
        "enableAutomaticUpdates": true,
        "patchSettings": {
          "patchMode": "AutomaticByPlatform"
        }
      }
    }
  }
}
```
---


## User scenarios

**Scenarios** | **Azure-orchestrated** | **BypassPlatformSafetyChecksOnUserSchedule** | **Schedule Associated** |**Expected behavior in Azure** |
--- | --- | --- | --- | ---|
Scenario 1 | Yes | True | Yes | The schedule patch runs as defined by user. |
Scenario 2 | Yes | True | No | Neither autopatch nor the schedule patch will run.|
Scenario 3 | Yes | False | Yes | Neither autopatch nor schedule patch will run. You'll get an error that the prerequisites for schedule patch aren't met.| 
Scenario 4 | Yes |  False | No   | The VM is autopatched.|
Scenario 5 | No | True | Yes | Neither autopatch nor schedule patch will run. You'll get an error that the prerequisites for schedule patch aren't met. |
Scenario 6 | No | True | No | Neither the autopatch nor the schedule patch will run.|
Scenario 7 | No | False | Yes | Neither autopatch nor schedule patch will run. You'll get an error that the prerequisites for schedule patch aren't met.| 
Scenario 8 | No | False | No | Neither the autopatch nor the schedule patch will run.|

## Next steps

* To troubleshoot issues, see the [Troubleshoot](troubleshoot.md) update management center (preview).