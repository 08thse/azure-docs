---
title: Continuous regression testing in GitHub with Azure Load Testing
titleSuffix: Azure Load Testing
description: Continuous regression testing in GitHub with Azure Load Testing
services: load-testing
ms.service: load-testing
ms.author: ninallam
author: ninallam
ms.date: 10/04/2021
ms.topic: tutorial
#Customer intent: As a Azure user, I want to learn how to automatically test builds for performance regressions on every pull request and/or deployment with GitHub Actions
---

# Tutorial: Set up CI/CD workflow in GitHub to run load tests automatically for every build

In this tutorial, you'll automatically load test a sample web app from GitHub on every pull request and deployment.

In this tutorial, you'll learn how to:

> [!div class="checklist"]
>
> - Set up your repository with files required for load testing.
> - Set up a GitHub workflow to integrate with Azure Load testing
> - Run the load test and view results in the workflow.
> - Define test criteria for load test to pass or fail based on thresholds.
> - Parameterize load test using GitHub secrets.

## Prerequisites

- An Azure account with an active subscription. If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.
- A GitHub account, where you can create a repository. If you don't have one, you can [create one for free](https://github.com/).
- An existing Azure Load Testing resource. Follow these steps to create one.

## Set up your repository

To get started, fork the following repository into your GitHub account.

```
https://github.com/Azure-Samples/nodejs-appsvc-cosmosdb-bottleneck.git
```

This repository has a sample Node.js app consisting of an Azure App Service web component and a Cosmos DB database.

You will require the following files in your repository for running the load test

1. JMeter script: The source repository contains a JMeter script named SampleApp.jmx. Make sure to update the JMeter script with the URL of the webapp to which the application will be deployed.

1. Configuration files (if any): This is not required for the sample app

1. Load test YAML file: The repository contains a SampleApp.yaml file. For a new test, you can author this file using the syntax shown [here](https://github.com/microsoft/azureloadtest/wiki/Common-Terminologies#brief-overview-of-yaml-properties). Alternatively, if you have already run a load test before, you can download this file from the input files section on the dashboard as shown below.

    :::image type="content" source="media/tutorial-continous-regression-testing-in-cicd-azure-pipelines/download-input-files-from-dashboard.png" alt-text="Download the input files from the dashboard of a test run":::

> [!IMPORTANT]
> The path of the testPlan and configurationFiles should be relative to the Load test YAML file.

## Set up a GitHub workflow to integrate with Azure Load testing service

1. Create an [Azure Service Principal for RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview) by running the below Azure CLI command

```azurecli
az ad sp create-for-rbac --name "myApp" --role contributor \
                                 --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                                 --sdk-auth
        
        # Replace {subscription-id}, {resource-group} with the subscription, resource group details of the WebApp
        # The command should output a JSON object similar to this:
    
      {
        "clientId": "<GUID>",
        "clientSecret": "<GUID>",
        "subscriptionId": "<GUID>",
        "tenantId": "<GUID>",
        (...)
      }
```

1. Paste the json response from above Azure CLI to your GitHub Repository > Settings > Secrets > Add a new secret > AZURE_CREDENTIALS.

1. Authorize the Service Principal to access Azure Load Testing service by assigning the **Load Test Contributor** role. Run the following Az CLI command.

    ```azurecli
    az role assignment create --assignee "{ObjectID}}" \
    --role "Load Test Contributor" \
    --subscription "{subscriptionNameOrId}"
    ```

1. Now edit the workflow file in your branch: .github/workflows/workflow.yml. Replace the following values of the variables.

    ```yml
    webAppName: 'Name of the web App' #This should be same as the App name in the SampleApp.jmx
    loadTestResource: 'Name of the Load test resource'
    loadTestResourceGroup: 'Name of the Load test resource group'
    ```

1. Commit the file directly to the main branch. The workflow should get triggered, you can check the same in the Actions tab.

## View results in GitHub

Here's what happened when you ran the workflow

- The workflow builds, creates the required Azure resources and deploys the sample node.js app to the App service.  The workflow triggers are defined to run for every push to the main branch. You can modify the triggers according to the [available triggers](https://docs.github.com/actions/learn-github-actions/events-that-trigger-workflows).

- The Azure Load testing action was used to create and run a load test on the App service deployed in the above step.

The load test results are available in the workflow logs once the test run is completed. The task is marked success or failure based on the test execution status. The link to the portal is available in the log to view execution progress. Once the test run is complete, you can view the summary and the client-side metrics in the pipeline logs. You can view the detailed dashboard by clicking on the portal URL. The results files are exported to the folder “loadTest\results.zip”

## Define test criteria for your load test

Now that you have your CI/CD workflow configured to run load tests, the next step is to add test criteria for your load test to determine the success of the test.

Test criteria can be defined using the below syntax

`[Aggregation_function] ([client_metric]) [condition] [value]`

It includes the following inputs

|Parameter  |Description  |
|---------|---------|
|Client metric     | (Required) The client metric on which the criteria should be applied.        |
|Aggregate function     |  (Required) The aggregation function to be applied on the client metric.       |
|Condition     | (Required) The comparison operator. Supported types >.        |
|Value     |  (Required) The threshold value to compare with the client metric.        |
|Action     |   (Optional) Either ‘continue’ or 'stop' after the threshold is met.</br> Default: ‘continue’      |

- If action is ‘continue’, the criteria is evaluated on the aggregate values, when the test is completed.

- If the action is ‘stop’, the criteria is evaluated for the entire test at every 60 seconds. If the criteria evaluates to true at any time, the test is stopped.

The following are the supported values

|Aggregation function  |client metric  |condition  |
|---------|---------|---------|
|Average (avg)     | Response Time (response_time) </br>Units: milliseconds (ms)  |    greater than (>)      |
|Average (avg)     | Latency (latency) </br> Units: milliseconds (ms)            |    greater than (>)      |
|Rate (rate)       | Error (error) </br> Enter percentage values                 |    greater than (>)      |

Add the test criteria to your pipeline load test as shown below

1. Edit the SampleApp.yml file in your GitHub repository.
  
1. Add the following snippet to the file

    ```yml
    criteria: 
        - avg(response_time) > 100ms
        - avg(latency) > 300ms
        - rate(error) > 20
    ```

1. Commit and push the changes to the main branch of the repository. This will now trigger the CI/CD workflow in GitHub.

1. Once the load test completes, the above workflow will fail. Go to the workflow logs and view the output of the Azure Load Testing action.

1. The output of the task will show the outcome of the test criteria. For the above criteria  since the average response time is greater than 100 ms, the first criteria will fail. The other two criteria should pass.

1. Edit the SampleApp.yml file and change the above test criteria to the following

    ```yml
    criteria: 
        - avg(response_time) > 300ms
        - avg(latency) > 300ms
        - rate(error) > 20
    ```

1. Save and run the workflow. The test should pass and the workflow should run successfully.

The Azure Load Testing service evaluates the criteria during the test execution. If any of the criteria defined in the test fails, Azure Load Testing service will return with a non-zero exit code, communicating to the workflow that the test has failed. The action in the workflow is then marked as passed or failed accordingly.

## Provide parameters to your load tests from the workflow

Now that you have set test criteria to pass or fail your test, the next step is to parameterize your load tests using workflow variables. These parameters may be secrets and /or non-secrets

- Secret parameters: These are any sensitive variables you don't want to be checked in to your source control repository. These can be stored as a secret in GitHub or in any other secret store, which can be fetched within the workflow.

- Non-secret parameters: These are values that may keep changing based on the test run. These can be provided as inputs at runtime, instead of defining in the test script.

To add parameters to your load test from the workflow

1. Edit the SampleApp.jmx file in your GitHub repository. Use the built-in function *get_param(param_name)* in your test script to call *function* to fetch the parameters as shown below. Save and commit the file

1. Go your GitHub Repository > Settings > Secrets > New repository secret > "mySecret". Add "" as the value and select Add secret.

1. In the workflow.yml file, edit the Azure Load testing task. Add the following YAML snippet to the task definition

    ```yml
    parameters: |
      { 
          "secrets": [ 
              { 
                  "name": "secret", 
                  "value": "$(mySecret)" 
              }
          ]
      }
    ```

1. Save and run the pipeline.

The Azure Load Testing task, passes the secret from the workflow to the load test engine in a secure manner. The secret parameter is used while running the load test and then the value is discarded.

## Azure Load Testing Action

This action creates and runs an Azure load test from a GitHub Workflow. The action runs on Windows, Linux, and Mac runners. Use the azure/load-testing@v1 action in your workflow. It has the following parameters.

|Parameters  |Description  |
|---------|---------|
|YAMLFilePath    | (Required) Path of the YAML file. Should be fully qualified path or relative to the default working directory        |
|resourceGroup     |  (Required) Name of the resource group.       |
|loadtestResource     |   (Required) Name of an existing load test resource      |
|parameters   |   (Optional) secrets  and  non-secrets parameters <br/> Enter Name and value of each parameter in JSON format  |

```yaml
- name: 'Azure Load Testing'
  uses: azure/load-testing@v1
  with:
    YAMLFilePath: 'Application/SampleApp.yaml'
    loadtestresource: 'nikita-sampleapp'
    resourceGroup: 'nikita-sample-rg'
    parameters: |
      { 
          "secrets": [ 
              { 
                  "name": "secret1", 
                  "value": "$(mySecret1)" 
              }, 
              { 
                  "name": "secret2", 
                  "value": "$(mySecret2)" 
              } 
          ], 
          "non-secrets": [ 
              { 
                  "name": "param1", 
                  "value": "paramValue1" 
              }, 
              { 
                  "name": "param2", 
                  "value": "paramValue2" 
              } 
          ] 
      }
```

## Clean up resources

[!INCLUDE [alt-delete-resource-group](../../includes/alt-delete-resource-group.md)]

## Next steps

You can find our set of Actions grouped into different repositories on GitHub, each one containing documentation and examples to help you use GitHub for CI/CD and deploy your apps to Azure.

> [!div class="nextstepaction"]
> [Azure and GitHub integration](https://docs.microsoft.com/azure/developer/github/)
