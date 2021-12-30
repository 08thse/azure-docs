---
title: What's New in Azure Lab Services | Microsoft Docs
description: Learn what's new in the Azure Lab Services January 2022 Updates. 
ms.topic: overview
ms.date: 11/19/2021
---

# What's new in Azure Lab Services January 2022 Updates (preview)

We've made fundamental improvements for the service to boost performance, reliability, and scalability. In this article, we describe all the great changes and new features that are available in this preview!

**[Lab plans replace lab accounts](#lab-plans-replace-lab-accounts).** The lab account concept is being replaced with a new concept called a lab plan. Although similar in functionality, there are some fundamental differences. The lab plan serves as a collection of configurations and settings that apply to the labs created from it. Labs are now an Azure resource in their own right and a sibling resource to lab plans.

**[Canvas Integration](how-to-get-started-create-lab-within-canvas.md)**. Use Canvas to organize everything for your classes—even virtual labs. Now, instructors don’t have to leave Canvas to create their labs. Students can connect to a virtual machine from inside their course.

**[Per customer assigned capacity](capacity-limits.md#per-customer-assigned-capacity)**. No more sharing capacity with others. If your organization has requested more quota, Azure Lab Services will save it just for you.

**[Virtual network injection](how-to-connect-vnet-injection.md)**.  Virtual network peering is replaced by virtual network injection. In your own subscription, create a virtual network in the same region as the lab and delegate a subnet to Azure Lab Services will cause lab VMs to be attached to your virtual network.

**[Improved auto-shutdown](how-to-configure-auto-shutdown-lab-plans.md)**. Auto-shutdown settings are now available for **all** operating systems! If we detect a student shut down their VM, we’ll stop billing.

**[More built-in roles](administrator-guide.md#manage-identity)**. In addition to Lab Creator, we’ve added Lab Operator and Lab Assistant roles. Lab Operators can manage existing labs, but not create new ones. Lab Assistant can only help students by starting, stopping, or redeploying virtual machines. They will not be able to adjust quota or set schedules.

**[Improved cost tracking in Cost Management](cost-management-guide.md#separate-the-costs)**. Lab virtual machines are now the cost unit tracked in Azure Cost Management. Tags for lab plan ID and lab name are automatically added if you want to group lab VM cost entries together. Custom tags on labs will propagate to Azure Cost Management entries to allow further cost analysis.  

**[Updates to lab owner experience](how-to-manage-labs.md)**. Now you can choose to skip the template creation process and automatically publish the lab if you already have an image ready to use. In addition, we’ve added the ability to add a non-admin user to lab VMs and made some scheduling improvements while we were at it.

**[Updates to student experience](get-started-manage-labs.md#redeploy-vms)**. Student can now redeploy their VM without losing data.  Also, if the lab is set up to use Azure AD group sync, Teams or Canvas, there is no longer a need to send an invitation email so students can register for a virtual machine—one is assigned to the student automatically.

**SDKs**. The Azure Lab Services PowerShell will now be integrated with the Az PowerShell module and will release with the next monthly update of the Az module. Also, check out the C# SDK.

[Give it a try!](tutorial-setup-lab-plan.md)

In this release, there remain a few known issues:

- Az.LabServices cmdlets will be included in the February [monthly release](/powershell/azure/release-notes-azureps) for the [Azure PowerShell module](/powershell/azure/new-azureps-module-az).
- When using virtual network injection, use caution in making changes to the virtual network and subnet because it can cause the lab VMs to stop working. For example, deleting your virtual network will cause all the lab VMs to stop working. We plan to improve this experience, but for now make sure to delete labs before deleting networks.
- Moving lab plan and lab resources from one Azure region to another is not yet supported.

## Lab plans replace lab accounts

For the new version of Lab Services, the lab account concept is being replaced with a new concept called a lab plan. Although similar in functionality, there are some fundamental differences between the old lab account and the new Lab plan:

|Lab account (classic)|Lab plan|
|-|-|
|Lab account was the only resource that administrators could interact with inside the Azure portal.|Administrators can now manage two types of resources, lab plan and lab, in the Azure portal.|
|Lab account served as the **parent** for the labs.|Lab plan is a **sibling** resource to the lab resource. Grouping of labs is now done by the resource group.|
|Lab account served as a container for the labs.  A change to the lab account often affected the lab.|The lab plan serves as a collection of configurations and settings that apply when a lab is **created**. If you change a lab plan’s settings, these changes won’t impact any existing labs that were previously created from the lab plan. (The exception to this is the internal help information settings, which will affect all labs.)|

By moving to a sibling relationship between the lab plan and lab instead of a parental relationship, lab plan provides an upgraded experience from the lab account experience. The following table compares the previous experience with a lab account and the new improved experience with a lab plan:

|Feature/area|Lab account (classic)|Lab plan|
|-|-|-|
|Resource Management|Lab account was the only resource tracked in the Azure portal. All other resources were child resources of the lab account and tracked in Lab Services directly.|Lab plans and labs are now sibling resources. Administrators can now use existing tools in the Azure portal to manage labs.</br>Virtual machines will continue to be a child resource of labs.|
|Cost tracking|In Azure Cost Management, admins were able to track and analyze cost only at the service level and at the lab account level.|In Azure Cost Management, entries are for lab virtual machines. Automatic tags on each entry specify the lab plan ID and the lab. Now you can analyze cost by lab plan, lab, or virtual machine from within the Azure portal. Custom tags on the lab will also show in the cost data.|  
|Selecting regions|By default, labs were created in the same geography as the lab account.  A geography typically aligns with a country and contains one or more Azure regions. Lab owners were not able to manage exactly which Azure region the labs resided in, only the geography.|In the lab plan, administrator now can manage the exact Azure regions to allowed for lab creation. By default, labs will be created in the same Azure region as the lab plan they were created from. </br>Note that when a lab plan is connected to your own virtual network, labs can only be created in the same Azure region as that virtual network.|
|Deletion experience|When a lab account is deleted, all labs within it are also deleted.|When deleting a lab plan, labs are *not* deleted. Labs that were created from a deleted lab plan will continue to retain references to:</br>- A virtual network, if advanced networking was configured on the lab plan.</br>- An image from Shared Image Gallery, if a custom image was used to create the lab.</br>- However, the labs will no longer be able to export an image to Shared Image Gallery.|
|Connecting to a virtual network|The lab account provided an option to peer to a virtual network. If you already had labs in the lab account before you peered to a virtual network, the virtual network connection did not apply to existing labs. This created a situation where admins could not tell which labs in the lab account were peered to the virtual network.|In a lab plan, admins will have the ability to set up the advanced networking only at the time of lab plan creation. Once a lab plan is created, you will see and read-only connection to the connected virtual network.</br>If you need to use another virtual network, create a new lab plan configured with the new virtual network.|
|Labs portal experience|Labs are lab listed under lab accounts in [https://labs.azure.com](https://labs.azure.com).|Labs are listed under resource group name in [https://labs.azure.com](https://labs.azure.com).</br>If there are multiple lab plans in the same resource group, instructors will be able to choose which lab plan to use when creating the lab.|
|Permissions needed to manage labs|To create a lab, administrator must assign:</br>- **Lab Contributor** role on the lab account.</br>To modify an existing lab, administrator must assign:</br>- **Reader** role on the lab account.</br>- **Lab Creator** or **Contributor** role on the lab.|To create a lab, administrator must assign:</br>- **Owner** or **Contributor** role on the resource group that contains the lab plan.</br>- **Lab Creator** role on the lab plan.</br>To modify an existing lab, administrator must assign:</br>- **Contributor** role on the lab.|

### Migrate from lab account to lab plan

To use new features provided in the public preview, you will need to create new lab plans and labs. When you create a lab plan, you can reuse the same Shared Image Gallery and images that you previously used with your lab account.  Likewise, you can reuse the same licensing server. As you migrate, there likely will be a period when you are using both the public preview and the current version of Azure Lab Services at the same time. You may have both lab accounts and lab plans that coexist in your subscription and that access the same Shared Image Gallery and licensing server.

For each new lab plan, there are some settings that you will need to configure, such as:

- Assign user permissions on the lab plan, the lab plan’s resource group, and the lab.
- Enable the Marketplace and Shared Image Gallery images that lab creators can use.
- Select regions that your labs will be deployed in.
- Set auto-shutdown settings.

With all the new enhancements in the January 2022 Update, this is a good time to revisit your overall lab structure. For example, you may decide to structure your lab plans differently than your lab accounts now that you can explicitly select the regions that labs are deployed in.

### Configure a lab plan

Once the lab plan is [created](how-to-manage-lab-plans.md), administrators can set up configurations as needed on the lab plan.

Depending on your scenario, more than one lab plan may be required.  For example, the math department may only require one lab plan in one resource group.  However, the computer science department may require a couple lab plan.  One lab plan may enable advanced networking and enable a few custom images from Shared Image Gallery.  Another lab plan may use basic network and not enable custom images.  Both lab plans can be kept in the same resource group.

Restrictions and configurations that apply at lab creation:

- Which region(s) the labs can be created in.
- What marketplace images are allowed.
- What custom images from a connected Azure Compute Gallery are allowed.
- Default auto-shutdown settings that labs will inherit.
- Specify your organization’s Azure Compute Gallery to export custom VM images to.
- Provide internal support information for your organization when using Azure Lab Services.
- Give access to educators to create and/or manage labs.

Configurations that apply at to all labs:

- Provide internal support information for your organization when using Azure Lab Services.

Remember, changes made to the lab settings from the lab plan will apply only to new labs created after the settings change is saved.

## Next Steps

- [Create a lab plan](tutorial-setup-lab-plan.md).
- [Manage your lab plan](how-to-manage-lab-plans.md).
- [Set up a classroom lab](tutorial-setup-classroom-lab.md)
