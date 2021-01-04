---
title: Understanding Azure File Sync Cloud Tiering | Microsoft Docs
description: Get a general understanding of what cloud tiering, an optional Azure File Sync feature, is. Frequently accessed files are cached locally on the server; others are tiered to Azure Files.
author: mtalasila
ms.service: storage
ms.topic: conceptual
ms.date: 1/4/2021
ms.author: mtalasila
ms.subservice: files
---

# Cloud tiering overview
Cloud tiering, an optional feature of Azure File Sync, decreases the amount of local storage required while keeping the performance of an on-premises file server.

When enabled, this feature stores only frequently accessed (hot) files on your local server. Infrequently accessed (cool) files are split into namespace (file and folder structure) and file content. The namespace is stored locally and the file content stored in an Azure file share in the cloud. 

When a user opens a tiered file, Azure File Sync seamlessly recalls the file data from the file share in Azure.
    
## What is cloud tiering?

Cloud tiering is the separation between namespace and file content.

#### Tiered file

For tiered files, the size on disk is zero since the file content itself isn't being stored locally. When a file is tiered, the Azure File Sync file system filter (StorageSync.sys) replaces the file locally with a pointer (reparse point). The reparse point represents a URL to the file in the Azure file share stored in the cloud. A tiered file has both the "offline" attribute and the FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS attribute set in NTFS so that third-party applications can securely identify tiered files.   

![A screenshot of a file's properties when it is tiered - namespace only](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-2.png)    

#### Locally cached file

On the other hand, for a file stored in an on-premises file server, the size on disk is about equal to the logical size of the file since the entire file (namespace + file content) is stored locally.     

![A screenshot of a file's properties when it is not tiered - namespace + file content](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-1.png) 

It's also possible for a file to be partially tiered (or partially recalled). In a partially tiered file, part of the file is on disk. This might occur when files are partially read by applications like multimedia players or zip utilities.

> [!NOTE]
> Size represents the logical size of the file. Size on disk represents the physical size of the file stream that's stored on the disk.

## How cloud tiering works

The Azure File Sync system filter builds a "heatmap" of your namespace on each server endpoint. This heatmap is an ordered list of all the files that are syncing and are in a location that has cloud tiering enabled. It monitors accesses (read and write operations) over time and based on both the frequency and recency of access, assigns a heat score to every file. A frequently accessed file that was recently opened will be considered hot, while a file that is barely touched and hasn't been accessed for some time will be considered cool. 

To determine the relative position of an individual file in that heatmap, the system uses the maximum of either of the following timestamps, in that order: MAX(Last Access Time, Last Modified Time, Creation Time). 

Typically, last access time is tracked and available. However, when a new server endpoint is created, with cloud tiering enabled, then initially not enough time has passed to observe file access. In the absence of a last access time, the last modified time is used to evaluate the relative position in the heatmap. 

The same fallback is applicable to the date policy. Without a last access time, the date policy will act on the last modified time. Should that be unavailable, it will fall back to the create time of a file. Over time, the system will observe more and more file access requests and pivot to predominantly use the self-tracked last access time.

> [!Note]
> Cloud tiering does not depend on the NTFS feature for tracking last access time. This NTFS feature is off by default and due to performance considerations, we do not recommend that you manually enable this feature. Cloud tiering tracks last access time separately and very efficiently.

### Cloud tiering policies
When you enable cloud tiering, there are two policies that you can set to inform the Azure File Sync system filter when to tier cool files back to the cloud: the **volume free space policy** and the **date policy**. 

#### Volume free space policy
The **volume free space policy** tells the system filter to tier cool files to the cloud when a certain amount of space is taken up on your local disk. 

For example, if your local disk capacity is 200 GB and you always want at least 40 GB of your local disk capacity to remain free, you should set the volume free space policy to 20%. In this case, up to 80% of the volume space will be occupied by the most recently accessed files, with any remaining files that don't fit into this space tiered up to Azure. Volume free space applies at the volume level rather than at the level of individual directories or sync groups. 

To check out how the volume free space policy affects files when they're initially downloaded when adding a new server endpoint, see the Sync Policies that Affect Cloud Tiering section below (see [Sync policies that affect cloud tiering](#sync-policies-that-affect-cloud-tiering)).

#### Date policy
With the **date policy**, cool files are tiered to the cloud if they haven't been accessed (that is, read or written to) for x number of days. For example, if you noticed that files that have gone more than 15 days without being accessed are typically archival files, you should set your date policy to 15 days. 

For more examples on how the date policy and volume free space policy work together, see [Choosing a cloud tiering policy](storage-sync-choosing-cloud-tiering-policies.md).

### Sync policies that affect cloud tiering

With Azure File Sync agent version 11, there are two additional Azure File Sync policies you can set that affects cloud tiering: **initial download mode** and **proactive recalling mode**.

#### Initial download mode 

When a server is connecting to an Azure file share with files in it, you can now decide how you want the server to initially download the file share data. When cloud tiering is enabled, you have two options. 

The first option is to recall the namespace first, and then recall the file content by last-modified timestamp, until local disk capacity is reached. If you have enough disk space and you know that files that are last modified should be cached locally, this is the option for you.     

![A screenshot of initial download mode when cloud tiering is enabled](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-3.png)   

The second option is to initially recall the namespace only, and recall the file content when accessed. This option is best if you want to minimize the capacity used on your local disk and want users to decide which files should be cached locally.

![A screenshot of initial download mode when cloud tiering is disabled](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-4.png)

When cloud tiering is disabled, in addition to the two options above, you get a third option as well, which is to avoid tiered files all together. The files will only appear on the server once they are fully downloaded. If you have applications that require full files to be present and cannot tolerate tiered files in its namespace, this is the option for you.    

#### Proactive recalling mode

When a file is created or modified, you now have the option to proactively recall a file to servers that you specify. This makes the new or modified file readily available for consumption in each server you specified. 

For example, say you have a global company, with offices in the US and India. If the US server endpoint and India server endpoint are in the same sync group, by enabling proactive recalling for India’s server endpoint, any new/modified files uploaded via the US server endpoint will be readily available for consumption on India's server. Without selecting this option, these files will appear as tiered files on India's server, and will have to be manually recalled. 

One thing you might want to keep in mind is that if files recalled to the server are not actually needed locally, then the unnecessary recall can increase your egress traffic and bill from Azure. Therefore, only enable proactive recalling when you know that pre-populating a server's cache with recent changes from the cloud will have a positive effect on users or applications using the files on that server. 

Enabling proactive recalling may also result in increased bandwidth usage on the server and may cause other relatively new content on the local server to be aggressively tiered due to the increase in files being recalled. In turn, this may lead to more recalls if the files being tiered are considered hot by servers. 

:::image type="content" source="media/storage-sync-files-deployment-guide/proactive-download.png" alt-text="An image showing the Azure file share download behavior for a server endpoint currently in effect and a button to open a menu that allows to change it.":::

For more details on both of these options, see [Deploy Azure File Sync](storage-sync-files-deployment-guide.md).

## Next Steps
* [Planning for an Azure File Sync deployment](storage-sync-files-planning.md)
* [Choosing cloud tiering policies](storage-sync-choosing-cloud-tiering-policies.md)
