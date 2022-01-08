---
title: Query logs and results from update management center (private preview)
description: This article provides details on how you can review logs and search results from update management center (private preview) in Azure using Azure Resource Graph
ms.service: update-management-center
author: mgoedtel
ms.author: magoedte
ms.date: 08/17/2021
ms.topic: conceptual
---

# Query update management center (private preview) logs

Logs created from operations like update assessments and installations are stored by update management center (private preview) in [Azure Resource Graph](/azure/governance/resource-graph/overview). Azure Resource Graph is a service in Azure designed to be the store for Azure service details without any cost or deployment requirements. Update management center (private preview) uses Azure Resource Graph to store its results, and you can view the last 30 days of of update history from the resources.

Azure Resource Graph's query language is based on the [Kusto query language](/azure/governance/resource-graph/concepts/query-language) used by Azure Data Explorer. 

This article describes the structure of the logs from Update management center (private preview) and how you can use [Azure Resource Graph Explorer](/azure/governance/resource-graph/first-query-portal) to analyze them in support of your reporting, visualizing, and export needs.

## Log structure

Update management center (private preview) sends the results of all its operation into Azure Resource Graph as logs, which are available for 30 days. Listed below are the structure of logs being sent to Azure Resource Graph.

### Patch assessment results

The table `patchassessmentresources` includes resources related to machine patch assessment. The following table describes its properties. 

| Property | Description |
|----------|-------------|
| `ID` | The Azure Resource Manager ID forwarding the result. It will be the similar to the [REST API](manage-vms-programmatically.md) path for Guest OS assessment. Typically, *`<resourcePath>/patchAssessmentResults/latest`* or *`<resourcePath>/patchAssessmentResults/latest/softwarePatches/<update>`* |
| `NAME` | If the ID is of type *`<resourcePath>/patchAssessmentResults/latest`* - then the record contains unique GUID for the assessment operation completed. If *`<resourcePath>/patchAssessmentResults/latest/softwarePatches/<update>`* - then the record contains update name or label. |
| `TYPE` |Specifies the type of log for assessment. If type is `patchassessmentresults` , then the record provides a summary of OS assessment with numerical aggregate statistics. If type is `patchassessmentresults/softwarepatches`, then the record describes a specific OS update available for the resource. |
| `TENANTID` | Azure tenant ID for the Azure VM or Azure Arc-enabled server resource|	
| `KIND`	| Intentionally left blank for future use. |
| `LOCATION` | Azure cloud region where the Azure VM or Azure Arc-enabled server resource exists|
| `RESOURCEGROUP` | Azure resource group hosting the Azure VM or Azure Arc-enabled server resource|
| `SUBSCRIPTIONID` | Azure subscription ID for the Azure VM or Azure Arc-enabled server resource |
| `MANAGEDBY` |  Intentionally left blank for future use. |
| `SKU` |  Intentionally left blank for future use. |
| `PLAN` |	Intentionally left blank for future use. |
| `PROPERTIES` | Captures details of operation in JSON format. Additional information follows this table.|
| `TAGS` | Azure tags defined for the Azure VM or Azure Arc-enabled servers resource |
| `IDENTITY` |	Intentionally left blank for future use. |
| `ZONES` | Intentionally left blank for future use. |
| `EXTENDEDLOCATION` | Intentionally left blank for future use. |


### Description of the **PROPERTIES** property 

If the `PROPERTIES` property for the resource type is `patchassessmentresources`, it includes the following information: 

|Value |Description |
|------|------------|
| `rebootPending` |Flag to specify if the specific update requires the OS to reboot to complete installation. As provided by machine's OS update service or package manager. If your OS package manager or update service does not require a reboot, the value of the field is set to `false`.|
|`patchServiceUsed` |OS service used on the machine to install updates. `WU-WSUS` for Windows Update service and/or Windows Server Update Service. For Linux, its the OS package manager like `YUM`, `APT`, or `Zypper`.|
|`osType` |Represents the type of operating system `Windows` or `Linux`.|
|`startDateTime` |Timestamp (UTC) representing when the OS update assessment task started execution on the machine.|
|`lastModifiedDateTime` |Timestamp (UTC) representing when the record was last updated.|
|`startedBy` |Identifies if the OS update installation run was triggered by a user or Azure service. Further details of the operation can be found in [Azure Activity Log](/azure/azure-resource-manager/management/view-activity-logs).|
|`errorDetails` |First 5 error messages generated while executing update installation from the machine's OS package manager or update service.|
|`availablePatchCountByClassification` |Number of OS updates by the category which the specific updates belongs based on the OS vendor. Information is generated by the machine's OS update service or package manager. If your OS package manager or update service, does not provide the detail of category, then the value is `Others` (for Linux) or `Updates` (for Windows Server).|
|

If the `PROPERTIES` property for the resource type is `patchassessmentresults/softwarepatches`, it includes the following information: 

|Value |Description |
|------|------------|
|`lastModifiedDateTime` |Timestamp (UTC) representing when the record was last updated.|
|`publishedDateTime` |Timestamp representing when the specific update was made available by the OS vendor. Information is generated by the machine's OS update service or package manager. If your OS package manager or update service does not provide the detail of when an update was provided by OS vendor, then the value is null.|
|`classifications` |Category of which the specific update belongs to as per the OS vendor. Information is generated by the machine's OS update service or package manager. If your OS package manager or update service does not provide the detail of category, then the value  is `Others` (for Linux) or `Updates` (for Windows Server). |
|`rebootRequired` |Value indicates if the specific update requires the OS to reboot to complete the installation. Information is generated by the machine's OS update service or package manager. If your OS package manager or update service does not require a reboot, then the value is `false`.|
|`rebootBehavior` |Behavior set in the OS update installation run job when configuring the update deployment if update management center (private preview) can reboot the target machine. |
|`patchName` |Name or label for the specific update generated by the machine's OS package manager or update service.|
|`Kbid` |If the machine's OS is Windows Server, the value includes the unique KB ID for the update provided by the Windows Update service.|
|`version` |If the machine's OS is Linux, the value includes the version details for the update as provided by Linux package manager. For example, `1.0.1.el7.3`.|

### Patch installation results

The table `patchinstallationresources` includes resources related to machine patch assessment. The following table describes its properties. 

| Property | Description |
|----------|-------------|
| `ID` | The Azure Resource Manager ID forwarding the result. It will be the similar to the [REST API](manage-vms-programmatically.md) path for Guest OS assessment. Typically, *`<resourcePath>/patchInstallationResults/<GUID>`* or *`<resourcePath>/patchAssessmentResults/latest/softwarePatches/<update>`* |
| `NAME` | If the ID is of type *`<resourcePath>/patchInstallationResults`* - then the record contains unique GUID for the update operation completed. If *`<resourcePath>/patchInstallationResults/softwarePatches/<update>`* - then the record contains update name or label being installed on the machine. |
| `TYPE` |Specifies the type of log for assessment. If type is `patchinstallationresults` , then the record provides a summary of OS installation with numerical aggregate statistics. If type is `patchinstallationresults/softwarepatches`, then the record describes a specific OS update installed for the resource. |
| `TENANTID` | Azure tenant ID for the Azure VM or Azure Arc-enabled server resource |	
| `KIND`	| Intentionally left blank for future use. |
| `LOCATION` | zure cloud region where the Azure VM or Azure Arc-enabled server resource exists|
| `RESOURCEGROUP` | Azure resource group hosting the Azure VM or Azure Arc-enabled server resource|
| `SUBSCRIPTIONID` | Azure subscription ID for the Azure VM or Azure Arc-enabled server resource|
| `MANAGEDBY` |  Intentionally left blank for future use. |
| `SKU` |  Intentionally left blank for future use. |
| `PLAN` |	Intentionally left blank for future use. |
| `PROPERTIES` | Captures details of operation in JSON format. Additional information follows this table.|
| `TAGS` | Azure tags defined for the Azure VM or Azure Arc-enabled servers resource	|
| `IDENTITY` |	Intentionally left blank for future use. |
| `ZONES` | Intentionally left blank for future use. |
| `EXTENDEDLOCATION` | Intentionally left blank for future use. |

### Description of the **PROPERTIES** property 

If the `PROPERTIES` property for the resource type is `patchinstallationresults`, it includes the following information: 

|Value |Description |
|------|------------|
|`installationActivityId` | Unique GUID for the OS update installation run. |
|`maintenanceWindowExceeded` | Values are `True` or `False` if update installation run exceeded the defined maintenance window. |
|`lastModifiedDateTime` |Timestamp (UTC) representing when the record was last updated |
|`notSelectedPatchCount` |Number of OS updates available on the machine not selected for installation in an update deployment. |
|`installedPatchCount` |Number of OS updates that were successfully installed that were specified in an update deployment. |
|`excludedPatchCount` |Number of OS updates available on the machine and excluded for installation in an update deployment.|
|`pendingPatchCount` |Number of OS updates still awaiting to be installed that were specified in an update deployment. |
|`patchServiceUsed` |OS service used on the machine to install updates. `WU-WSUS` for Windows Update service and/or Windows Server Update Service. For Linux, its the OS package manager like `YUM`, `APT`, or `Zypper`. |
|`failedPatchCount` |Number of OS updates that failed to successfully get installed that were specified in an update deployment. |
|`startDateTime` |Timestamp (UTC) representing when the OS update installation task started execution on the machine. |
|`rebootStatus` |Information from the OS update service or package manager, if the OS needs to be restarted to complete the update installation. Status values are `NotNeeded` (No restart is needed), `Required` (OS restart is needed for completion), `Started` (Restart was initiated), `Failed` (OS could not be restarted), and `Completed` (Restart was done successfully). |
|`startedBy` |Identifies if the OS update installation run was triggered by a user or an Azure service. Further details of the operation can be found in [Azure Activity Log](/azure/azure-resource-manager/management/view-activity-logs). |
|`status` |Status of the OS update installation run. Values can be - NotStarted, InProgress, Failed, Succeeded and CompletedWithWarnings. Note that the update installation run is deemed 'Failed' status, if one or more OS update installation is unsuccessful. |
|`osType` |Represents the type of operating system `Windows` or `Linux`. |
|`errorDetails` |Includes the first five error messages generated while executing update installation from the machine's OS package manager or update service. |
|`maintenanceRunId ` | This is used as a maintenance run identifier for Auto VM Guest Patching or schedule run id in the case of recurring updates |

If the `PROPERTIES` property for the resource type is `patchinstallationresults/softwarepatches`, it includes the following information: 

|Value |Description |
|------|------------|
|`installationState` |Installation status for the specific OS update. Values are `Installed`, `Failed`, `Pending`, `NotSelected`, and `Excluded`. |
|`lastModifiedDateTime` |Timestamp (UTC) representing when the record was last updated. |
|`publishedDateTime` |Timestamp representing when the specific update was made available by the OS vendor. Information is generated by the machine's OS update service or package manager. If your OS package manager or update service does not provide the detail of when an update was provided by OS vendor, then the value is null. |
|`classifications` |Category which the specific update belongs to as per the OS vendor.  As provided by machine's OS update service or package manager. If your OS package manager or update service, does not provide the detail of category - then the value of the field will be Others (for Linux) and Updates (for Windows Server). |
|`rebootRequired` |Flag to specify if the specific update requires the OS to reboot to complete installation. As provided by machine's OS update service or package manager. If your OS package manager or update service, does not provide regarding need of OS reboot - then the value of the field will be set of 'false'. |
|`rebootBehavior` |Behavior set in the OS update installation run job by user, regarding allowing update management center (private preview) to reboot the OS. |
|`patchName` |Name or Label for the specific update as provided by the machine's OS package manager or update service. |
|`Kbid` |If the machine's OS is Windows Server, the value includes the unique KB ID for the update provided by the Windows Update service. |
|`version` |If the machine's OS is Linux, the value includes the version details for the update as provided by Linux package manager. For example, `1.0.1.el7.3`. |

### Maintenance resources

The table `maintenanceresources` includes resources related to maintenance configuration. The following table describes its properties. 

| Property | Description |
|----------|-------------|
| `ID` | The Azure Resource Manager ID forwarding the result. It will be the similar to the [REST API](manage-vms-programmatically.md) path for create a maintenance configuration. |
| `NAME` | If the ID is of type *`<resourcePath>/applyupdates`* - then the record contains unique GUID for the maintenance run. If *`<resourcePath>/configurationassignments`* - then the record contains the assignment of maintenance configuration to an Azure or Arc VM. |
| `TYPE` |Specifies the type of log for assessment. If type is `applyupdates` , then the record provides details of maintenance run record at machine level. If type is `configurationassignments`, then the record describes the link between Azure or Arc VM and a maintenance configuration. |
| `TENANTID` | Azure tenant ID for the Azure VM or Azure Arc-enabled server resource |	
| `KIND`	| Intentionally left blank for future use. |
| `LOCATION` | zure cloud region where the Azure VM or Azure Arc-enabled server resource exists|
| `RESOURCEGROUP` | Azure resource group hosting the Azure VM or Azure Arc-enabled server resource|
| `SUBSCRIPTIONID` | Azure subscription ID for the Azure VM or Azure Arc-enabled server resource|
| `MANAGEDBY` |  Intentionally left blank for future use. |
| `SKU` |  Intentionally left blank for future use. |
| `PLAN` |	Intentionally left blank for future use. |
| `PROPERTIES` | Captures details of operation in JSON format. Additional information follows this table.|
| `TAGS` | Azure tags defined for the Azure VM or Azure Arc-enabled servers resource	|
| `IDENTITY` |	Intentionally left blank for future use. |
| `ZONES` | Intentionally left blank for future use. |
| `EXTENDEDLOCATION` | Intentionally left blank for future use. |

### Description of the **PROPERTIES** property 

If the `PROPERTIES` property for the resource type is `applyupdates`, it includes the following information: 

|Value |Description |
|------|------------|
|`maintenanceConfigurationId` | ARM ID of applied maintenance configuration |
|`maintenanceScope` | Maintenance scope of applied maintenance configuration |
|`resourceId` | ARM resource id of ARC/Azure VM |
|`correlationId` | Schedule run id of maintenance/schedule run. This can be used to find all the VMs which were part of same schedule. |
|`startDateTime` | Start date and time of schedule |
|`endDateTime` | End date and time of schedule |

If the `PROPERTIES` property for the resource type is `configurationassignments`, it includes the following information: 

|Value |Description |
|------|------------|
|`resourceId` | ARM resource id of ARC/Azure VM |
|`maintenanceConfigurationId` | ARM ID of applied maintenance configuration |


## Sample queries

The following are some sample queries to help you get started querying the update assessment and deployment information collected from your managed machines. 
 
### List available updates for all your machines grouped by update category

The following query returns a list of pending updates for your machine with the time when the assessment was performed, the resource ID for the assessment, OS type on the machine, and the OS updates available based on update classification.

```kusto
patchassessmentresources
| where type !has "softwarepatches"
| extend prop = parse_json(properties)
| extend lastTime = properties.lastModifiedDateTime
| extend updateRollupCount = prop.availablePatchCountByClassification.updateRollup, featurePackCount = prop.availablePatchCountByClassification.featurePack, servicePackCount = prop.availablePatchCountByClassification.servicePack, definitionCount = prop.availablePatchCountByClassification.definition, securityCount = prop.availablePatchCountByClassification.security, criticalCount = prop.availablePatchCountByClassification.critical, updatesCount = prop.availablePatchCountByClassification.updates, toolsCount = prop.availablePatchCountByClassification.tools, otherCount = prop.availablePatchCountByClassification.other, OS = prop.osType
| project lastTime, id, OS, updateRollupCount, featurePackCount, servicePackCount, definitionCount, securityCount, criticalCount, updatesCount, toolsCount, otherCount
```

### Count of update installations 

The following query returns a list of update installations with their status for your machines from the last 7 days. Results include the time when the update deployment was run, the resource ID of the installation, machine details, and the count of OS updates installed based on their status and what you selected.

```kusto
patchinstallationresources
| where type !has "softwarepatches"
| extend machineName = tostring(split(id, "/", 8)), resourceType = tostring(split(type, "/", 0)), tostring(rgName = split(id, "/", 4))
| extend prop = parse_json(properties)
| extend lTime = todatetime(prop.lastModifiedDateTime), OS = tostring(prop.osType), installedPatchCount = tostring(prop.installedPatchCount), failedPatchCount = tostring(prop.failedPatchCount), pendingPatchCount = tostring(prop.pendingPatchCount), excludedPatchCount = tostring(prop.excludedPatchCount), notSelectedPatchCount = tostring(prop.notSelectedPatchCount)
| where lTime > ago(7d)
| project lTime, RunID=name,machineName, rgName, resourceType, OS, installedPatchCount, failedPatchCount, pendingPatchCount, excludedPatchCount, notSelectedPatchCount
```

### List of Windows Server OS update installations 

The following query returns a list of update installations for Windows Server with their status for your machines from the last 7 days. Results include the time when the update deployment was run, the resource ID of the installation, machine details, and other related deployment details.

```kusto
patchinstallationresources
| where type has "softwarepatches" and properties !has "version"
| extend machineName = tostring(split(id, "/", 8)), resourceType = tostring(split(type, "/", 0)), tostring(rgName = split(id, "/", 4)), tostring(RunID = split(id, "/", 10))
| extend prop = parse_json(properties)
| extend lTime = todatetime(prop.lastModifiedDateTime), patchName = tostring(prop.patchName), kbId = tostring(prop.kbId), installationState = tostring(prop.installationState), classifications = tostring(prop.classifications)
| where lTime > ago(7d)
| project lTime, RunID, machineName, rgName, resourceType, patchName, kbId, classifications, installationState
| sort by RunID
```

### List of Linux OS update installations

The following query returns a list of update installations for Linux with their status for your machines from the last 7 days. Results include the time when the update deployment was run, the resource ID of the installation, machine details, and other related deployment details.

```kusto
patchinstallationresources
| where type has "softwarepatches" and properties has "version"
| extend machineName = tostring(split(id, "/", 8)), resourceType = tostring(split(type, "/", 0)), tostring(rgName = split(id, "/", 4)), tostring(RunID = split(id, "/", 10))
| extend prop = parse_json(properties)
| extend lTime = todatetime(prop.lastModifiedDateTime), patchName = tostring(prop.patchName), version = tostring(prop.version), installationState = tostring(prop.installationState), classifications = tostring(prop.classifications)
| where lTime > ago(7d)
| project lTime, RunID, machineName, rgName, resourceType, patchName, version, classifications, installationState
| sort by RunID
```

### List of maintenance run record at VM level
The following query returns a list of all the maintenance run records for a VM

```kusto
maintenanceresources 
| where ['id'] contains "/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/microsoft.compute/virtualmachines/<vm-name>" //VM Id here
| where ['type'] == "microsoft.maintenance/applyupdates" 
| where properties.maintenanceScope == "InGuestPatch"
```

## Next steps

To troubleshoot issues, see the [Troubleshoot](troubleshoot.md) update management center (private preview).
