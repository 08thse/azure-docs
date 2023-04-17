# Failover Considerations for Storage Accounts with Private Endpoints

**NOTES**
>*  We need to test failover
>*  Datalake is not supported

Storage Accounts operate differently than other services.  Instead of having a redundant node, like Azure SQL Multi-region HA, or a secondary web app setup for redundancy, Storage Accounts replicate to a secondary region and fail over in an emergency.
That means that you don't plan to have a second SA (you could but its uncommon and you have to solve for file sync and pay more), you plan to fail over your SA.
The Storage Account depends on DNS for routing if you are doing a Private Endpoint
You need a secondary set of PrivateLink DNS Zones for Storage Services in your secondary region so that the resources in your secondary region can resolve to the PE in your secondary region, for the storage account that has been failed over.
If doing active/active, have PEs in both prestaged and able to handle traffic.  PEs do not need to be in the same region as the Storage Account.  If DR only, you still want them there, but you have less to consider.

If you have a very high Recovery Time Objective (6 hours) - You can make changes to the environment to help with the failover and not have the zone.  But do you want to be messing with DNS during an outage - no way.

To be safe you need:

1. Two Privatelink DNS zones for the storage services - one in primary one in secondary
2. Have two Private Endpoints - one primary, one in secondary - for each storage service
3. Enable DNS Zone Group for Primary-to-Primary and Secondary-to-Secondary

![Image of PE environment](./media/storage-resiliency-privateendpoint/privateendpointenvironment.png)

Scenario 1 - Storage Account Failover 

The Storage Account fails over to the paired region, but the network routing stays the same.

The Private Endpoint should not be impacted, and the service continues to run in the primary region unchanged.

No DNS consideration appears to be needed.

Scenario 2 - Other Services Failover

Due to an environmental issue, the primary region is unable to support the resources that connect to the Storage Account.  VMs, Function Apps, or whatever.  But networking and the storage account are still possible.

Resources redeployed in the secondary region can route through Hub to Hub to access the Private Endpoint in the Secondary Region.  They can use the same DNS in both Primary and Secondary Regions

This is probably very uncommon.

Scenario 3 - Whole Region Outage

The primary region is unavailable.  The storage account fails over to the secondary region, and then the customer redeploys/fails over their resources to the secondary region.

In this event, the 

Scenario 4 - Running in HA

Scenario 5 - Connecting from On-Prem

On-Prem:

If the on-prem DNS service has a conditional forwarder to Azure Primary Zone, part of the DR Failover to the secondary Zone must involve changing the forwarder to the Secondary Zone, IF IT IS NEEDED IN A DR SCENARIO

Alternatively, Jump Boxes can be used to access the secondary environment as a degraded service in line with their RLO.
