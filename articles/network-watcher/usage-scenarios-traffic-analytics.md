---
title: Usage Scenarios | Microsoft Docs
description: This article describes the usage scenarios of Traffic Analytics.
services: network-watcher
documentationcenter: na
author: v-ssenthilna

ms.service: network-watcher
ms.topic: article
ms.tgt_pltfrm: na
ms.workload:  infrastructure-services
ms.date: 05/11/2022
ms.author: v-ssenthilna
ms.custom: references_regions, devx-track-azurepowershell
---
# Usage scenarios

Some of the insights you might want to gain after Traffic Analytics is fully configured, are as follows:

## Find traffic hotspots

**Look for**

- Which hosts, subnets, virtual networks and virtual machine scale set are sending or receiving the most traffic, traversing maximum malicious traffic and blocking significant flows?
    - Check comparative chart for hosts, subnet, virtual network and virtual machine scale set. Understanding which hosts, subnets, virtual networks and virtual machine scale set are sending or receiving the most traffic can help you identify the hosts that are processing the most traffic, and whether the traffic distribution is done properly.
    - You can evaluate if the volume of traffic is appropriate for a host. Is the volume of traffic normal behavior, or does it merit further investigation?
- How much inbound/outbound traffic is there?
    -   Is the host expected to receive more inbound traffic than outbound, or vice-versa?
- Statistics of blocked traffic.
    - Why is a host blocking a significant volume of benign traffic? This behavior requires further investigation and probably optimization of configuration
- Statistics of malicious allowed/blocked traffic
  - Why is a host receiving malicious traffic and why are flows from malicious sources allowed? This behavior requires further investigation and probably optimization of configuration.

    Select **See all**, under **IP**, as shown in the following picture:

    ![Dashboard showcasing host with most traffic details](media/traffic-analytics/dashboard-showcasing-host-with-most-traffic-details.png)

    The following picture shows time trending for the top five talking hosts and the flow-related details (allowed – inbound/outbound and denied - inbound/outbound flows) for a host:

	Select **See more**, under **Details of top 5 talking IPs**, as shown in the following picture to get insights about all the hosts:

    ![Top five most-talking host trend](media/traffic-analytics/top-five-most-talking-host-trend.png)
	

**Look for**

- Which are the most conversing host pairs?
    - Expected behavior like front-end or back-end communication or irregular behavior, like back-end internet traffic.
- Statistics of allowed/blocked traffic
    - Why a host is allowing or blocking significant traffic volume
- Most frequently used application protocol among most conversing host pairs:
    - Are these applications allowed on this network?
    - Are the applications configured properly? Are they using the appropriate protocol for communication? Select **See all** under **Frequent conversation**, as show in the following picture:

        ![Dashboard showcasing most frequent conversation](./media/traffic-analytics/dashboard-showcasing-most-frequent-conversation.png)

- The following picture shows time trending for the top five conversations and the flow-related details such as allowed and denied inbound and outbound flows for a conversation pair:

    ![Top five chatty conversation details and trend](./media/traffic-analytics/top-five-chatty-conversation-details-and-trend.png)

**Look for**

- Which application protocol is most used in your environment, and which conversing host pairs are using the application protocol the most?
    - Are these applications allowed on this network?
    - Are the applications configured properly? Are they using the appropriate protocol for communication? Expected behavior is common ports such as 80 and 443. For standard communication, if any unusual ports are displayed, they might require a configuration change. Select **See all** under **Application port**, in the following picture:

        ![Dashboard showcasing top application protocols](./media/traffic-analytics/dashboard-showcasing-top-application-protocols.png)

- The following pictures show time trending for the top five L7 protocols and the flow-related details (for example, allowed and denied flows) for an L7 protocol:

    ![Top five layer 7 protocols details and trend](./media/traffic-analytics/top-five-layer-seven-protocols-details-and-trend.png)

    ![Flow details for application protocol in log search](./media/traffic-analytics/flow-details-for-application-protocol-in-log-search.png)

**Look for**

- Capacity utilization trends of a VPN gateway in your environment.
    - Each VPN SKU allows a certain amount of bandwidth. Are the VPN gateways underutilized?
    - Are your gateways reaching capacity? Should you upgrade to the next higher SKU?
- Which are the most conversing hosts, via which VPN gateway, over which port?
    - Is this pattern normal? Select **See all** under **VPN gateway**, as shown in the following picture:

        ![Dashboard showcasing top active VPN connections](./media/traffic-analytics/dashboard-showcasing-top-active-vpn-connections.png)

- The following picture shows time trending for capacity utilization of an Azure VPN Gateway and the flow-related details (such as allowed flows and ports):

    ![VPN gateway utilization trend and flow details](./media/traffic-analytics/vpn-gateway-utilization-trend-and-flow-details.png)

## Visualize traffic distribution by geography

**Look for**

- Traffic distribution per data center such as top sources of traffic to a datacenter, top rogue networks conversing with the data center, and top conversing application protocols.
  - If you observe more load on a data center, you can plan for efficient traffic distribution.
  - If rogue networks are conversing in the data center, then correct NSG rules to block them.

    Select **View map** under **Your environment**, as shown in the following picture:

    ![Dashboard showcasing traffic distribution](./media/traffic-analytics/dashboard-showcasing-traffic-distribution.png)

- The geo-map shows the top ribbon for selection of parameters such as data centers (Deployed/No-deployment/Active/Inactive/Traffic Analytics Enabled/Traffic Analytics Not Enabled) and countries/regions contributing Benign/Malicious traffic to the active deployment:

    ![Geo map view showcasing active deployment](./media/traffic-analytics/geo-map-view-showcasing-active-deployment.png)

- The geo-map shows the traffic distribution to a data center from countries/regions and continents communicating to it in blue (Benign traffic) and red (malicious traffic) colored lines:

    ![Geo map view showcasing traffic distribution to countries/regions and continents](./media/traffic-analytics/geo-map-view-showcasing-traffic-distribution-to-countries-and-continents.png)

    ![Flow details for traffic distribution in log search](./media/traffic-analytics/flow-details-for-traffic-distribution-in-log-search.png)
	
- The **More Insight** blade of a Azure region also shows the total traffic remaining inside that region (i.e. source and destination in same region). It further gives insights about traffic exchanged between availability zones of a datacenter 

	![Inter Zone and Intra region traffic](./media/traffic-analytics/inter-zone-and-intra-region-traffic.png)

## Visualize traffic distribution by virtual networks

**Look for**

- Traffic distribution per virtual network, topology, top sources of traffic to the virtual network, top rogue networks conversing to the virtual network, and top conversing application protocols.
  - Knowing which virtual network is conversing to which virtual network. If the conversation is not expected, it can be corrected.
  - If rogue networks are conversing with a virtual network, you can correct NSG rules to block the rogue networks.
 
    Select **View VNets** under **Your environment** as shown in the following picture:

    ![Dashboard showcasing virtual network distribution](./media/traffic-analytics/dashboard-showcasing-virtual-network-distribution.png)

- The Virtual Network Topology shows the top ribbon for selection of parameters like a virtual network's (Inter virtual network Connections/Active/Inactive), External Connections, Active Flows, and Malicious flows of the virtual network.
- You can filter the Virtual Network Topology based on subscriptions, workspaces, resource groups and time interval. Additional filters that help you understand the flow are:
  Flow Type (InterVNet, IntraVNET, and so on), Flow Direction (Inbound, Outbound), Flow Status (Allowed, Blocked), VNETs (Targeted and Connected), Connection Type (Peering or Gateway - P2S and S2S), and NSG. Use these filters to focus on VNets that you want to examine in detail.
- You can zoom-in and zoom-out while viewing Virtual Network Topology using mouse scroll wheel. Left-click and moving the mouse lets you drag the topology in desired direction. You can also use keyboard shortcuts to achieve these actions: A (to drag left), D (to drag right), W (to drag up), S (to drag down), + (to zoom in), - (to zoom out), R (to zoom reset).
- The Virtual Network Topology shows the traffic distribution to a virtual network with regard to flows (Allowed/Blocked/Inbound/Outbound/Benign/Malicious), application protocol, and network security groups, for example:

    ![Virtual network topology showcasing traffic distribution and flow details](./media/traffic-analytics/virtual-network-topology-showcasing-traffic-distribution-and-flow-details.png)
    
    ![Virtual network topology showcasing top level and more filters](./media/traffic-analytics/virtual-network-filters.png)

    ![Flow details for virtual network traffic distribution in log search](./media/traffic-analytics/flow-details-for-virtual-network-traffic-distribution-in-log-search.png)

**Look for**

- Traffic distribution per subnet, topology, top sources of traffic to the subnet, top rogue networks conversing to the subnet, and top conversing application protocols.
    - Knowing which subnet is conversing to which subnet. If you see unexpected conversations, you can correct your configuration.
    - If rogue networks are conversing with a subnet, you are able to correct it by configuring NSG rules to block the rogue networks.
- The Subnets Topology shows the top ribbon for selection of parameters such as Active/Inactive subnet, External Connections, Active Flows, and Malicious flows of the subnet.
- You can zoom-in and zoom-out while viewing Virtual Network Topology using mouse scroll wheel. Left-click and moving the mouse lets you drag the topology in desired direction. You can also use keyboard shortcuts to achieve these actions: A (to drag left), D (to drag right), W (to drag up), S (to drag down), + (to zoom in), - (to zoom out), R (to zoom reset).
- The Subnet Topology shows the traffic distribution to a virtual network with regard to flows (Allowed/Blocked/Inbound/Outbound/Benign/Malicious), application protocol, and NSGs, for example:

    ![Subnet topology showcasing traffic distribution a virtual network subnet with regards to flows](./media/traffic-analytics/subnet-topology-showcasing-traffic-distribution-to-a-virtual-subnet-with-regards-to-flows.png)

**Look for**

Traffic distribution per Application gateway & Load Balancer, topology, top sources of traffic, top rogue networks conversing to the Application gateway & Load Balancer, and top conversing application protocols. 
    
 - Knowing which subnet is conversing to which Application gateway or Load Balancer. If you observe unexpected conversations, you can correct your configuration.
 - If rogue networks are conversing with an Application gateway or Load Balancer, you are able to correct it by configuring NSG rules to block the rogue networks. 

    ![Screenshot shows a subnet topology with traffic distribution to an application gateway subnet with regard to flows.](./media/traffic-analytics/subnet-topology-showcasing-traffic-distribution-to-a-application-gateway-subnet-with-regards-to-flows.png)

## View ports and virtual machines receiving traffic from the internet

**Look for**

- Which open ports are conversing over the internet?
  - If unexpected ports are found open, you can correct your configuration:

    ![Dashboard showcasing ports receiving and sending traffic to the internet](./media/traffic-analytics/dashboard-showcasing-ports-receiving-and-sending-traffic-to-the-internet.png)

    ![Details of Azure destination ports and hosts](./media/traffic-analytics/details-of-azure-destination-ports-and-hosts.png)

**Look for**

Do you have malicious traffic in your environment? Where is it originating from? Where is it destined to?

![Malicious traffic flows detail in log search](./media/traffic-analytics/malicious-traffic-flows-detail-in-log-search.png)

## View information about public IPs interacting with your deployment

**Look for**

- Which public IPs are conversing with my network? What is the WHOIS data and geographic location of all public IPs?
- Which malicious IPs are sending traffic to my deployments? What is the threat type and threat description for malicious IPs?
	- The Public IP Information section, gives a summary of all types of public IPs present in your network traffic. 
	  Select the public IP type of interest to view details. This [schema document](./traffic-analytics-schema.md#public-ip-details-schema) defines the data fields presented.
	  
	  :::image type="content" source="./media/traffic-analytics/public-ip-information.png" alt-text="Public IP information" lightbox="./media/traffic-analytics/public-ip-information.png":::
	  
	- On the traffic analytics dashboard, click on any IP to view its information	
	
	  :::image type="content" source="./media/traffic-analytics/external-public-ip-details.png" alt-text="external IP information in tool tip" lightbox="./media/traffic-analytics/external-public-ip-details.png":::
	  
	  :::image type="content" source="./media/traffic-analytics/malicious-ip-details.png" alt-text="malicious IP information in tool tip" lightbox="./media/traffic-analytics/malicious-ip-details.png":::

## Visualize the trends in NSG/NSG rules hits

**Look for**

- Which NSG/NSG rules have the most hits in comparative chart with flows distribution?
- What are the top source and destination conversation pairs per NSG/NSG rules?

    ![Dashboard showcasing NSG hits statistics](./media/traffic-analytics/dashboard-showcasing-nsg-hits-statistics.png)

- The following pictures show time trending for hits of NSG rules and source-destination flow details for a network security group:

  - Quickly detect which NSGs and NSG rules are traversing malicious flows and which are the top malicious IP addresses accessing your cloud environment
  - Identify which NSG/NSG rules are allowing/blocking significant network traffic
  - Select top filters for granular inspection of an NSG or NSG rules

    ![Showcasing time trending for NSG rule hits and top NSG rules](./media/traffic-analytics/showcasing-time-trending-for-nsg-rule-hits-and-top-nsg-rules.png)

    ![Top NSG rules statistics details in log search](./media/traffic-analytics/top-nsg-rules-statistics-details-in-log-search.png)
    
## Frequently asked questions

To get answers to frequently asked questions, see [Traffic analytics FAQ](traffic-analytics-faq.yml).

## Next steps

- To learn how to enable flow logs, see [Enabling NSG flow logging](network-watcher-nsg-flow-logging-portal.md).
- To understand the schema and processing details of Traffic Analytics, see [Traffic analytics schema](traffic-analytics-schema.md).