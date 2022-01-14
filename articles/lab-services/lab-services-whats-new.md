---
title: What's New in Azure Lab Services | Microsoft Docs
description: Learn what's new in the Azure Lab Services January 2022 Updates. 
ms.topic: overview
ms.date: 01/06/2022
---

# What's new in Azure Lab Services January 2022 Updates (preview)

We've made fundamental improvements for the service to boost performance, reliability, and scalability. In this article, we'll describe all the great changes and new features that are available in this preview!

## Overview

**[Lab plans replace lab accounts](#lab-plans-replace-lab-accounts).** The lab account concept is being replaced with a new concept called a lab plan. Although similar in functionality, there are some fundamental differences between the two concepts. The lab plan serves as a collection of configurations and settings that apply to the labs created from it. Also, labs are now an Azure resource in their own right and a sibling resource to lab plans.

**[Canvas Integration](how-to-get-started-create-lab-within-canvas.md)**. Now, instructors don’t have to leave Canvas to create their labs. Students can connect to a virtual machine from inside their course.

**[Per customer assigned capacity](capacity-limits.md#per-customer-assigned-capacity)**. No more sharing capacity with others. If your organization has requested more quota, Azure Lab Services will save it just for you.

**[Virtual network injection](how-to-connect-vnet-injection.md)**.  Virtual network peering is replaced by virtual network injection. In your own subscription, create a virtual network in the same region as the lab plan and delegate a subnet to Azure Lab Services.  Lab plans with vnet injection (i.e. advanced networking) will cause labs to create VMs attached to your virtual network.

**[Improved auto-shutdown](how-to-configure-auto-shutdown-lab-plans.md)**. Auto-shutdown settings are now available for *all* operating systems!

**[More built-in roles](administrator-guide.md#rbac-roles)**. Previously, there was only the Lab Creator built-in role.  We’ve added a few more roles including Lab Operator and Lab Assistant. Lab operators can manage existing labs, but not create new ones. Lab assistants can only help students by starting, stopping, or redeploying virtual machines. Lab assistants can't adjust quota or set schedules.

**[Improved cost tracking in Azure Cost Management](cost-management-guide.md#separate-the-costs)**. Lab virtual machines are now the cost unit tracked in Azure Cost Management. Tags for lab plan ID and lab name are automatically added to each cost entry.  If you want to track the cost of a single lab, group the lab VM cost entries together by the lab name tag. Custom tags on labs will also propagate to Azure Cost Management entries to allow further cost analysis.  

**[Updates to lab owner experience](how-to-manage-labs.md)**. Now you can choose to skip the template creation process when creating a new lab if you already have an image ready to use. We’ve also added the ability to add a non-admin user to lab VMs.

**[Updates to student experience](get-started-manage-labs.md#redeploy-vms)**. Student can now redeploy their VM without losing data. We also updated the registration experience for some scenarios.  A lab VM is assigned to students *automatically* if the lab is set up to use Azure AD group sync, Teams, or Canvas.

**SDKs**. The Azure Lab Services PowerShell will now be integrated with the Az PowerShell module and will release with the February 2022 monthly update of the Az module. Also, check out the C# SDK.

[Give it a try!](tutorial-setup-lab-plan.md)

In this release, there are a few known issues:

- Az.LabServices cmdlets will be included in the February 2022 [monthly release](/powershell/azure/release-notes-azureps) for the [Azure PowerShell module](/powershell/azure/new-azureps-module-az).
- When using virtual network injection, use caution in making changes to the virtual network and subnet.  Changes may can cause the lab VMs to stop working. For example, deleting your virtual network will cause all the lab VMs to stop working. We plan to improve this experience in the future, but for now make sure to delete labs before deleting networks.
- Moving lab plan and lab resources from one Azure region to another isn't yet supported.

### Lab plans replace lab accounts

For the new version of Lab Services, the lab account concept is being replaced with a new concept called a lab plan. Although similar in functionality, there are some fundamental differences between the old lab account and the new lab plan.

|Lab account (classic)|Lab plan|
|-|-|
|Lab account was the only resource that administrators could interact with inside the Azure portal.|Administrators can now manage two types of resources, lab plan and lab, in the Azure portal.|
|Lab account served as the **parent** for the labs.|Lab plan is a **sibling** resource to the lab resource. Grouping of labs is now done by the resource group.|
|Lab account served as a container for the labs.  A change to the lab account often affected the labs under it.|The lab plan serves as a collection of configurations and settings that are applied when a lab is **created**. If you change a lab plan’s settings, these changes won’t affect any existing labs that were previously created from the lab plan. (The exception is the internal help information, which will affect all labs.)|

Lab accounts and labs have a parental relationship.  By moving to a sibling relationship between the lab plan and lab, lab plans provide an upgraded experience. The following table compares the previous experience with a lab account and the new improved experience with a lab plan.

|Feature/area|Lab account (classic)|Lab plan|
|-|-|-|
|Resource Management|Lab account was the only resource tracked in the Azure portal. All other resources were child resources of the lab account and tracked in Lab Services directly.|Lab plans and labs are now sibling resources in Azure. Administrators can use existing tools in the Azure portal to manage labs. Virtual machines will continue to be a child resource of labs.|
|Cost tracking|In Azure Cost Management, admins could only track and analyze cost at the service level and at the lab account level.| Cost entries in Azure Cost Management are now for lab virtual machines. Automatic tags on each entry specify the lab plan ID and the lab name. You can analyze cost by lab plan, lab, or virtual machine from within the Azure portal. Custom tags on the lab will also show in the cost data.|  
|Selecting regions|By default, labs were created in the same geography as the lab account.  A geography typically aligns with a country and contains one or more Azure regions. Lab owners weren't able to manage exactly which Azure region the labs resided in.|In the lab plan, administrators now can manage the exact Azure regions allowed for lab creation. By default, labs will be created in the same Azure region as the lab plan. </br> Note, when a lab plan has advanced networking enabled, labs are created in the same Azure region as virtual network.|
|Deletion experience|When a lab account is deleted, all labs within it are also deleted.|When deleting a lab plan, labs *aren't* deleted. After a lab plan is deleted, labs will keep references to their virtual network even if advanced networking is enabled. However, if a lab plan was connected to an Azure Compute Gallery, the labs can no longer export an image that Azure Compute Gallery.|
|Connecting to a virtual network|The lab account provided an option to peer to a virtual network. If you already had labs in the lab account before you peered to a virtual network, the virtual network connection didn't apply to existing labs. Admins couldn't tell which labs in the lab account were peered to the virtual network.|In a lab plan, admins set up the advanced networking only at the time of lab plan creation. Once a lab plan is created, you'll see a read-only connection to the virtual network. If you need to use another virtual network, create a new lab plan configured with the new virtual network.|
|Labs portal experience|Labs are lab listed under lab accounts in [https://labs.azure.com](https://labs.azure.com).|Labs are listed under resource group name in [https://labs.azure.com](https://labs.azure.com). If there are multiple lab plans in the same resource group, instructors can choose which lab plan to use when creating the lab.|
|Permissions needed to manage labs|To create a lab, someone must be assigned:</br>- **Lab Contributor** role on the lab account.</br>To modify an existing lab, someone must be assigned:</br>- **Reader** role on the lab account.</br>- **Owner** or **Contributor** role on the lab. (Lab creators are assigned the **Owner** role to any labs they create.)|To create a lab, someone must be assigned:</br>- **Owner** or **Contributor** role on the resource group that contains the lab plan.</br>- **Lab Creator** role on the lab plan.</br>To modify an existing lab, someone must be assigned:</br>- **Owner** or **Contributor** role on the lab. (Lab creators are assigned the **Owner** role to any labs they create.)|

### Migrate from lab account to lab plan

To use the new features provided in the public preview, you'll need to create new lab plans and labs. When you create a lab plan, you can reuse the same Azure Compute Gallery and images.  Likewise, you can reuse the same licensing server. As you migrate, there likely will be a time when you're using both the January 2022 Update (preview) and the current version of Azure Lab Services. You might have both lab accounts and lab plans that coexist in your subscription and that access the same external resources.

With all the new enhancements in the January 2022 Update (preview), it's a good time to revisit your overall lab structure. More than one lab plan might be needed depending on your scenario.  For example, the math department may only require one lab plan in one resource group.  The computer science department might require multiple lab plans.  One lab plan can enable advanced networking and enable a few custom images from an Azure Compute Gallery.  Another lab plan can use basic networking and not enable custom images.  Both lab plans can be kept in the same resource group.

### Configure a lab plan

Once the lab plan is [created](how-to-manage-lab-plans.md), administrators can set up configurations as needed.

Most lab plan configurations apply at the time of lab creation.

- Which region(s) the labs can be created in.
- Default auto-shutdown settings for labs.
- What marketplace images are allowed.
- What custom images from a connected Azure Compute Gallery are allowed.
- Linked Azure Compute Gallery to export custom VM images to.
- Give access to educators to create and manage labs.

Configuration that applies at to all labs:

- Internal support information for your organization when using Azure Lab Services.

Remember, changes made to the lab settings from the lab plan will apply only to new labs created after the settings change is saved.

Don't forget to assign user permissions on the lab plan and the lab plan’s resource group.  Permission assignments for new labs may also be required if labs are created for instructors instead of by them.

## Next Steps

- [Create a lab plan](tutorial-setup-lab-plan.md).
- [Manage your lab plan](how-to-manage-lab-plans.md).
- [Set up a lab](tutorial-setup-lab.md)
