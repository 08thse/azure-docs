---
title: VNet flow logs (preview)
titleSuffix: Azure Network Watcher
description: Learn about VNet flow logs feature of Azure Network Watcher. 
author: halkazwini
ms.author: halkazwini
ms.service: network-watcher
ms.topic: conceptual
ms.date: 03/12/2023
ms.custom: template-concept, engagement-fy23
---

# VNet flow logs (preview)

Virtual network (VNet) flow logs is a feature of Azure Network Watcher that allows you to log information about IP traffic flowing through a virtual network. Flow data is sent to Azure Storage from where you can access it and export it to any visualization tool, security information and event management (SIEM) solution, or intrusion detection system (IDS) of your choice. Network Watcher VNet flow logs capability overcomes some of the existing limitations of [NSG flow logs](network-watcher-nsg-flow-logging-overview.md).

## Why use flow logs?

It's vital to monitor, manage, and know your network so that you can protect and optimize it. You may need to know the current state of the network, who's connecting, and where users are connecting from. You may also need to know which ports are open to the internet, what network behavior is expected, what network behavior is irregular, and when sudden rises in traffic happen.

Flow logs are the source of truth for all network activity in your cloud environment. Whether you're in a startup that's trying to optimize resources or a large enterprise that's trying to detect intrusion, flow logs can help. You can use them for optimizing network flows, monitoring throughput, verifying compliance, detecting intrusions, and more.

## Common use cases

#### Network monitoring

- Identify unknown or undesired traffic.
- Monitor traffic levels and bandwidth consumption.
- Filter flow logs by IP and port to understand application behavior.
- Export flow logs to analytics and visualization tools of your choice to set up monitoring dashboards.

#### Usage monitoring and optimization

- Identify top talkers in your network.
- Combine with GeoIP data to identify cross-region traffic.
- Understand traffic growth for capacity forecasting.
- Use data to remove overly restrictive traffic rules.

#### Compliance

- Use flow data to verify network isolation and compliance with enterprise access rules.

#### Network forensics and security analysis

- Analyze network flows from compromised IPs and network interfaces.
- Export flow logs to any SIEM or IDS tool of your choice.

## How logging works

Key properties of VNet flow logs include:

- Flow logs operate at Layer 4 of the Open Systems Interconnection (OSI) model and record all IP flows going in and out of a virtual network.
- Logs are collected at 1-minute intervals through the Azure platform and don't affect your Azure resources or network traffic.
- Logs are written in the JSON (JavaScript Object Notation) format 
- Each log record contains the network interface (NIC) the flow applies to, 5-tuple information, traffic direction, flow state, encryption state & throughput information. See [Log Format](#log-format) for more details.

## Log format

:::image type="content" source="./media/vnet-flow-logs-overview/vnet-flow-log-format.png" alt-text="Table showing the format of a virtual network flow log.":::

VNet flow logs have the following properties:

- `time`: Time in UTC when the event was logged. 
- `flowLogVersion`: Version of flow log schema.
- `flowLogGUID`: The resource GUID of the FlowLog resource. 
- `macAddress`: MAC address of the network interface where the event was captured. 
- `category`: Category of the event. The category is always `FlowLogFlowEvent`. 
- `flowLogResourceID`: Resource ID of the FlowLog resource. 
- `targetResourceID`: Resource ID of target resource associated to the FlowLog resource. 
- `operationName`: Always `FlowLogFlowEvent`. 
- `flowRecords`: Collection of flow records.
	- `flows`: Collection of flows. This property has multiple entries for different ACLs. 
		- `aclID`: GUID that identifies the network security group resource. For cases like traffic denied by encryption, this value is `unspecified`.
		- `flowGroups`: Collection of flow records at a rule level. 
			- `rule`: Name of the rule that allowed or denied the traffic. For traffic denied due to encryption, this value is `unspecified`. 
			- `flowTuples`: string that contains multiple properties for the flow tuple in a comma-separated format:
				- `Time Stamp`: Time stamp of when the flow occurred in UNIX epoch format.
				- `Source IP`: Source IP address.
				- `Destination IP`: Destination IP address.
				- `Source port`: Source port.
				- `Destination port`: Destination Port.
				- `Protocol`: Layer 4 protocol of the flow expressed in IANA assigned values.
				- `Flow direction`: Direction of the traffic flow. Valid values are `I` for inbound and `O` for outbound. 
				- `Flow state`: State of the flow. Possible states are:
					- `B`: Begin, when a flow is created. No statistics are provided.
					- `C`: Continuing for an ongoing flow. Statistics are provided at 5-minute intervals.
					- `E`: End, when a flow is terminated. Statistics are provided.
					- `D`: Deny, when a flow is denied. 
				- `Flow encryption`: Encryption state of the flow. Possible values are:
					- `X`: Encrypted.
					- `NX`: Unencrypted.
					- `NX_HW_NOT_SUPPORTED`: Unsupported hardware.
					- `NX_SW_NOT_READY`: Software not ready.
					- `NX_NOT_ACCEPTED`: Drop due to no encryption.
					- `NX_NOT_SUPPORTED`: Discovery not supported.
					- `NX_LOCAL_DST`: Destination on same host.
					- `NX_FALLBACK`: Fallback to no encryption. 
				- `Packets sent`: Total number of packets sent from source to destination since the last update. 
				- `Bytes sent`: Total number of packet bytes sent from source to destination since the last update. Packet bytes include the packet header and payload. 
				- `Packets received`: Total number of packets sent from destination to source since the last update. 
				- `Bytes received`: Total number of packet bytes sent from destination to source since the last update. Packet bytes include packet header and payload. 

## Sample log records

In the following examples of VNet flow log, multiple records that follow the property list described earlier.

```json
{
    "records": [
        {
            "time": "2022-09-14T09:00:52.5625085Z",
            "flowLogVersion": 4,
            "flowLogGUID": "daf543bb-8d76-4221-8e66-2f874f3625eb",
            "macAddress": "00224871C205",
            "category": "FlowLogFlowEvent",
            "flowLogResourceID": "/SUBSCRIPTIONS/AF15E575-F948-49AC-BCE0-252D028E9379/RESOURCEGROUPS/NETWORKWATCHERRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKWATCHERS/NETWORKWATCHER_EASTUS2EUAP/FLOWLOGS/VNETFLOWLOG",
            "targetResourceID": "/subscriptions/af15e575-f948-49ac-bce0-252d028e9379/resourceGroups/trdey-rg/providers/Microsoft.Network/virtualNetworks/vnetfl",
            "operationName": "FlowLogFlowEvent",
            "flowRecords": {
                "flows": [
                    {
                        "aclID": "1aa8c000-fd32-4a0d-b3c6-c7c80fcfa6fa",
                        "flowGroups": [
                            {
                                "rule": "DefaultRule_AllowInternetOutBound",
                                "flowTuples": [
                                    "1663146003599,10.0.0.6,52.239.184.180,23956,443,6,O,B,NX,0,0,0,0",
                                   "1663146003606,10.0.0.6,52.239.184.180,23956,443,6,O,E,NX,3,767,2,1580",
                                    "1663146003637,10.0.0.6,40.74.146.17,22730,443,6,O,B,NX,0,0,0,0",
                                    "1663146003640,10.0.0.6,40.74.146.17,22730,443,6,O,E,NX,3,705,4,4569",
                                    "1663146004251,10.0.0.6,40.74.146.17,22732,443,6,O,B,NX,0,0,0,0",
                                    "1663146004251,10.0.0.6,40.74.146.17,22732,443,6,O,E,NX,3,705,4,4569",
                                    "1663146004622,10.0.0.6,40.74.146.17,22734,443,6,O,B,NX,0,0,0,0",
                                    "1663146004622,10.0.0.6,40.74.146.17,22734,443,6,O,E,NX,2,134,1,108",
                                    "1663146017343,10.0.0.6,104.16.218.84,36776,443,6,O,B,NX,0,0,0,0",
                                    "1663146022793,10.0.0.6,104.16.218.84,36776,443,6,O,E,NX,22,2217,33,32466"
                                ]
                            }
                        ]
                    },
                    {
                        "aclID": "082de8d8-b438-49ef-b093-30606cdffc8a",
                        "flowGroups": [
                            {
                                "rule": "BlockHighRiskTCPPortsFromInternet",
                                "flowTuples": [
                                    "1663145998065,101.33.218.153,10.0.0.6,55188,22,6,I,D,NX,0,0,0,0",
                                    "1663146005503,192.241.200.164,10.0.0.6,35276,119,6,I,D,NX,0,0,0,0"
                                ]
                            },
                            {
                                "rule": "Internet",
                                "flowTuples": [
                                    "1663145989563,20.106.221.10,10.0.0.6,50557,44357,6,I,D,NX,0,0,0,0",
                                    "1663145989679,20.55.117.81,10.0.0.6,62797,35945,6,I,D,NX,0,0,0,0",
                                    "1663145989709,20.55.113.5,10.0.0.6,51961,65515,6,I,D,NX,0,0,0,0",
                                    "1663145990049,13.65.224.51,10.0.0.6,40497,40129,6,I,D,NX,0,0,0,0",
                                    "1663145990145,20.55.117.81,10.0.0.6,62797,30472,6,I,D,NX,0,0,0,0",
                                    "1663145990175,20.55.113.5,10.0.0.6,51961,28184,6,I,D,NX,0,0,0,0",
                                    "1663146015545,20.106.221.10,10.0.0.6,50557,31244,6,I,D,NX,0,0,0,0"
                                ]
                            }
                        ]
                    }
                ]
            }
        }
    ]
}






```

## Next steps


