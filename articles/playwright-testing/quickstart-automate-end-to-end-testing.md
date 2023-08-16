---
title: 'Quickstart: Continuous end-to-end testing'
description: In this quickstart, you learn how to run your Playwright tests at scale in your CI pipeline with Microsoft Playwright Testing. Continuously validate that your web app runs correctly across browsers and operating systems.
ms.topic: quickstart
ms.date: 08/16/2023
ms.custom: playwright-testing-preview
---

# Quickstart: Set up continuous end-to-end testing with Microsoft Playwright Testing Preview

In this quickstart, you set up continuous end-to-end testing with Microsoft Playwright Testing Preview to validate that your web app runs correctly across different browsers and operating systems with every code commit. Learn how to add your Playwright tests to a continuous integration (CI) workflow, such as GitHub Actions, Azure Pipelines, or other CI platforms.

After you complete this quickstart, you have a CI workflow that runs your Playwright test suite at scale with Microsoft Playwright Testing.

> [!IMPORTANT]
> Microsoft Playwright Testing is currently in preview. For legal terms that apply to Azure features that are in beta, in preview, or otherwise not yet released into general availability, see the [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## Prerequisites

* An Azure account with an active subscription. If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.

* A Microsoft Playwright Testing workspace. Complete the [quickstart: run Playwright tests at scale](./quickstart-run-end-to-end-tests.md) to create a workspace.

# [GitHub Actions](#tab/github)
- A GitHub account. If you don't have a GitHub account, you can [create one for free](https://github.com/).
- A GitHub repository that contains your Playwright test specifications and GitHub Actions workflow. To create a repository, see [Creating a new repository](https://docs.github.com/github/creating-cloning-and-archiving-repositories/creating-a-new-repository).
- A GitHub Actions workflow. If you need help getting started with GitHub Actions, see [create your first workflow](https://docs.github.com/en/actions/quickstart)

# [Azure Pipelines](#tab/pipelines)
- An Azure DevOps organization and project. If you don't have an Azure DevOps organization, you can [create one for free](/azure/devops/organizations/projects/create-project?view=azure-devops&tabs=browser).
- A pipeline definition. If you need help with getting started with Azure Pipelines, see [create your first pipeline](/azure/devops/pipelines/create-first-pipeline?preserve-view=true&view=azure-devops&tabs=java%2Ctfs-2018-2%2Cbrowser).

---

## 1. Configure a service access key

Microsoft Playwright Testing uses access keys to authorize users to run Playwright tests with the service. You can generate a service access key in the Playwright portal, and then specify the access key in the service configuration file.

To generate an access key and store it as a CI workflow secret, perform the following steps:

1. Sign in to the [Microsoft Playwright Testing portal](https://aka.ms/mpt/portal) with your Azure account.

1. Select **Generate new access key**.

    :::image type="content" source="./media/include-generate-access-key/playwright-testing-generate-new-access-key.png" alt-text="Screenshot that shows Microsoft Playwright Testing portal, highlighting the 'Generate access key' button.":::

1. Select **Generate key** and then copy the access key value.

    :::image type="content" source="./media/include-generate-access-key/playwright-testing-generate-key.png" alt-text="Screenshot that shows setup guide in the Playwright Testing portal, highlighting the 'Generate key' button.":::

    :::image type="content" source="./media/include-generate-access-key/playwright-testing-copy-access-key.png" alt-text="Screenshot that shows how to copy the generated access key in the Playwright Testing portal.":::

1. Store the access key in a CI workflow secret to avoid specifying the key in clear text in the workflow definition:

    The following steps describe how to create a workflow secret in GitHub Actions or Azure Pipelines. Follow the specific instructions of your CI platform to create store the access key securely.

# [GitHub Actions](#tab/github)

1. Go to your GitHub repository, and select **Settings** > **Secrets and variables** > **Actions**.
1. Select **New repository secret**.
1. Enter the secret details, and then select **Add secret** to create the CI/CD secret.

    | Parameter | Value |
    | ----------- | ------------ |
    | **Name** | *PLAYWRIGHT_SERVICE_ACCESS_KEY* |  
    | **Value** | Paste the workspace access key you copied previously. |

1. Select **OK** to create the workflow secret.

# [Azure Pipelines](#tab/pipelines)

1. Go to your Azure DevOps project.
1. Go to the **Pipelines** page, select the appropriate pipeline, and then select **Edit**.
1. Locate the **Variables** for this pipeline.
1. Add a new variable.
1. Enter the variable details, and then select **Add secret** to create the CI/CD secret.

    | Parameter | Value |
    | ----------- | ------------ |
    | **Name** | *PLAYWRIGHT_SERVICE_ACCESS_KEY* |
    | **Value** | Paste the workspace access key you copied previously. |
    | **Keep this value secret** | Check this value |

1. Select **OK**, and then **Save** to create the workflow secret.

---

## 2. Get the service region endpoint URL

In the service configuration you have to provide the region-specific service endpoint. The endpoint depends on the Azure region you selected when creating the workspace.

To get the service endpoint URL and store it as a CI workflow secret, perform the following steps:

1. Sign in to the [Microsoft Playwright Testing portal](https://aka.ms/mpt/portal) with your Azure account.

1. On the workspace home page, select **View setup guide**.

    > [!TIP]
    > If you have multiple workspaces, you can switch to another workspace by selecting the workspace name at the top of the page, and then select **Manage all workspaces**.

1. In **Add region endpoint in your setup**, copy the service endpoint URL.

    The endpoint URL matches the Azure region that you selected when creating the workspace.

1. Store the service endpoint URL in a CI workflow secret:

    | Secret name | Value |
    | ----------- | ------------ |
    | *PLAYWRIGHT_SERVICE_URL* | Paste the endpoint URL you copied previously. |

## 3. Add service configuration file

If you haven't configured your Playwright tests yet for running them on cloud-hosted browsers, add a service configuration file to your repository. In the next step, you then specify this service configuration file on the Playwright CLI.

1. Create a new file `playwright.service.config.ts` alongside the `playwright.config.ts` file.

1. Create a file `playwright.service.config.ts` and add the following content to it:

    ```typescript
    /*
    * This file enables Playwright client to connect to remote browsers.
    * It should be placed in the same directory as playwright.service.config.ts.
    * The file is temporary for private preview.
    */
    
    import { defineConfig } from '@playwright/test';
    import config from './playwright.config';
    import dotenv from 'dotenv';
    
    // Define environment on the dev box in .env file:
    //  .env:
    //    PLAYWRIGHT_SERVICE_ACCESS_KEY=XXX
    //    PLAYWRIGHT_SERVICE_URL=XXX
    //    PLAYWRIGHT_SERVICE_OS=XXX
    
    // Define environment in your GitHub workflow spec.
    //  env:
    //    PLAYWRIGHT_SERVICE_ACCESS_KEY: ${{ secrets.PLAYWRIGHT_SERVICE_ACCESS_KEY }}
    //    PLAYWRIGHT_SERVICE_URL: ${{ secrets.PLAYWRIGHT_SERVICE_URL }}
    //    PLAYWRIGHT_SERVICE_OS: ${{ matrix.service-os }}
    //    PLAYWRIGHT_SERVICE_RUN_ID: ${{ github.run_id }}-${{ github.run_attempt }}-${{ github.sha }}
    
    dotenv.config();
    
    // Name the test run if it's not named yet.
    process.env.PLAYWRIGHT_SERVICE_RUN_ID = process.env.PLAYWRIGHT_SERVICE_RUN_ID || new Date().toISOString();
    
    export default defineConfig(config, {
      // Define more generous timeout for the service operation if necessary.
      // timeout: 60000,
      // expect: {
      //   timeout: 10000,
      // },
      use: {
        connectOptions: {
          wsEndpoint: `${process.env.PLAYWRIGHT_SERVICE_URL}?cap=${JSON.stringify({
            os: process.env.PLAYWRIGHT_SERVICE_OS || 'linux',
            runId: process.env.PLAYWRIGHT_SERVICE_RUN_ID
          })}`,
          timeout: 30000,
          headers: {
            'x-mpt-access-key': process.env.PLAYWRIGHT_SERVICE_ACCESS_KEY!
          },
          exposeNetwork: '<loopback>'
        }
      }
    });
    ```

1. Save and commit the file to your source code repository.

## 4. Update the workflow definition

Update the CI workflow definition to run your Playwright tests with the Playwright CLI. Pass the [service configuration file](#3-add-service-configuration-file) as an input parameter for the Playwright CLI. You configure your environment by specifying environment variables.

1. Open the CI workflow definition

1. Add the following steps to run your Playwright tests in Microsoft Playwright Testing.

    The following steps describe the workflow changes for GitHub Actions or Azure Pipelines. Similarly, you can run your Playwright tests by using the Playwright CLI in other CI platforms.

# [GitHub Actions](#tab/github)

  ```yml
  - name: Install dependencies
    working-directory: path/to/playwright/folder # update accordingly
    run: npm ci
  - name: Run Playwright tests
    working-directory: path/to/playwright/folder # update accordingly
    env:
      # Access key and regional endpoint for Microsoft Playwright Testing
      PLAYWRIGHT_SERVICE_ACCESS_KEY: ${{ secrets.PLAYWRIGHT_SERVICE_ACCESS_KEY }}
      PLAYWRIGHT_SERVICE_URL: ${{ secrets.PLAYWRIGHT_SERVICE_URL }}
    run: npx playwright test -c playwright.service.config.ts --workers=20
  ```

# [Azure Pipelines](#tab/pipelines)

  ```yml
  - task: PowerShell@2
    enabled: true
    displayName: "Install dependencies"
    inputs:
      targetType: 'inline'
      script: 'npm ci'
      workingDirectory: path/to/playwright/folder # update accordingly
  
  - task: PowerShell@2
    enabled: true
    displayName: "Run Playwright tests"
    env:
      PLAYWRIGHT_SERVICE_ACCESS_KEY: $(PLAYWRIGHT_SERVICE_ACCESS_KEY)
      PLAYWRIGHT_SERVICE_URL: $(PLAYWRIGHT_SERVICE_URL)
    inputs:
      targetType: 'inline'
      script: 'npx playwright test -c playwright.service.config.ts --workers=20'
      workingDirectory: path/to/playwright/folder # update accordingly
  ```

---

1. Save and commit your changes.

    When the CI workflow is triggered, your Playwright tests will run in your Microsoft Playwright Testing workspace on cloud-hosted browsers, across 20 parallel workers.

## Next steps

You've successfully set up a continuous end-to-end testing workflow to run your Playwright tests at scale on cloud-hosted browsers.

- Learn more about [managing workspaces in the Azure portal](./how-to-manage-workspace-in-azure-portal.md).
