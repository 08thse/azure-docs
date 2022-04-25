---
title: "Quickstart: Deploy your code to Azure Container Apps"
description: Code to cloud deploying your application to Azure Container Apps
services: container-apps
author: cebundy
ms.service: container-apps
ms.topic: quickstart
ms.date: 04/04/2022
ms.author: v-bcatherine
zone_pivot_groups: container-apps-image-build-type
---

# Quickstart: Deploy your code to Azure Container Apps

This article demonstrates how to deploy to Azure Container Apps from a repository on your machine in the language of your choice.

This quickstart is the first in a series of articles that walk you through how to use core capabilities within Azure Container Apps. The first step is to create a backend web API for an application that returns a static collection of music albums.

## Prerequisites

To complete this project, you'll need the following items:

::: zone pivot="acr-remote"

| Requirement  | Instructions |
|--|--|
| Azure account | If you don't have one, [create an account for free](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). You need the *Contributor* or *Owner* permission on the Azure subscription to proceed. <br><br>Refer to [Assign Azure roles using the Azure portal](/azure/role-based-access-control/role-assignments-portal?tabs=current) for details. |
| GitHub Account | Sign up for [free](https://github.com/join). |
| git | [Install git](https://git-scm.com/downloads) |
| Azure CLI | Install the [Azure CLI](/cli/azure/install-azure-cli).|

::: zone-end

::: zone pivot="docker-local"

| Requirement  | Instructions |
|--|--|
| Azure account | If you don't have one, [create an account for free](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). You need the *Contributor* or *Owner* permission on the Azure subscription to proceed. Refer to [Assign Azure roles using the Azure portal](/azure/role-based-access-control/role-assignments-portal?tabs=current) for details. |
| GitHub Account | Sign up for [free](https://github.com/join). |
| git | [Install git](https://git-scm.com/downloads) |
| Azure CLI | Install the [Azure CLI](/cli/azure/install-azure-cli).|
| Docker engine | Install the Docker Engine. Docker provides packages that configure the Docker environment on [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/), and [Linux](https://docs.docker.com/engine/installation/#supported-platforms). <br><br>From your command prompt, type `docker` to ensure Docker is running. |

::: zone-end

[!INCLUDE [container-apps-setup-cli-only.md](../../includes/container-apps-setup-cli-only.md)]

Now Azure CLI setup is validated, you can next define the initial environment variables used throughout this article.

[!INCLUDE [container-apps-code-to-cloud-setup.md](../../includes/container-apps-code-to-cloud-setup.md)]

### Set your language preference

Set an environment variable for the language you'll be using. The value can be one of the following values:

- csharp
- go
- javascript
- python

# [Bash](#tab/bash)

```bash
LANGUAGE="<LANGUAGE_VALUE>"
```

# [PowerShell](#tab/powershell)

```powershell
$LANGUAGE="<LANGUAGE_VALUE>"
```

---

Before you run this command, replace `<LANGUAGE_VALUE>` with one of the following values: `csharp`, `go`, `javascript`, or `python`.

Next, set an environment variable for the target port of your application.  If you're using the JavaScript API, the target port should be set to `3000`.  Otherwise, set your target port to `80`.

# [Bash](#tab/bash)

```bash
API_PORT="<TARGET_PORT>"
```

# [PowerShell](#tab/powershell)

```powershell
$API_PORT="<TARGET_PORT>"
```

---

Before you run this command, replace `<TARGET_PORT>` with `3000` for node apps, or `80` for all other apps.


## Prepare the GitHub repository

Navigate to the repository for your preferred language and fork the repository.

Select the **Fork** button at the top of the page to fork the repo to your account.

* [C#](https://github.com/azure-samples/containerapps-albumapi-csharp)
* [Go](https://github.com/azure-samples/containerapps-albumapi-go)
* [JavaScript](https://github.com/azure-samples/containerapps-albumapi-javascript)
* [Python](https://github.com/azure-samples/containerapps-albumapi-python)

Now you can clone your fork of the sample repository.

Use the following git command to clone your forked repo into the *code-to-cloud* folder:

```git
git clone https://github.com/$GITHUB_USERNAME/containerapps-albumapi-$LANGUAGE.git code-to-cloud
```

> [!NOTE]
> If the `clone` command fails, then you probably forgot to first fork the repository.

Next, change the directory into the root of the cloned repo.

```console
cd code-to-cloud
```

---

## Create an Azure Resource Group

Create a resource group to organize the services related to your container app deployment.

# [Bash](#tab/bash)

```azurecli
az group create \
  --name $RESOURCE_GROUP \
  --location "$LOCATION"
```

# [PowerShell](#tab/powershell)

```powershell
New-AzResourceGroup -Name $RESOURCE_GROUP -Location $LOCATION
```

---

## Create an Azure Container Registry

Next, create an Azure Container Registry (ACR) registry instance in your new resource group.

# [Bash](#tab/bash)

```azurecli
az acr create \
  --resource-group $RESOURCE_GROUP \
  --name $ACR_NAME \
  --sku Basic \
  --admin-enabled true
```

Now store your ACR password in an environment variable.

```azurecli
ACR_PASSWORD=$(az acr credential show -n $ACR_NAME --query passwords[0].value)
```

# [PowerShell](#tab/powershell)

```powershell
$ACA_REGISTRY = New-AzContainerRegistry `
    -ResourceGroupName $RESOURCE_GROUP `
    -Name $ACR_NAME `
    -EnableAdminUser `
    -Sku Basic
```

Now store your ACR password in an environment variable.

```powershell
$ACR_PASSWORD=(Get-AzContainerRegistryCredential `
 -ResourceGroupName $RESOURCE_GROUP `
 -Name $ACR_NAME | Select -Property Password).Password
```

---

Sign in to your container registry.

# [Bash](#tab/bash)

```azurecli
az acr login --name $ACR_NAME
```

# [PowerShell](#tab/powershell)

```powershell
Connect-AzContainerRegistry -Name $ACR_NAME
```

---

::: zone pivot="acr-remote"

## Build your application

With ACR Tasks, you can build the docker image for the album API without the need to install Docker locally.  You'll find the Dockerfile in the root directory of the GitHub repo that you use to create the container image.

### Build the container with ACR

The following command uses ACR to remotely build the Dockerfile for the album API. The `.` represents the current build context, so run this command at the root of the repository where the Dockerfile is located.

# [Bash](#tab/bash)

```azurecli
az acr build --registry $ACR_NAME --image $API_NAME .
```

# [PowerShell](#tab/powershell)

```powershell
az acr build --registry $ACR_NAME --image $API_NAME .
```

---

Output from the `az acr build` command shows the upload progress of the source code to Azure and the details of the `docker build` operation.

To verify that your image is now available in ACR, run the command below.

# [Bash](#tab/bash)

```azurecli
az acr manifest list-metadata --registry $ACR_NAME --name $API_NAME
```

# [PowerShell](#tab/powershell)

```powershell
Get-AzContainerRegistryManifest -RegistryName $ACR_NAME `
  -Repository $API_NAME
```

---

::: zone-end

::: zone pivot="docker-local"

## Build your application

In the below steps, you build your container image locally using Docker. Once the image is built successfully, you push the image to your newly created container registry.

### Build the container with Docker

The following command builds the image using the Dockerfile for the album API. The `.` represents the current build context, so run this command at the root of the repository where the Dockerfile is located.

# [Bash](#tab/bash)

```azurecli
docker build -t $ACR_NAME.azurecr.io/$API_NAME .
```

# [PowerShell](#tab/powershell)

```powershell
docker build -t $ACR_NAME.azurecr.io/$API_NAME .
```

---

If your image was successfully built, it's listed in the output of the following command:

# [Bash](#tab/bash)

```azurecli
docker images
```

# [PowerShell](#tab/powershell)

```powershell
docker images
```

---

### Push the Docker image to your ACR registry

First, sign in to your Azure Container Registry.

# [Bash](#tab/bash)

```azurecli
az acr login --name $ACR_NAME
```

# [PowerShell](#tab/powershell)

```powershell
az acr login --name $ACR_NAME
```

---

Now, push the image to your registry.

# [Bash](#tab/bash)

```azurecli
docker push $ACR_NAME.azurecr.io/$API_NAME
```

# [PowerShell](#tab/powershell)

```powershell
docker push $ACR_NAME.azurecr.io/$API_NAME
```

---

::: zone-end

## Create a Container Apps environment

The Azure Container Apps environment acts as a secure boundary around a group of container apps.

Create the Container Apps environment using the following command.

# [Bash](#tab/bash)

```azurecli
az containerapp env create \
  --name $ENVIRONMENT \
  --resource-group $RESOURCE_GROUP \
  --location "$LOCATION"
```

# [PowerShell](#tab/powershell)

```azurecli
az containerapp env create `
  --name $ENVIRONMENT `
  --resource-group $RESOURCE_GROUP `
  --location $LOCATION
```

---

## Deploy your image to a container app

Now that you have an environment created, you can create and deploy your container app with the `az containerapp create` command.

By setting `--ingress` to `external`, your container app will be accessible from the public internet.

Create and deploy your container app with the following command.

# [Bash](#tab/bash)

```azurecli
az containerapp create \
  --name $API_NAME \
  --resource-group $RESOURCE_GROUP \
  --environment $ENVIRONMENT \
  --image $ACR_NAME.azurecr.io/$API_NAME \
  --target-port $API_PORT \
  --ingress 'external' \
  --registry-password $ACR_PASSWORD \
  --registry-username $ACR_NAME \
  --registry-server $ACR_NAME.azurecr.io \
  --query configuration.ingress.fqdn
```

# [PowerShell](#tab/powershell)

```azurecli
az containerapp create `
  --name $API_NAME `
  --resource-group $RESOURCE_GROUP `
  --environment $ENVIRONMENT `
  --image $API_NAME `
  --target-port $API_PORT `
  --ingress 'external' `
  --query configuration.ingress.fqdn
```

---

## Verify deployment

The `az containerapp create` command returns the fully qualified domain name for the container app. Copy this location to a web browser.

From your web browser, navigate to the FQDN on the `/albums` endpoint.

## Clean up resources

If you're not going to continue on to the [Communication between microservices](communicate-between-microservices.md) tutorial, you can remove the Auzre resources created during this quickstart. Run the following command to delete the resource group along with all the resources created in this quickstart.

# [Bash](#tab/bash)

```azurecli
az group delete --name $RESOURCE_GROUP
```

# [PowerShell](#tab/powershell)

```powershell
Remove-AzResourceGroup -Name $RESOURCE_GROUP -Force
```

---

> [!TIP]
> Having issues? Let us know on GitHub by opening an issue in the [Azure Container Apps repo](https://github.com/microsoft/azure-container-apps).

## Next steps

This quickstart is the entrypoint for a set of progressive tutorials that showcase the various features within Azure Container Apps. To continue with the tutorial path, `ADD LINKS`.

> [!div class="nextstepaction"]
> [Environments in Azure Container Apps](environment.md)