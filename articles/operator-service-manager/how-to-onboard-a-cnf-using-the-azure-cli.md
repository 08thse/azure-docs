---
title: How to onboard a CNF using the Azure Operator Service Manager CLI extension
description: Learn how to onboard a CNF using the Azure Operator Service Manager CLI extension.
author: peterwhiting
ms.author: peterwhiting
ms.date: 03/18/2024
ms.topic: how-to
ms.service: azure-operator-service-manager
ms.custom: devx-track-azurecli

---
# Onboard a Containerized Network Function (CNF) to Azure Operator Service Manager (AOSM)

In this how-to guide, Network Function Publishers and Service Designers learn how to use the Azure CLI extension to onboard a containerized network function to AOSM. Onboarding is a multi-step process. Once you meet the prerequisites, you'll use the Azure CLI AOSM extension to:

1. Convert your Helm charts and values.yaml into BICEP files that define a Network Function Definition (NFD).
1. Publish the NFD and upload the CNF images to an AOSM-managed Azure Container Registry (ACR).
1. Add your published NFD to the BICEP files that define a Network Service Design (NSD).
1. Publish the NSD.

## Prerequisites

- You have [enabled AOSM](quickstart-onboard-subscription-to-aosm.md) on your Azure subscription.

### Configure Permissions

- You require the Contributor role over your subscription in order to create a Resource Group, or an existing Resource Group where you have the Contributor role.
- You require the `Reader`/`AcrPull` role assignments on the source ACR containing your images.
- For the fastest image transfers, you require the `Contributor` and `AcrPush` role assignments on the subscription that will contain the AOSM managed Artifact Store. Alternatively, you can use the `--no-subscription-permissions` parameter when publishing images. This parameter means you don't require subscription scoped permissions.

### Helm Packages

The Helm packages you intend to onboard must be present on the local storage of the machine from which you're executing the CLI.

> [!NOTE]
> It is strongly recommended that the Helm package contains a schema for the helm values, that the helm package templates as you expect when `helm template` is run using the values.yaml you intend to use in onboarding to AOSM, and that you have tested that `helm install` succeeds on your target Arc-connected Kubernetes environment.

### Register necessary resource providers

Before you begin using the Azure Operator Service Manager, make sure to register the required resource providers. Execute the following commands. This registration process can take up to 5 minutes.

```azurecli
# Register Resource Provider
az provider register --namespace Microsoft.ContainerRegistry
```

> [!NOTE]
> It may take a few minutes for the resource provider registration to complete.

You can verify that your subscription is registered to use AOSM by running the following commands:

### Verify the registration status of the resource providers

Execute the following commands:

```azurecli
# Query the Resource Provider
az provider show -n Microsoft.ContainerRegistry --query "{RegistrationState: registrationState, ProviderName: namespace}"
```

### Download and install Azure CLI

Use the Bash environment in the Azure cloud shell. For more information, see [Start the Cloud Shell](/azure/cloud-shell/quickstart?tabs=azurecli) to use Bash environment in Azure Cloud Shell.

For users that prefer to run CLI reference commands locally refer to [How to install the Azure CLI](/cli/azure/install-azure-cli).

If you're running on Window or macOS, consider running Azure CLI in a Docker container. For more information, see [How to run the Azure CLI in a Docker container](/cli/azure/run-azure-cli-docker).

If you're using a local installation, sign into the Azure CLI using the `az login` command and complete the prompts displayed in your terminal to finish authentication. For more sign-in options, refer to [Sign in with Azure CLI](/cli/azure/authenticate-azure-cli).

### Install Azure Operator Service Manager (AOSM) CLI extension

Install the Azure Operator Service Manager (AOSM) CLI extension using this command:

```azurecli
az extension add --name aosm
```

1. Run `az version` to see the version and dependent libraries that are installed.
2. Run `az upgrade` to upgrade to the current version of Azure CLI.

### Download and install Helm and the Docker engine

Install Helm and the Docker engine by following the linked documents:

- [Install Helm CLI](https://helm.sh/docs/intro/install/)
- [Install the Docker Engine](https://docs.docker.com/engine/install/)

## Build the Network Function Definition (NFD)

This section creates a folder in the working directory called `cnf-cli-output`. This folder contains the BICEP templates of the Azure Operator Service Manager resources that define a Network Function Definition and the Artifact Store that will be used to store the container images. This Network Function Definition templates a Network Function that can be deployed using AOSM once it has been included in a Network Service Design.

1. Sign in to Azure using the Azure CLI and set the default subscription.

```azurecli
az login
az account set --subscription <subscription>
```

1. Generate the Azure CLI AOSM extension input file for a CNF.

```azurecli
az aosm nfd generate-config --definition-type cnf --output-file <filename.jsonc>
```

1. Open the input file you generated in the previous step and use the inline comments to enter the required values. This example shows the Az CLI AOSM extension input file for a fictional contoso CNF.

```json
{
    // Azure location to use when creating resources e.g uksouth
    "location": "eastus",
    // Name of the Publisher resource you want your definition published to.
    // Will be created if it does not exist.
    "publisher_name": "contoso",
    // Resource group for the Publisher resource.
    // You should create this before running the publish command.
    "publisher_resource_group_name": "contoso",
    // Name of the ACR Artifact Store resource.
    // Will be created if it does not exist.
    "acr_artifact_store_name": "contoso-artifact-store",
    // Name of the network function.
    "nf_name": "contoso-cnf-nfd",
    // Version of the network function definition in 1.1.1 format (three integers separated by dots).
    "version": "1.0.0",
    // Source of container images to be included in the CNF. Currently only one source is supported.
    "images": {
        // Login server of the source acr registry from which to pull the image(s).
        // For example sourceacr.azurecr.io.
        "source_registry": "contoso.azurecr.io",
        // Optional. Namespace of the repository of the source acr registry from which to pull.
        // For example if your repository is samples/prod/nginx then set this to samples/prod.
        // Leave as empty string if the image is in the root namespace.
        // See https://learn.microsoft.com/en-us/azure/container-registry/container-registry-best-practices#repository-namespaces for further details.
        "source_registry_namespace": ""
    },
    // List of Helm packages to be included in the CNF.
    "helm_packages": [
        {
            // The name of the Helm package.
            "name": "contoso-helm-package",
            // The file path to the helm chart on the local disk, relative to the directory from which the command is run.
            // Accepts .tgz, .tar or .tar.gz, or an unpacked directory. Use Linux slash (/) file separator even if running on Windows.
            "path_to_chart": "/home/cnf-onboard/contoso-cnf-helm-chart-0-1-0.tgz",
            // The file path (absolute or relative to this configuration file) of YAML values file on the local disk which will be used instead of the values.yaml file present in the helm chart.
            // Accepts .yaml or .yml. Use Linux slash (/) file separator even if running on Windows.
            "default_values": "",
            // Names of the Helm packages this package depends on.
            // Leave as an empty array if there are no dependencies.
            "depends_on": [
            ]
        }
    ]
}
```

1. Execute the following command to build the Network Function Definition.

```azurecli
az aosm nfd build --definition-type cnf --config-file <filename.jsonc>
```

## Publish the Network Function Definition (NFD)

This step creates the Azure Operator Service Manager resources that define the Network Function Definition and the Artifact Store that will be used to store the Network Function's container images. It also uploads those images to the Artifact Store either by copying them directly from the source ACR or, if you don't have subscription scope `Contributor` and `AcrPush` roles, by retagging the docker images locally and uploading to the Artifact Store using tightly scoped credentials generated from the AOSM service.

1. Execute the following command to publish the Network Function Definition. If you don't have subscription scope `Contributor` and `AcrPush` roles, include `--no-subscription-permissions` in the command.

```azurecli
az aosm nfd publish --build-output-folder cnf-cli-output --definition-type cnf
```

## Build the Network Service Design (NSD)

This section creates a folder in the working directory called `nsd-cli-output`. This folder contains the BICEP templates of the Azure Operator Service Manager resources that define a Network Service Design. This Network Service Design templates a Site Network Service resource that will deploy the Network Function you onboarded in the previous sections.

1. Generate the Azure CLI AOSM Extension NSD input file.

```azurecli
az aosm nsd generate-config --output-file <nsd-output-filename.jsonc>
```

1. Open the input file you generated in the previous step and use the inline comments to enter the required values. This example shows the Az CLI AOSM extension input file for a fictional contoso NSD that can be used to deploy a fictional contoso CNF onto an Arc-connected Kubernetes cluster.

```json
{
    // Azure location to use when creating resources e.g uksouth
    "location": "eastus",
    // Name of the Publisher resource you want your definition published to.
    // Will be created if it does not exist.
    "publisher_name": "contoso",
    // Resource group for the Publisher resource.
    // You should create this before running the publish command.
    "publisher_resource_group_name": "contoso",
    // Name of the ACR Artifact Store resource.
    // Will be created if it does not exist.
    "acr_artifact_store_name": "contoso-artifact-store",
    // Network Service Design (NSD) name. This is the collection of Network Service Design Versions. Will be created if it does not exist.
    "nsd_name": "contoso-nsd",
    // Version of the NSD to be created. This should be in the format A.B.C
    "nsd_version": "1.0.0",
    // Optional. Description of the Network Service Design Version (NSDV).
    "nsdv_description": "An NSD that deploys the onboarded contoso-cnf NFD",
    // Type of NFVI (for nfvisFromSite). Defaults to 'AzureCore'.
    // Valid values are 'AzureCore', 'AzureOperatorNexus' or 'AzureArcKubernetes.
    "nfvi_type": "AzureArcKubernetes",
    // List of Resource Element Templates.
    "resource_element_templates": [
        {
            // Type of Resource Element. Either NF or ArmTemplate
            "resource_element_type": "NF",
            "properties": {
                // The name of the existing publisher for the NSD.
                "publisher": "contoso",
                // The resource group that the publisher is hosted in.
                "publisher_resource_group": "contoso",
                // The name of the existing Network Function Definition Group to deploy using this NSD.
                "name": "contoso-cnf-nfd",
                // The version of the existing Network Function Definition to base this NSD on.
                // This NSD will be able to deploy any NFDV with deployment parameters compatible with this version.
                "version": "1.0.0",
                // The region that the NFDV is published to.
                "publisher_offering_location": "eastus",
                // Type of Network Function. Valid values are 'cnf' or 'vnf'.
                "type": "cnf",
                // Set to true or false. Whether the NSD should allow arbitrary numbers of this type of NF. If false only a single instance will be allowed. Only supported on VNFs, must be set to false on CNFs.
                "multiple_instances": "false"
            }
        }
    ]
}
```

>[!NOTE]
> The resource element template section defines which NFD is included in the NSD. The properties must match those used in the input file passed to the `az aosm nfd build` command. This is because the Azure CLI AOSM Extension validates that the NFD has been correctly onboarded when building the NSD.

1. Execute the following command to build the Network Service Definition.

```azurecli
az aosm nsd build --config-file <nsd-output-filename.jsonc>
```

## Publish the Network Service Design (NSD)

This step creates the Azure Operator Service Manager resources that define the Network Service Definition. It also uploads artifacts required by the NSD to the Artifact Store.

1. Execute the following command to publish the Network Service Definition. If you don't have subscription scope `Contributor` and `AcrPush` roles, include `--no-subscription-permissions` in the command.

```azurecli
az aosm nsd publish --build-output-folder nsd-cli-output
```
