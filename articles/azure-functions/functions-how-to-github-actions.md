---
title: Use GitHub Actions to make code updates in Azure Functions
description: Learn how to use GitHub Actions to define a workflow to build and deploy Azure Functions projects in GitHub.
ms.topic: conceptual
ms.date: 05/10/2023
ms.custom: "devx-track-csharp, devx-track-python, github-actions-azure"
---

# Continuous delivery by using GitHub Actions

Use [GitHub Actions](https://github.com/features/actions) to define a workflow to automatically build and deploy code to your function app in Azure Functions. 

In GitHub Actions, a [workflow](https://docs.github.com/en/actions/learn-github-actions/introduction-to-github-actions#the-components-of-github-actions) is an automated process that you define in your GitHub repository. This process tells GitHub how to build and deploy your function app project on GitHub. 

There are four options for deploying a function with GitHub Actions: 

1. Use the Deployment option in the Azure portal 
1. Manually create your GitHub Actions workflow. 
1. Use Azure CLI to generate a workflow file and deploy your app.   
1. Use an Actions template within GitHub.  

All four approaches will involve adding a YAML (.yml) file in the `/.github/workflows/` path in your repository. This definition contains the various steps and parameters that make up the workflow. 

For an Azure Functions workflow, the file has three sections: 

| Section | Tasks |
| ------- | ----- |
| **Authentication** | Download a publish profile.<br/>Create a GitHub secret.|
| **Build** | Set up the environment.<br/>Build the function app.|
| **Deploy** | Deploy the function app.|

## Prerequisites

- An Azure account with an active subscription. [Create an account for free](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- A GitHub account. If you don't have one, sign up for [free](https://github.com/join).  
- A working function app hosted on Azure with a GitHub repository.   
    - [Quickstart: Create a function in Azure using Visual Studio Code](./create-first-function-vs-code-csharp.md)


## Use the Deployment option in Azure portal

You can get started quickly with GitHub Actions through the Deployment tab when you create a function in Azure portal. You can also add GitHub Actions to an existing function app. This will automatically generate a workflow file based on your application stack and commit it to your GitHub repository in the correct directory.

To add a GitHub Actions workflow when you create a new function app:

1. Select **Deployment** in the **Create Function App** flow. 

    :::image type="content" source="media/functions-how-to-github-actions/github-actions-deployment.png" alt-text="Screenshot of Deployment option in Functions menu.":::

1. Enable **Continuous Deployment** if you want each code update to trigger a code push to Azure portal. 

1. Enter your GitHub organization, repository, and branch. 

    :::image type="content" source="media/functions-how-to-github-actions/github-actions-github-account-details.png" alt-text="Screenshot of GitHub user account details.":::

1. Complete configuring your function app. Your GitHub repository now includes a new workflow file in `/.github/workflows/`. 

To add a GitHub Actions workflow to an existing function app:

1. Navigate to your function app in the Azure portal.
1. Select **Deployment Center**. 
1. Under Continuous Deployment (CI / CD), select **GitHub**. You'll see a default message, *Building with GitHub Actions*. 
1. Enter your GitHub organization, repository, and branch. 
1. Select **Preview file** to see the workflow file that will be added to your GitHub repository in `github/workflows/`.
1. Select **Save** to add the workflow file to your repository. 


## Manually create your GitHub Actions workflow

### Generate deployment credentials

The recommended way to authenticate with Azure Functions for GitHub Actions is by using a publish profile. You can also authenticate with a service principal. To learn more, see [this GitHub Actions repository](https://github.com/Azure/functions-action). 

After saving your publish profile credential as a [GitHub secret](https://docs.github.com/en/actions/reference/encrypted-secrets), you'll use this secret within your workflow to authenticate with Azure. 

#### Download your publish profile

To download the publishing profile of your function app:

1. Select the function app's **Overview** page, and then select **Get publish profile**.

   :::image type="content" source="media/functions-how-to-github-actions/get-publish-profile.png" alt-text="Download publish profile":::

1. Save and copy the contents of the file.


#### Add the GitHub secret

1. In [GitHub](https://github.com/), go to your repository.

1. Go to **Settings**. 

1. Select **Secrets and variables > Actions**.

1. Select **New repository secret**.

1. Add a new secret with the name `AZURE_FUNCTIONAPP_PUBLISH_PROFILE` and the value set to the contents of the publishing profile file.

1. Select **Add secret**.

GitHub can now authenticate to your function app in Azure.

### Create the environment 

Setting up the environment is done using a language-specific publish setup action.

# [.NET](#tab/dotnet)

.NET (including ASP.NET) uses the `actions/setup-dotnet` action.  
The following example shows the part of the workflow that sets up the environment:

```yaml
    - name: Setup DotNet 6.0.x Environment
      uses: actions/setup-dotnet@v3
      with:
        dotnet-version: 6.0.x
```

# [Java](#tab/java)

Java uses the  `actions/setup-java` action.  
The following example shows the part of the workflow that sets up the environment:

```yaml
    - name: Setup Java 17
      uses: actions/setup-java@3
      with:
        distribution: 'zulu' # See 'Supported distributions' for available options in setup-java-jdk
        java-version: '17'
```

# [JavaScript](#tab/javascript)

JavaScript (Node.js) uses the `actions/setup-node` action.  
The following example shows the part of the workflow that sets up the environment:

```yaml

    - name: Setup Node 16.x Environment
      uses: actions/setup-node@v3
      with:
        node-version: '16.x'
```

# [Python](#tab/python)

Python uses the `actions/setup-python` action.  
The following example shows the part of the workflow that sets up the environment:

```yaml
    - name: Setup Python 3.9 Environment
      uses: actions/setup-python@v4
      with:
        python-version: '3.9'
```

# [PowerShell](#tab/powershell)

This step can be skipped for PowerShell as the GitHub runner already includes PowerShell.

---

### Build the function app

This depends on the language and for languages supported by Azure Functions, this section should be the standard build steps of each language.

The following example shows the part of the workflow that builds the function app, which is language-specific:

# [.NET](#tab/dotnet)

```yaml
    env:
      AZURE_FUNCTIONAPP_PACKAGE_PATH: '.' # set this to the path to your web app project, defaults to the repository root

    - name: 'Resolve Project Dependencies Using Dotnet'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        dotnet build --configuration Release --output ./output
        popd
```

# [Java](#tab/java)

```yaml
    env:
      POM_XML_DIRECTORY: '.'  # set this to the directory which contains pom.xml file

    - name: 'Restore Project Dependencies Using Mvn'
      shell: bash
      run: |
        pushd './${{ env.POM_XML_DIRECTORY }}'
        mvn clean package
        mvn azure-functions:package
        popd
```

# [JavaScript](#tab/javascript)

```yaml
    env:
      AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'  # set this to the path to your web app project, defaults to the repository root

    - name: 'Resolve Project Dependencies Using Npm'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
```

# [Python](#tab/python)

```yaml
    env:
      AZURE_FUNCTIONAPP_PACKAGE_PATH: '.' # set this to the path to your web app project, defaults to the repository root

    - name: 'Resolve Project Dependencies Using Pip'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        python -m pip install --upgrade pip
        pip install -r requirements.txt --target=".python_packages/lib/site-packages"
        popd
```

# [PowerShell](#tab/powershell)

This step can be skipped for PowerShell as there is no need to build.

---

### Deploy the function app

Use the `Azure/functions-action` action to deploy your code to a function app. This action has three parameters:

|Parameter |Explanation  |
|---------|---------|
|_**app-name**_ | (Mandatory) The name of your function app. |
|_**slot-name**_ | (Optional) The name of the [deployment slot](functions-deployment-slots.md) you want to deploy to. The slot must already be defined in your function app. |
|_**publish-profile**_ | (Optional) The name of the GitHub secret for your publish profile. |

The following example uses version 1 of the `functions-action` and a `publish profile` for authentication 

# [.NET](#tab/dotnet)

Set up a .NET Linux workflow that uses a publish profile.

```yaml
name: Deploy DotNet project to function app with a Linux environment

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name  # set this to your application's name
  AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'    # set this to the path to your web app project, defaults to the repository root
  DOTNET_VERSION: '6.0.x'                # set this to the dotnet version to use

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    - name: 'Checkout GitHub action'
      uses: actions/checkout@v3

    - name: Setup DotNet ${{ env.DOTNET_VERSION }} Environment
      uses: actions/setup-dotnet@v3
      with:
        dotnet-version: ${{ env.DOTNET_VERSION }}

    - name: 'Resolve Project Dependencies Using Dotnet'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        dotnet build --configuration Release --output ./output
        popd
    - name: 'Run Azure Functions action'
      uses: Azure/functions-action@v1
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: '${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}/output'
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}
```
Set up a .NET Windows workflow that uses a publish profile.

```yaml
name: Deploy DotNet project to function app with a Windows environment

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name  # set this to your application's name
  AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'    # set this to the path to your web app project, defaults to the repository root
  DOTNET_VERSION: '6.0.x'                # set this to the dotnet version to use

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:
    - name: 'Checkout GitHub action'
      uses: actions/checkout@v3

    - name: Setup DotNet ${{ env.DOTNET_VERSION }} Environment
      uses: actions/setup-dotnet@v3
      with:
        dotnet-version: ${{ env.DOTNET_VERSION }}

    - name: 'Resolve Project Dependencies Using Dotnet'
      shell: pwsh
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        dotnet build --configuration Release --output ./output
        popd
    - name: 'Run Azure Functions action'
      uses: Azure/functions-action@v1
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: '${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}/output'
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}
```

# [Java](#tab/java)

Set up a Java Linux workflow that uses a publish profile.

```yaml
name: Deploy Java project to function app

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name      # set this to your function app name on Azure
  POM_XML_DIRECTORY: '.'                     # set this to the directory which contains pom.xml file
  POM_FUNCTIONAPP_NAME: your-app-name        # set this to the function app name in your local development environment
  JAVA_VERSION: '17'                         # set this to the java version to use

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    - name: 'Checkout GitHub action'
      uses: actions/checkout@v3

    - name: Setup Java Sdk ${{ env.JAVA_VERSION }}
      uses: actions/setup-java@v3
      with:
        distribution: 'zulu'
        java-version: ${{ env.JAVA_VERSION }}

    - name: 'Restore Project Dependencies Using Mvn'
      shell: bash
      run: |
        pushd './${{ env.POM_XML_DIRECTORY }}'
        mvn clean package
        mvn azure-functions:package
        popd
    - name: 'Run Azure Functions action'
      uses: Azure/functions-action@v1
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: './${{ env.POM_XML_DIRECTORY }}/target/azure-functions/${{ env.POM_FUNCTIONAPP_NAME }}'
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}
```

Set up a Java Windows workflow that uses a publish profile.

```yaml
name: Deploy Java project to function app

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name      # set this to your function app name on Azure
  POM_XML_DIRECTORY: '.'                     # set this to the directory which contains pom.xml file
  POM_FUNCTIONAPP_NAME: your-app-name        # set this to the function app name in your local development environment
  JAVA_VERSION: '17'                         # set this to the Java version to use

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:
    - name: 'Checkout GitHub action'
      uses: actions/checkout@v3

    - name: Setup Java Sdk ${{ env.JAVA_VERSION }}
      uses: actions/setup-java@v3
      with:
        distribution: 'zulu'
        java-version: ${{ env.JAVA_VERSION }}

    - name: 'Restore Project Dependencies Using Mvn'
      shell: pwsh
      run: |
        pushd './${{ env.POM_XML_DIRECTORY }}'
        mvn clean package
        mvn azure-functions:package
        popd
    - name: 'Run Azure Functions action'
      uses: Azure/functions-action@v1
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: './${{ env.POM_XML_DIRECTORY }}/target/azure-functions/${{ env.POM_FUNCTIONAPP_NAME }}'
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}
```

# [JavaScript](#tab/javascript)

Set up a Node.JS Linux workflow that uses a publish profile.

```yaml
name: Deploy Node.js project to function app

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name    # set this to your application's name
  AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  NODE_VERSION: '16.x'                     # set this to the node version to use (supports 14.x, 16.x, 18.x)

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    - name: 'Checkout GitHub action'
      uses: actions/checkout@v3

    - name: Setup Node ${{ env.NODE_VERSION }} Environment
      uses: actions/setup-node@v3
      with:
        node-version: ${{ env.NODE_VERSION }}

    - name: 'Resolve Project Dependencies Using Npm'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
    - name: 'Run Azure Functions action'
      uses: Azure/functions-action@v1
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: ${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}
```

Set up a Node.JS Windows workflow that uses a publish profile.

```yaml
name: Deploy Node.js project to function app

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name    # set this to your application's name
  AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  NODE_VERSION: '16.x'                     # set this to the node version to use (supports 14.x, 16.x, 18.x)

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:
    - name: 'Checkout GitHub action'
      uses: actions/checkout@v3

    - name: Setup Node ${{ env.NODE_VERSION }} Environment
      uses: actions/setup-node@v3
      with:
        node-version: ${{ env.NODE_VERSION }}

    - name: 'Resolve Project Dependencies Using Npm'
      shell: pwsh
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
    - name: 'Run Azure Functions action'
      uses: Azure/functions-action@v1
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: ${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}

```
# [Python](#tab/python)

Set up a Python Linux workflow that uses a publish profile.

```yaml
name: Deploy Python project to function app

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name # set this to your application's name
  AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'   # set this to the path to your web app project, defaults to the repository root
  PYTHON_VERSION: '3.9'                 # set this to the Python version to use (supports 3.8, 3.9, 3.10)

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    - name: 'Checkout GitHub action'
      uses: actions/checkout@v3

    - name: Setup Python ${{ env.PYTHON_VERSION }} Environment
      uses: actions/setup-python@v4
      with:
        python-version: ${{ env.PYTHON_VERSION }}

    - name: 'Resolve Project Dependencies Using Pip'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        python -m pip install --upgrade pip
        pip install -r requirements.txt --target=".python_packages/lib/site-packages"
        popd
    - name: 'Run Azure Functions action'
      uses: Azure/functions-action@v1
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: ${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}
```

# [PowerShell](#tab/powershell)

Set up a Windows workflow that uses a publish profile.

```yaml
name: Deploy PowerShell project to function app

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name # set this to your application's name
  AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'   # set this to the path to your web app project, defaults to the repository root

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:
      - name: 'Checkout GitHub Action'
        uses: actions/checkout@v2

      - name: 'Run Azure Functions Action'
        uses: Azure/functions-action@v1
        id: fa
        with:
          app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
          slot-name: 'Production'
          package: ${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}
          publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}

```

---

## Use Azure CLI to create a GitHub Actions workflow file 

In your existing repository with a functions app, run the `az functionapp deployment github-actions add` command to create a workflow file and have it get added to your repository.

```azurecli
az functionapp deployment github-actions add --repo
                                             [--branch]
                                             [--build-path]
                                             [--force]
                                             [--ids]
                                             [--login-with-github]
                                             [--name]
                                             [--resource-group]
                                             [--runtime]
                                             [--runtime-version]
                                             [--slot]
                                             [--subscription]
                                             [--token]
```

To use the interactive method to retrieve a personal access token:

1. Run this `az functionapp` command, replacing the values `githubUser/githubRepo`, `MyResourceGroup`, and `MyFunctionapp`.
    ```azurecli
    az functionapp deployment github-actions add --repo "githubUser/githubRepo" -g MyResourceGroup -n MyFunctionapp --login-with-github
    ```

1. In your terminal window, you'll see the message, "Please navigate to https://github.com/login/device and enter the user code 1234-ABCD to activate and retrieve your github personal access token." Your values will be different from `1234-ABDC`. 

1. Go to <https://github.com/login/device> and enter your unique code. 

1. After entering your code, you should see a message like this:

    ```
    Verified GitHub repo and branch
    Getting workflow template using runtime: java
    Filling workflow template with name: func-app-123, branch: main, version: 8, slot: production, build_path: .
    Adding publish profile to GitHub
    Fetching publish profile with secrets for the app 'func-app-123'
    Creating new workflow file: .github/workflows/master_func-app-123.yml
    ```

1. Go to your GitHub repository and select **Actions**. Verify that your workflow ran. 

## Use an Actions template

You can also use functions templates from within GitHub Actions. 

1. In [GitHub](https://github.com/), go to your repository.

1. Go to **Actions**. 

1. Select **New workflow**. 

1. Search for *functions*. 

    :::image type="content" source="media/functions-how-to-github-actions/github-actions-functions-templates.png" alt-text="Screenshot of search for GitHub Actions functions templates. ":::

1. Select **Configure** and use one of the functions app workflows authored by Microsoft Azure. 


## Next steps

> [!div class="nextstepaction"]
> [Learn more about Azure and GitHub integration](/azure/developer/github/)
