---
title: What is Microsoft Playwright Testing?
description: 'Microsoft Playwright Testing is a fully managed service for end-to-end testing built on top of Playwright. Run Playwright tests with high parallelization across different operating system and browser combinations simultaneously.'
ms.topic: overview
ms.date: 08/23/2022
ms.custom: playwright-testing-preview
---

# What is Microsoft Playwright Testing Preview?

With Playwright, you can automate end-to-end tests to ensure your web apps work the way you expect it to, across different web browsers and operating systems. Microsoft Playwright Testing Preview is a fully managed service for end-to-end testing built on top of Playwright. The service abstracts the complexity and infrastructure for running Playwright tests with high parallelization across different operating system and browser combinations simultaneously.

Run your Playwright test suite in the cloud, without changes to your test code or modifications to your tooling setup. Use the Playwright Test Visual Studio Code extension rich editor experience, or use the Playwright CLI for automation within your continuous integration (CI) workflow.

Get started by [running your Playwright tests at scale with Microsoft Playwright Testing](./quickstart-run-end-to-end-tests.md).

The following diagram shows an architecture overview of Microsoft Playwright Testing.

:::image type="content" source="./media/overview-what-is-microsoft-playwright-testing/microsoft-playwright-testing-architecture.png" alt-text="Diagram that shows the Microsoft Playwright Testing architecture.":::

To learn more about how Playwright works, visit the [Getting started documentation](https://playwright.dev/docs/intro) on the Playwright website.

> [!IMPORTANT]
> Microsoft Playwright Testing is currently in preview. For legal terms that apply to Azure features that are in beta, in preview, or otherwise not yet released into general availability, see the [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## Run end-to-end tests at scale across multiple platforms

Playwright lets you run end-to-end tests across multiple browser configurations. Microsoft Playwright Testing helps you run these tests at cloud scale, and on multiple operating systems.

The service enables you to run all tests on a specific operating system version, or you can [test across multiple operating systems](./how-to-cross-platform-tests.md) with each run.

Microsoft Playwright Testing lets you scale your test run across parallel *workers*, without being limited by the processing power of your local infrastructure. These workers let you achieve a high degree of parallelism across the different test cases, browser configurations, and operating systems. This parallelization helps you shorten the overall test duration.

By reducing the test duration, you can also enable [continuous end-to-end testing](./quickstart-automate-end-to-end-testing.md) and get feedback with every application build. You can integrate Microsoft Playwright Testing in any CI/CD solution by using the Playwright command-line interface (CLI).

You can use the cloud-based infrastructure to test both publicly and privately accessible applications without allowing inbound connections on your firewall. During the development phase, you can also use Microsoft Playwright Testing to run tests against a localhost development server.

## Key capabilities

- Multiple browsers - all [browsers supported by Playwright](https://playwright.dev/docs/release-notes)
- Multiple operating systems (Windows, Linux)
- Regional affinity
- Test publicly and privately hosted applications, localhost development servers
- Run on parallel workers

## Limitations

- Linux & Windows hosted browsers (macOS)
- 50 parallel workers
- Support for JavaScript/TypeScript with the Playwright runner

## How it works

Microsoft Playwright Testing enables you to run Playwright end-to-end tests in a managed service. Microsoft Playwright Testing workers abstract the cloud infrastructure for operating the remote browsers that run the test steps in your test specification. The workers support [multiple operating systems](./how-to-cross-platform-tests.md) and multiple browsers.

Playwright runs on the client machine and interacts with Microsoft Playwright Testing to run the tests on the remote browsers. The client machine can be your developer workstation or a CI/CD agent machine if you run your tests as part of your CI/CD pipeline. Playwright uses the test specification file to determine which steps must be run to perform the web UI test.

After a test run completes, Playwright uploads the test results and test artifacts to the service for you to view in the dashboard. Microsoft Playwright Testing stores the history of your test runs.

Running existing tests with Microsoft Playwright Testing requires no changes to your test specifications. Update the Playwright configuration file to add the settings to connect and authenticate with the service. To authenticate service requests, you [generate an access key](./how-to-manage-access-keys.md).

After you update the Playwright configuration file, you can continue to run your tests using the Playwright command-line interface, the Visual Studio Code extension, or integrate them in your CI/CD pipeline.

To [test applications that aren't publicly accessible](./how-to-test-private-endpoints.md), you can add a configuration setting to enable Microsoft Playwright Testing to communicate with the application over an outbound connection.

## Data at rest

Microsoft Playwright Testing automatically encrypts all data stored in your workspace with keys managed by Microsoft (service-managed keys). For example, this data includes workspace details and Playwright test run metadata, such as test start time, test duration, number of parallel workers.

## In-region data residency
Microsoft Playwright Testing doesn't store or process customer data outside the region you deploy the workspace in.

## Next step

> [!div class="nextstepaction"]
> [Run Playwright tests at scale](quickstart-run-end-to-end-tests.md)
