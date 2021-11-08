---
title: Connect Microsoft Sentinel to S3 Buckets to get Amazon Web Services (AWS) data
description: Use the AWS connector to delegate Microsoft Sentinel access to AWS resource logs, creating a trust relationship between Amazon Web Services and Microsoft Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/06/2021
ms.author: yelevin

---

# Connect Microsoft Sentinel to S3 Buckets to get Amazon Web Services (AWS) data

Use the Amazon Web Services (AWS) S3 connector to pull AWS service logs into Microsoft Sentinel. This connector works by granting Microsoft Sentinel access to your AWS resource logs. Setting up the connector establishes a trust relationship between Amazon Web Services and Microsoft Sentinel. This is accomplished on AWS by creating a role that gives permission to Microsoft Sentinel to access your AWS logs.

The AWS services and data types that are currently supported by this connector are:

- [Amazon Virtual Private Cloud (VPC)](https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html) - [VPC Flow Logs](https://docs.aws.amazon.com/vpc/latest/userguide/flow-logs.html)
- [Amazon GuardDuty](https://docs.aws.amazon.com/guardduty/latest/ug/what-is-guardduty.html) - [Findings](https://docs.aws.amazon.com/guardduty/latest/ug/guardduty_findings.html)
- [AWS CloudTrail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-user-guide.html) - [Management](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/logging-management-events-with-cloudtrail.html) and [data](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/logging-data-events-with-cloudtrail.html) events

This document explains how to configure the new AWS S3 connector. The process of setting it up has two parts: the AWS side and the Microsoft Sentinel side.

1. In your AWS environment:

    - Configure your AWS service(s) to send logs to an **S3 bucket**.

    - Create a **Simple Queue Service (SQS) queue** to provide notification.

    - Create an **assumed role** to grant permissions to your Microsoft Sentinel account (external ID) to access your AWS resources.

    - Attach the appropriate **IAM permissions policies** to grant Microsoft Sentinel access to the appropriate resources (S3 bucket, SQS).

1. In Microsoft Sentinel:

    - Enable and configure the **AWS S3 Connector** in the Microsoft Sentinel portal. See the instructions below.

Each side's process produces information used by the other side. This sharing creates secure communication.

We have made available, in our GitHub repository, a script that **automates the AWS side of this process**. See the instructions for [automatic setup](#automatic-setup) later in this document.

## Architecture overview

This graphic and the following text shows how the parts of this connector solution interact.

:::image type="content" source="media/connect-aws-s3/s3-connector-architecture.png" alt-text="Screenshot of A W S S 3 connector architecture.":::

- AWS services are configured to send their logs to S3 (Simple Storage Service) storage buckets.

- The S3 bucket sends notification messages to the SQS (Simple Queue Service) message queue whenever it receives new logs.

- The Microsoft Sentinel AWS S3 connector polls the SQS queue at regular, frequent intervals. If there is a message in the queue, it will contain the path to the log files.

- The connector reads the message with the path, then fetches the files from the S3 bucket.

- To connect to the SQS queue and the S3 bucket, Microsoft Sentinel uses AWS credentials and connection information embedded in the AWS S3 connector's configuration. The AWS credentials are configured with a role and a permissions policy giving them access to those resources. Similarly, the Microsoft Sentinel workspace ID is embedded in the AWS configuration, so there is in effect two-way authentication.

## Prerequisites

- You must have write permission on your Microsoft Sentinel workspace.

- You must have PowerShell and the AWS CLI on your machine.

    - [Installation instructions for PowerShell](/powershell/scripting/install/installing-powershell?view=powershell-7.1)
    - [Installation instructions for the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html)


## Automatic setup

To simplify the onboarding process, Microsoft Sentinel has provided a [PowerShell script to automate the setup](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/AWS-S3/ConfigAwsConnector.ps1) of the AWS side of the connector - the required AWS resources, credentials, and permissions.

The script takes the following actions:

- Creates an *IAM assumed role* with the minimal necessary permissions, to grant Microsoft Sentinel access to your logs in a given S3 bucket and SQS queue.

- Enables specified AWS services to send logs to that S3 bucket, and notification messages to that SQS queue.

- If necessary, creates that S3 bucket and that SQS queue for this purpose.

- Configures any necessary IAM permissions policies and applies them to the IAM role created above.

- ***IS THE ORDER OF THE ABOVE LOGICAL? DOES IT/SHOULD IT FOLLOW THE ORDER OF THE SCRIPT? -YL***

### Instructions

To run the script to set up the connector, use the following steps:

1. From the Microsoft Sentinel navigation menu, select **Data connectors**.

1. Select **Amazon Web Services S3** from the data connectors gallery, and in the details pane, select **Open connector page**.

1. In the **Configuration** section, under **1. Set up your AWS environment**, expand **Setup with PowerShell script**.

1. Follow the on-screen instructions to download and extract the [AWS S3 Setup Script](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/AWS-S3) from the connector page.

1. Before running the script, run the `aws configure` command from your PowerShell command line, and enter the relevant information as prompted. See [AWS Command Line Interface | Configuration basics](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-quickstart.html) for details.

1. Under **3. Add connection**, copy the **Role ARN** and the **SQS URL** from the output of the script (see example in first screenshot below) and paste them in their respective fields in the connector page (see second screenshot below).

    :::image type="content" source="media/connect-aws-s3/aws-script-output.png" alt-text="Screenshot of output of A W S connector setup script.":::

   :::image type="content" source="media/connect-aws-s3/aws-add-connection.png" alt-text="Screenshot of pasting the A W S role information from the script, to the S3 connector.":::

1. Select a data type from the **Destination table** drop-down list. This tells the connector which AWS service's logs this connection is being established to collect, and into which Log Analytics table it will store the ingested data. Then select **Add connection**.

> [!NOTE]
> The script may take up to 30 minutes to finish running.

## Manual setup

Microsoft recommends using the automatic setup script to deploy this connector. If for whatever reason you do not want to take advantage of this convenience, follow the steps below to set up the connector manually.

### Prerequisites

- You must have an **S3 bucket** to which you will ship the logs from your AWS services - VPC, GuardDuty, or CloudTrail.

- You must have an **SQS message queue** to which the S3 bucket will publish notifications.

    - Create a [standard Simple Queue Service (SQS) queue](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-configure-create-queue.html) in AWS.

### Instructions

The manual setup consists of the following steps:
- [Create an AWS assumed role and grant access to the AWS Sentinel account](#create-an-aws-assumed-role-and-grant-access-to-the-aws-sentinel-account)
- [Configure an AWS service to export logs to an S3 bucket](#configure-an-aws-service-to-export-logs-to-an-s3-bucket)
- [Create a Simple Queue Service (SQS) in AWS](#create-a-simple-queue-service-sqs-in-aws)
- [Enable SQS notification](#enable-sqs-notification)
- [Apply IAM permissions policies](#apply-iam-permissions-policies)

#### Create an AWS assumed role and grant access to the AWS Sentinel account

1. In Microsoft Sentinel, select **Data connectors** and then select the **Amazon Web Services S3** line in the table and in the AWS pane to the right,  select **Open connector page**.

1. Under **Configuration**, copy the **Microsoft account ID** and the **External ID (Workspace ID)** and paste them aside.
 
1. In your Amazon Web Services console, under **Security, Identity & Compliance**, select **IAM**.

   :::image type="content" source="media/connect-aws-s3/aws-1.png" alt-text="Screenshot of Amazon Web Services console.":::

1. Choose **Roles** and select **Create role**.

   :::image type="content" source="media/connect-aws-s3/aws-2.png" alt-text="Screenshot of A W S roles creation screen.":::

1. Choose **Another AWS account.** In the **Account ID** field, enter the **Microsoft Account ID** that you copied from the AWS connector page in the Microsoft Sentinel portal and pasted aside.

   :::image type="content" source="media/connect-aws-s3/aws-3.png" alt-text="Screenshot of A W S role configuration screen.":::

1. Select the **Require External ID** check box, and then enter the **External ID (Workspace ID)** that you copied from the AWS connector page in the Microsoft Sentinel portal and pasted aside. Then select **Next: Permissions**.

   :::image type="content" source="media/connect-aws-s3/aws-4.png" alt-text="Screenshot of continuation of A W S role configuration screen.":::

1. Skip the next step, **Attach permissions policy**, for now. You'll come back to it later [when instructed](#apply-iam-permissions-policies). Select **Next: Tags**.

   :::image type="content" source="media/connect-aws-s3/aws-5.png" alt-text="Screenshot of Next: Tags.":::

1. Enter a **Tag** (optional). Then select **Next: Review**.

   :::image type="content" source="media/connect-aws-s3/aws-6.png" alt-text="Screenshot of tags screen.":::

1. Enter a **Role name** and select **Create role**.

   :::image type="content" source="media/connect-aws-s3/aws-7.png" alt-text="Screenshot of role naming screen.":::

1. In the **Roles** list, select the new role you created.

   :::image type="content" source="media/connect-aws-s3/aws-8.png" alt-text="Screenshot of roles list screen.":::

1. Copy the **Role ARN**.

   :::image type="content" source="media/connect-aws-s3/aws-9.png" alt-text="Screenshot of copying role A R N.":::

1. ***ADD STEP ABOUT ADDING THE SQS URL -YL***

1. In the AWS S3 connector page in the Microsoft Sentinel portal, paste the **Role ARN** into the **Role ARN** field under **3. Add connection**, and select **Add connection**.

   :::image type="content" source="media/connect-aws-s3/aws-add-connection.png" alt-text="Screenshot of adding an A W S role connection to the S3 connector.":::

#### Configure an AWS service to export logs to an S3 bucket

- [Publish a VPC flow log to an S3 bucket](https://docs.aws.amazon.com/vpc/latest/userguide/flow-logs-s3.html).

    > [!NOTE]
    > If you choose to customize the log's format, you must include the *start* attribute, as it maps to the *TimeGenerated* field in the Log Analytics workspace. Otherwise, the *TimeGenerated* field will be populated with the event's *ingested time*, which doesn't accurately describe the log event.

- [Export your GuardDuty findings to an S3 bucket](https://docs.aws.amazon.com/guardduty/latest/ug/guardduty_exportfindings.html).

    > [!NOTE]
    > The *TimeGenerated* field is populated with the finding's *Update at* value.

- AWS CloudTrail trails are stored in S3 buckets by default.

#### Create a Simple Queue Service (SQS) in AWS

If you haven't yet [created an SQS queue](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-configure-create-queue.html), do so now.

#### Enable SQS notification

[Configure your S3 bucket to send notifications to your SQS queue](https://docs.aws.amazon.com/AmazonS3/latest/userguide/enable-event-notifications.html).

#### Apply IAM permissions policies

The following permissions policies must be applied to the [Microsoft Sentinel role you created](#create-an-aws-assumed-role-and-grant-access-to-the-aws-sentinel-account).

- AmazonSQSReadOnlyAccess
- AWSLambdaSQSQueueExecutionRole
- AmazonS3ReadOnlyAccess
- KMS access
- ***ANY OTHERS? ANY SERVICE-DEPENDENT? -YL***

## Known issues and troubleshooting

## Next steps

In this document, you learned how to connect to AWS resources to ingest their logs into Microsoft Sentinel. To learn more about Microsoft Sentinel, see the following articles:
- Learn how to [get visibility into your data, and potential threats](get-visibility.md).
- Get started [detecting threats with Microsoft Sentinel](detect-threats-built-in.md).
- [Use workbooks](monitor-your-data.md) to monitor your data.
