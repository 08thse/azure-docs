---
title: Quality checks
description: Provides an overview of Azure Large Instances for Epic quality checks.
ms.topic: conceptual
author: jjaygbay
ms.author: jacobjaygbay
ms.service: baremetal-infrastructure
ms.date: 06/01/2023
---


Microsoft operations team performs a series of extensive quality checks to ensure that customers request to run Epic systems on Azure ALI is fulfilled accurately, and infrastructure is healthy before handover. 
However, customers are advised to perform their own checks to ensure services are provided as requested. 
This article identifies recommended checks.  

 

* Basic connectivity  


Latency check.  

Server health check from operating system.  

OS level sanity checks / configuration checks.  

Here are a few areas where quality checks are usually performed by Microsoft teams before the infrastructure handover to the customer.  

 

Network   

 

IP blade information.  

 

Access control list on firewall.

Compute  

 

number of processors and cores for servers.     

 

Accuracy of memory size for the assigned server.             

 

Latest firmware version on the blades.  

 
Storage   

 

size of boot LUN and FC LUNs are as per Epic on Azure Large Instances standard configuration.        

 

SAN configuration.            

 

required VLANs creation.           


Operating System  

 

Accuracy of LUNs.   



Epic on Azure BMI - post handover support request flow  

Source: Epic_BMI_SupportIncidentWorkflow.vsdx 
         
## Next steps

Learn how to identify and interact with ALI instances through the Azure portal.

> [!div class="nextstepaction"]
> [What is Azure for Large Instances?](../../what-is-azure-for-large-instances.md)
