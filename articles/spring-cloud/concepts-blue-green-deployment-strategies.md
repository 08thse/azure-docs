---
title:  "Blue-Green Deployment Strategies in Azure Spring Cloud"
description: This topic explains two approaches to blue-green deployments in Azure Spring Cloud.
author:  yevster
ms.author: yebronsh
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 04/02/2021
ms.custom: devx-track-java
---

Azure Spring cloud (Standard tier and higher) permits two deployments for every app, of which only one receives production traffic - a pattern commonly known as Blue-Green deployment. Azure Spring Cloud's support for Blue-Green deployment, together with a [Continuous Delivery (CD)](/azure/devops/learn/what-is-continuous-delivery) pipeline and rigorous automated testing, allows agile application deployments with high confidence.

## Alternating Deployments

The simplest way to implement blue-green deployment with Azure Spring Cloud is to create two fixed deployments and always deploy to the deployment that is not receiving production traffic. With the Azure Spring Cloud task for Azure Pipelines, you can accomplish this just by setting the `UseStagingDeployment` flag to `true`.

Here's how the alternating deployments approach works in practice:

Suppose an application has two deployments: `deployment1` and `deployment2`. Currently, `deployment1` is set as the production deployment, and is running version `v3` of the application.

Thus makes `deployment2` the staging deployment. Thus, when the Continuous Delivery (CD) pipeline is ready to run, it deploys the next version of the app, `v4` onto the staging deployment `deployment2`.

![Two deployments, deployment 1 receives production traffic](media/spring-cloud-blue-green-patterns/alternating-deployments-1.png)

Once `v4` has started up on `deployment2`, automated and manual tests can be run against it through a private test endpoint to ensure `v4` meets all expectations.

![V4 is now deployed on deployment2 and undergoes testing](media/spring-cloud-blue-green-patterns/alternating-deployments-2.png)

Once confidence in `v4` has been established, `deployment2` can be set as the production deployment. Now, it receives all production traffic. `v3` remains running on `deployment1` in case a critical issue is discovered that requires rolling back.

![V4 on deployment2 now receives production traffic](media/spring-cloud-blue-green-patterns/alternating-deployments-3.png)


Now, `deployment1` is the staging deployment. So the next run of the deployment pipeline deploys onto `deployment1`.


![V5 deployed on deployment1](media/spring-cloud-blue-green-patterns/alternating-deployments-4.png)

`V5` can now be tested on `deployment1`'s private test endpoint.

![V5 tested on deployment1](media/spring-cloud-blue-green-patterns/alternating-deployments-5.png)

Finally, once `v5` has been shown to meet all expectations, `deployment1` is once again set as production deployment, so that `v5` receives all production traffic.

![V5 receives traffic on deployment1](media/spring-cloud-blue-green-patterns/alternating-deployments-6.png)

### Tradeoffs of the Alternating Deployments Approach

The Alternating Deployments approach is simple and fast, as it does not require the creation of new deployments. However, it does present several disadvantages:

#### Persistent staging deployment

The staging deployment always remains running, and thus consuming resources of the Azure Spring Cloud instance. This effectively doubles the resources requirements of each application on Azure Spring Cloud.

#### The approval race condition

Suppose in the above application, the release pipeline requires manual approval before each new version of the application can receive production traffic. This creates the risk that while one version (`v6`) awaits manual approval on the staging deployment, the deployment pipeline will run again and overwrite it with a newer version (`v7`). Then, when the approval for `v6` is granted, the pipeline that deployed `v6` will set the staging deployment as production. But now it will be the unapproved `v7` not the approved `v6` that is deployed on that deployment and receives traffic.

![The approval race condition](media/spring-cloud-blue-green-patterns/alternating-deployments-race-condition.png)

You may be able to prevent the race condition by ensuring that the deployment flow for one version cannot begin until the deployment flow for all previous versions is complete or aborted. Another way to prevent the approval race condition is to use the Named Deployments approach described below.

## Named deployments

In the Named Deployments approach, a new deployment is created for each new version of the application being deployed. Once the application is tested on its bespoke deployment, that deployment is set as the production deployment. The deployment containing the previous version can be allowed to persist just long enough to be confident that a rollback will not be needed.

In the illustration below, version `v5` is running on the deployment `deployment-v5`. The deployment name now contains the version as the deployment was created specifically for this version. There is no other deployment at the outset. Now, to deploy version `v6`, the deployment pipeline creates a new deployment `deployment-v6` and deploys app version `v6` there.

![Deploying new version on a named deployment](media/spring-cloud-blue-green-patterns/named-deployment-1.png)

Note that there is no risk of another version being deployed in parallel. First, Azure Spring Cloud does not allow the creation of a third deployment while two deployments already exist. Second, even if it was possible to have more than two deployments, each deployment is identified by the version of the application it contains. Thus, the pipeline orchestrating the deployment of `v6` would only attempt to set `deployment-v6` as the production deployment.

![New version receives production traffic named deployment](media/spring-cloud-blue-green-patterns/named-deployment-2.png)

Once the deployment created for the new version receives production traffic, it is necessary to remove the deployment containing the previous version, to make room for future deployments. You may wish to postpone by some number of minutes or hours so you can roll back to the previous version should a critical issue be discovered in the new version.

![After a fallback period, deleting the previous deployment](media/spring-cloud-blue-green-patterns/named-deployment-3.png)

### Tradeoffs of the Named Deployments Approach

The Named Deployments approach has some significant benefits. It...

* Prevents the approval race condition
* Reduces resource consumption by deleting the staging deployment when it's not in use.

However, there are drawbacks as well:

#### Deployment pipeline failures

Between the time a deployment starts and the time the staging deployment is deleted, any additional attempts to run the deployment pipeline will fail. The pipeline will attempt to create a new deployment, which will result in an error as only two deployments are permitted per Azure Spring Cloud application.

Therefore, the deployment orchestration must either have the means to retry a failed deployment process at a later time or to ensure that the deployment flows for each version remain queued until the flow is completed for all previous versions.
