# Azure Spring Cloud Reference Architecture

## Overview
This architecture reference is a foundation leveraging a typical enterprise hub and spoke enterprise design for the use of Azure Spring Cloud. In the design, Spring Cloud is deployed in a single spoke which is dependent on shared services hosted in the hub. The architecture is built with components to achieve the tenants in the Well-Architected framework.

### Shared Services Hub

### Spring Cloud Spoke

## Well-Architected Framework Considerations
### Cost Optimization
### Operational Excellence
### Performance Efficiency
### Reliability
### Security
As one of the guiding tenants of the Azure Well-Architected Framework, security of this architecture was addressed by adhering to industry defined controls and benchmarks. The controls used in this architecture are from the Cloud Control Matrix (CCM) by the Cloud Security Alliance and the Microsoft Azure Foundations Benchmark (MAFB) by the Center for Internet Security. The primary security design principles of governance, networking, and application security were the focus of the applied controls. The design principles of Identity and Access Management and Storage are the responsibility of the reader as it relates to their target infrastructure.

#### Governance
The primary aspect of governance that this architecture addresses is segregation through isolation of network resources. In the CCM, DCS-08 recommends ingress and egress control for the datacenter. To satisfy the control, the architecture leverages a hub and spoke design using Network Security Groups (NSGs) to filter east-west traffic between resources, as well as traffic between central services in the hub and resources in the spoke. In addition, north-south traffic, particularly the flow between the Internet and the resources within the architecture, is managed through an instance of Azure Firewall.

| CSA CCM Control ID | CSA CCM Control Domain |
| :--------- | :--------------|
| DCS-08 | Datacenter Security Unauthorized Persons Entry |


#### Network
The network design supporting this architecture is derived from the traditional hub and spoke model. This decision ensures that network isolation is a foundational construct. CCM control IVS-06 recommends that traffic between networks and virtual machines are restricted and monitored between trusted and untrusted environments. This architecture adopts the control by implementation of the NSGs for east-west traffic, and the Azure Firewall for north-south traffic. CCM control IPY-04 recommends that the infrastructure should use secure network protocols for the exchange of data between services. The Azure services supporting this architecture all use standard secure protocols such as TLS for HTTP and SQL.

| CSA CCM Control ID | CSA CCM Control Domain |
| :--------- | :--------------|
| IPY-04 | Interoperability & Portability Standardized Network Protocols |
| IVS-06 | Infrastructure & Virtualization SecurityNetwork Security |

The network implementation is further secured by defining controls from the MAFB. The controls address restricting SSH from the Internet (6.2), restricting SQL database ingress from any 0.0.0.0/0 IP (6.3), ensuring that Network Watcher is enabled (6.5), and restricting UDP services from the Internet (6.6)

| CIS Control ID | CIS Control Description |
| :--------- | :------------------ |
| 6.2 | Ensure that SSH access is restricted from the internet |
| 6.3 | Ensure no SQL Databases allow ingress 0.0.0.0/0 (ANY IP) |
| 6.5 | Ensure that Network Watcher is 'Enabled' |
| 6.6 | Ensure that UDP Services are restricted from the Internet |

#### Application Security
This design principal is comprised of fundamental components which are identity, data protection, key management, and application configuration. For this reference architecture, the focus is on key management where identity, data protection, and application configuration are the responsibility of the reader as it relates to their target infrastructure, and application.

The controls that address key management in this reference from the CCM are the following:
| CSA CCM Control ID | CSA CCM Control Domain |
| :--------- | :-------------- |
| EKM-01 | Encryption and Key Management Entitlement |
| EKM-02 | Encryption and Key Management Key Generation |
| EKM-03 | Encryption and Key Management Sensitive Data Protection |
| EKM-04 | Encryption and Key Management Storage and Access |
