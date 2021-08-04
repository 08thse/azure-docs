---
title: Troubleshoot share connection failure on Azure Data Box device
description: Describes how to identify network issues preventing share connections in Azure Data Box.
services: databox
author: v-dalc

ms.service: databox
ms.subservice: pod
ms.topic: troubleshooting
ms.date: 08/04/2021
ms.author: alkohli
---

# Troubleshoot a share connection failure on an Azure Data Box device

This article describes what to do when you can't connect to a share on your Azure Data Box device.<!--Verifying: Is this during a data copy to the device, an upload from the device to the cloud, or both.-->

The most common reasons for being unable to connect to a share on a Data Box device are:

- [a domain issue](#check-for-a-domain-issue)
- [a group policy that's preventing a connection](#check-for-a-blocking-group-policy)

## Check for a domain issue

To find out whether a domain issue is preventing share access:

1. When you access the share, enter the share password in one of the following formats:

    - `<device IP address>\<user name>`
    - `\<user name>`

    To access a share associated with your storage account from your host computer, open a command window. At the command prompt, type the following command. You'll be prompted for a password.

    `net use \\<IP address of the device>\<share name> /u:<IP address of the device>\<user name for the share>`

    For a procedure, see [Copy data to Data Box via SMB](data-box-deploy-copy-data.md).

## Check for a blocking group policy

Check whether a group policy on your client/host computer is preventing you from connecting to the share. If possible, move your client/host computer to an organizational unit (OU) that doesn't have any Group Policy objects (GPOs) applied.

To ensure that no group policies are preventing your access to shares on the Data Box:

* Ensure that your client/host computer is in its own organizational unit (OU) for Active Directory.

* Make sure that no group policy objects (GPOs) are applied to your client/host computer. You can block inheritance to ensure that the client/host computer (child node) does not automatically inherit any GPOs from the parent. For more information, see [block inheritance](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731076(v=ws.11)).

## Contact support

If there's no domain issue, and no group policies are blocking your access to the share, [contact Microsoft support](data-box-disk-contact-microsoft-support.md) for additional help.


## Next steps

- [Copy data via SMB](data-box-deploy-copy-data.md).
- [Troubleshoot data copy issues in Data Box](data-box-troubleshoot.md).