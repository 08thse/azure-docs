---
title: Create User-Defined Routes with Azure Virtual Network Manager
description: Learn how to deploy User-Defined Routes (UDRs) with Azure Virtual Network Manager using the Azure portal.
author: mbender-ms
ms.author: mbender
ms.service: virtual-network-manager
ms.topic: how-to
ms.date: 03/18/2023
#customer intent: As a network engineer, I want to deploy User-Defined Routes (UDRs) with Azure Virtual Network Manager.
---

# Create User-Defined Routes (UDRs) in Azure Virtual Network Manager

In this article, you learn how to deploy [User-Defined Routes (UDRs)](./concept-udr-management.md) with Azure Virtual Network Manager using the Azure portal. UDRs allow you to describe your desired routing behavior, and Virtual Network Manager orchestrates UDRs to create and maintain that behavior. You'll deploy all the resources needed to create UDRs, including:
    - a Virtual Network Manager instance
    - two virtual networks and a network group
    - a routing configuration to create UDRs for the network group

## Prerequisites

- An Azure account with an active subscription. [Create an account for free](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- You need to have the **Network Contributor Role** for the scope that you want to use. 

## Create a Virtual Network Manager instance

Deploy a Virtual Network Manager instance with the defined scope and access that you need. 

1. Sign in to the [Azure portal](https://portal.azure.com/).

1. Select **+ Create a resource** and search for **Network Manager**. Then select **Network Manager** > **Create** to begin setting up Virtual Network Manager.

1. On the **Basics** tab, enter or select the following information, and then select **Review + create**.

    | Setting | Value |
    | ------- | ----- |
    | **Subscription** | Select the subscription where you want to deploy Virtual Network Manager. |
    | **Resource group** | Select **Create new** and enter **rg-vnm**.</br> Select **Ok**. |
    | **Name** | Enter **vnm-1**. |
    | **Region** | Select **(US) East US** or a region of your choosing. Virtual Network Manager can manage virtual networks in any region. The selected region is where the Virtual Network Manager instance will be deployed. |
    | **Description** | *(Optional)* Provide a description about this Virtual Network Manager instance and the task it's managing. |
    | [Features](concept-network-manager-scope.md#features) | Select **User defined routing** from the dropdown list. |

1. Select the **Management scope** tab or select **Next: Management scope >** to continue.
1. On the **Management scope** tab, select **+ Add**.
1. In the **Add scopes** pane, select your subscription then choose **Select**. 
1. Select **Review + create** and then select **Create** to deploy the Virtual Network Manager instance.

## Create virtual networks

In this step, you create two virtual networks to become members of a network group.

1. From the **Home** screen, select **+ Create a resource** and search for **Virtual network**. 
2. Select **Virtual network > Create** to begin configuring a virtual network.

3. On the **Basics** tab, enter or select the following information:

    | Setting | Value |
    | ------- | ----- |
    | **Subscription** | Select the subscription where you want to deploy this virtual network. |
    | **Resource group** | Select **rg-vnm**. |
    | **Virtual network name** | Enter **vnet-spoke-001**. |
    | **Region** | Select **(US) East US**. |

4. Select **Next > Next** or the **IP addresses** tab.
5. On the **IP addresses** tab, enter an IPv4 address range of **10.0.0.0** and **/16**.
6. Under **Subnets**, select **default** and enter the following information in the **Edit Subnet** window:

    | Setting | Value |
    | -------- | ----- |
    | **Subnet purpose** | Leave as **Default**. |
    | **Name** | Leave as **default**. |
    | **IPv4** | |
    | **IPv4 address range** | Select **10.0.0.0/16**. |
    | **Starting address** | Enter **10.0.1.0**. |
    | **Size** | Enter **/24 (256 addresses)**. |

    :::image type="content" source="media/how-to-deploy-user-defined-routes/edit-subnet.png" alt-text="Screenshot of subnet settings in Azure portal.":::

1. Select **Save** then **Review + create > Create**.

1. Return to home and repeat the preceding steps to create another virtual network with the following information:

    | Setting | Value |
    | ------- | ----- |
    | **Subscription** | Select the same subscription that you selected in step 2. |
    | **Resource group** | Select **rg-vnm**. |
    | **Virtual network name** | Enter **vnet-spoke-002**. |
    | **Region** | Select **(US) East US**. |
\   | **Edit subnet window** | |
    | **Subnet purpose** | Leave as **Default**. |
    | **Name** | Leave as **default**. |
    | **IPv4** | |
    | **IPv4 address range** | Select **10.1.0.0/16**. |
    | **Starting address** | Enter **10.1.1.0**. |
    | **Size** | Enter **/24 (256 addresses)**. |

1. Select **Save** then **Review + create > Create**.

## Create a network group with dynamic membership

In this step, you create a network group to contain your virtual networks.

1. From the **Home** page, select **Resource groups** and browse to the **rg-vnm** resource group, and select the **vnm-1** Virtual Network Manager instance.
1. Under **Settings**, select **Network groups**. Then select **Create**.
1. On the **Create a network group** pane, enter the following information:
   
    | Setting | Value |
    | ------- | ----- |
    | **Name** | Enter **ng-spoke**. |
    | **Description** | *(Optional)* Provide a description about this network group. |
    | **Member type** | Select **Virtual network**. |
1. Select **Create**.

## Create a routing configuration 


