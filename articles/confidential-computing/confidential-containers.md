---
 title: Confidential containers on Azure
 description: Learn about unmodified container support with confidential containers.
 services: container-service
 author: agowdamsft
 ms.topic: article
 ms.date: 11/1/2021
 ms.author: amgowda
 ms.service: container-service
---

# Confidential containers on Azure

Container deployments looking for secured, attestable, and isolated space helps the overall security for your container deployments. Confidential computing capability on Azure provides:

- Data integrity 
- Data confidentiality
- Code integrity
- Container code protection through encryption
- Hardware-based assurances
- Allow running existing apps
- Create hardware root of trust

A hardware-based Trusted Execution Environment (TEE) is an important component, used to provide strong security assurances. These assurances occur through hardware and software measurements from trusted computing base (TCB) components. Confidential containers offerings on Azure allow you to verify these measurements, and validate if the container apps run in a safe, trusted, and verifiable execution environment.

Confidential containers support custom applications developed with any programming language. They also support off-the-shelf Docker container apps.

![confidential container protection boundary](./media/confidential-containers/sgx-confidential-container.jpg)

## Enclave Confidential Container enablers with Intel SGX on Azure Kubernetes Service (AKS)

To run an existing docker container, applications on confidential computing nodes require an abstraction layer or SGX software to use the special CPU instruction set. The SGX software also enables your sensitive applications code to be protected and create a direct execution to CPU to remove the Guest OS, Host OS, or Hypervisor. This protection reduces the overall surface attack areas and vulnerabilities with operating system or hypervisor layers.

Confidential containers are fully supported on Azure Kubernetes Service (AKS) and enabled through Azure Partners and Open Source Software (OSS) projects. Developers can choose software providers based on the features, integration to Azure services and tooling support.

Below is the overall process for taking existing containers and running them confidentially on AKS.

![The confidential container conversion](./media/confidential-containers/confidential-containers-deploy-steps.jpg)

## Partner Enablers
> [!NOTE]
> The below solutions are offered through Azure Partners and may incur licensing fees. Please verify the partner software terms independently. 

### Fortanix

[Fortanix](https://www.fortanix.com/) offers developers a choice of a portal and CLI-based experience to bring their containerized applications and covert them to SGX capable confidential containers without any need to modify or recompile the application. Fortanix provides the flexibility to run and manage the broadest set of applications, including existing applications, new enclave-native applications, and pre-packaged applications. Users can start with [Enclave Manager](https://em.fortanix.com/) UI or [REST APIs](https://www.fortanix.com/api/em/) to create confidential containers by following the [Quick Start](https://support.fortanix.com/hc/en-us/articles/360049658291-Fortanix-Confidential-Container-on-Azure-Kubernetes-Service) guide for Azure Kubernetes Service.

![Fortanix Deployment Process](./media/confidential-containers/fortanix-confidential-containers-flow.png)

### Scone (Scontain)

[SCONE](https://scontain.com/index.html?lang=en) supports security policies that can generate certificates, keys, and secrets, and ensures they are only visible to attested services of an application. In this way, the services of an application automatically attest each other via TLS - without the need to modify the applications nor TLS. This is explained with the help of a simple
Flask application here: https://sconedocs.github.io/flask_demo/  

SCONE can convert existing most binaries into applications that run inside of enclaves without needing to change the application or to recompile that application. SCONE also protects interpreted languages like Python by encrypting both data files and Python code files. With the help of a SCONE security policy, one can protect the encrypted files against unauthorized accesses, modifications, and rollbacks. How to "sconify" an existing Python application is explained [here](https://sconedocs.github.io/sconify_image/)

![Scontain Flow](./media/confidential-containers/scone-workflow.png)

Scone deployments on confidential computing nodes with AKS are fully supported and integrated. Get started with a sample application here https://sconedocs.github.io/aks/

### Anjuna

[Anjuna](https://www.anjuna.io/) provides SGX platform software that enables you to run unmodified containers on AKS. Learn more on the functionality and check out the sample applications [here](https://www.anjuna.io/microsoft-azure-confidential-computing-aks-lp).

Get started with a sample Redis Cache and Python Custom Application [here](https://www.anjuna.io/microsoft-azure-confidential-computing-aks-lp)

![Anjuna Process](./media/confidential-containers/anjuna-process-flow.png)

## OSS Enablers 
> [!NOTE]
> The below solutions are offered through Open Source Projects and are not directly affiliated with Azure Confidential Computing (ACC) or Microsoft.  

### Gramine

[Gramine](https://grapheneproject.io/) is a lightweight guest OS, designed to run a single Linux application with minimal host requirements. Gramine can run applications in an isolated environment with benefits comparable to running a complete OS and has good tooling support for converting existing docker container application to Gramine Shielded Containers (GSC).

Get started with a sample application and deployment on AKS [here](https://graphene.readthedocs.io/en/latest/cloud-deployment.html#azure-kubernetes-service-aks)

### Occlum
[Occlum](https://occlum.io/) is a memory-safe, multi-process library OS (LibOS) for Intel SGX. It enables legacy applications to run on SGX with little to no modifications to source code. Occlum transparently protects the confidentiality of user workloads while allowing an easy lift and shift to existing docker applications.

Occlum supports AKS deployments. Follow the deployment instructions with various sample apps [here](https://github.com/occlum/occlum/blob/master/docs/azure_aks_deployment_guide.md)

### Marblerun
[Marblerun](https://marblerun.sh/) is an orchestration framework for confidential containers. It makes it easy to run and scale confidential services on SGX-enabled Kubernetes. Marblerun takes care of boilerplate tasks like verifying the services in your cluster, managing secrets for them, and establishing enclave-to-enclave mTLS connections between them. Marblerun also ensures that your cluster of confidential containers adheres to a manifest defined in simple JSON. The manifest can be verified by external clients via remote attestation.

In a nutshell, Marblerun extends the confidentiality, integrity, and verifiability properties of a single enclave to a Kubernetes cluster.

Marblerun supports confidential containers created with Graphene, Occlum, and EGo. Examples for each SDK are given [here](https://docs.edgeless.systems/marblerun/#/examples?id=examples). Marblerun is built to run on Kubernetes and alongside your existing cloud-native tooling. It comes with an easy-to-use CLI and helm charts. It has first-class support for confidential computing nodes on AKS. Information on how to deploy Marblerun on AKS can be found [here](https://docs.edgeless.systems/marblerun/#/deployment/cloud?id=cloud-deployment).

## Confidential Containers Demo
View the confidential healthcare demo with confidential containers. Sample is available [here](https://github.com/Azure-Samples/confidential-container-samples/blob/main/confidential-healthcare-scone-confinf-onnx/README.md). 

> [!VIDEO https://www.youtube.com/embed/PiYCQmOh0EI]


## Get In Touch

Have questions with your implementation or want to become an enabler? Send an email to acconaks@microsoft.com

## Reference Links

[Deploy AKS cluster with Intel SGX Confidential VM Nodes](./confidential-enclave-nodes-aks-get-started.md)

[Microsoft Azure Attestation](../attestation/overview.md)

[Intel SGX Confidential Virtual Machines](virtual-machine-solutions-sgx.md)

[Azure Kubernetes Service (AKS)](../aks/intro-kubernetes.md)