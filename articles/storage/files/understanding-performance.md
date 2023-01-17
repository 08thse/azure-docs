---
title: Understand Azure Files performance
description: Learn about the factors that can impact Azure file share performance and how to optimize performance for your workload.
author: khdownie
ms.service: storage
ms.topic: conceptual
ms.date: 01/17/2023
ms.author: kendownie
ms.subservice: files
---

# Understand Azure Files performance

Azure Files offers two performance tiers: standard and premium. Standard file shares are hosted on a storage system backed by hard disk drives (HDD), while premium file shares are backed by solid-state drives (SSD) to improve performance. Standard file shares have several storage tiers (transaction optimized, hot, amd cool) that you can seamlessly move between to maximize the data at-rest storage and transaction prices. However, you can't move between standard and premium tiers without physically migrating your data between different storage accounts.

## Applies to
| File share type | SMB | NFS |
|-|:-:|:-:|
| Standard file shares (GPv2), LRS/ZRS | ![Yes](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) |
| Standard file shares (GPv2), GRS/GZRS | ![Yes](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) |
| Premium file shares (FileStorage), LRS/ZRS | ![Yes](../media/icons/yes-icon.png) | ![Yes](../media/icons/yes-icon.png) |

## Glossary
Before reading this article, it's helpful to understand some key terms relating to storage performance:

-   **IO operations per second (IOPS)**

    IOPS measures the number of read and write operations per second.

-  **Throughput**

    Throughput measures the number of bits read or written per second, and is usually measured in megabytes per second (MB/s) or mebibytes per second (MiB/s). 

-  **Latency**

    Latency is a synonym for delay and is usually measured in milliseconds (ms). There are two types of latency: end-to-end latency and service latency. For more information, see Latency.

-  **Queue depth**

    Queue depth is the number of pending input/output (I/O) requests that a storage resource can handle at any one time.

-  **Application thread**

    Applications can be single threaded, which means that every operation is carried out sequentially, or multi-threaded, which means that multiple operations can be conducted at the same time for improved performance.

## Choosing a performance tier based on usage patterns

When choosing between standard and premium file shares, it's important to understand the requirements of the expected usage pattern you're planning to run on Azure Files.  

The following table summarizes the expected performance targets between standard and premium. For complete target details, see performance targets. 

| **Usage pattern requirements**                | **Standard** | **Premium** |
|-----------------------------------------------|--------------|-------------|
| Write latency (single-digit milliseconds)     | Yes          | Yes         |
| Read latency (single-digit milliseconds)      | Yes          | Yes         |
| Bandwidth > 300 MiB/s                         | No           | Yes         |
| IOPS > 20,000                                 | No           | Yes         |

Premium file shares offer a provisioning model that guarantees the following performance profile based on share size. For more information, see Provisioning model.

| **Capacity (GiB)** | **Baseline IOPS** | **Burst IOPS** | **Burst credits** | **Throughput (ingress + egress) (MiB/sec)** |
|--------------------|-------------------|----------------|-------------------|---------------------------------------------|
| 100                | 3,100             | Up to 10,000   | 24,840,000        | 110                                         |
| 500                | 3,500             | Up to 10,000   | 23,400,000        | 150                                         |
| 1,024              | 4,024             | Up to 10,000   | 21,513,600        | 203                                         |
| 5,120              | 8,120             | Up to 15,360   | 26,064,000        | 613                                         |
| 10,240             | 13,240            | Up to 30,720   | 62,928,000        | 1,125                                       |
| 33,792             | 36,792            | Up to 100,000  | 227,548,800       | 3,480                                       |
| 51,200             | 54,200            | Up to 100,000  | 164,880,000       | 5,220                                       |
| 102,400            | 100,000           | Up to 100,000  | 0                 | 10,340                                      |

## Performance checklist (maybe move this to later?)

Whether you're assessing performance requirements for a new or existing workload, understanding your usage patterns will help you achieve predictable performance. Consult with your storage admin or application developer to determine the following:

- **Latency sensitivity:** Are users opening files or interacting with virtual desktops that run on Azure Files? These are examples of read latency sensitive workloads that have high visibility to end users and are more suitable for a premium file share.

- **Maximum and average IOPS:** Is the workload 100% reads, 100% writes, or a mix such as 60%/40%? Do you need more than 20K IOPS at peak? Write IO intensive workloads that don't exceed 20K IOPS and aren't using large IO block size (64K or above) can achieve single-digit millisecond latency on a standard file share. Read IO with high queue depth (64) can also achieve single-digit latency on standard file shares.

- **Throughput** If the workload uses larger block size or more IOPS that will cause bandwidth to exceed 300 MiB/s per share or 60 MiB/s per file, then you should choose a premium file share over standard.

- **Workload duration and frequency:** Short (minutes) and infrequent (hourly) workloads will be less likely to achieve the upper performance limits of standard file shares compared to long-running, frequently occuring workloads. On premium file shares, workload duration is helpful when determining the correct performance profile to use based on the provisioning size. Depending on how long the workload needs to burst for and how long it spends bellow the baseline IOPS, you can determine if you're accumulating enough bursting credits to consistently satisfy your workload at peak times. Finding the right balance will reduce costs compared to over-provisioning the file share.

- **Workload parallelization:** For parallel supported workloads, it's easier to achieve the scale limits with fewer client machines by using SMB multichannel with SMB 3.1.1 on premium files. For more information, see SMB multichannel performance.

- **API operation distribution**: Is the workload metadata heavy with file open/close operations? This is common for workloads that are performing read operations against a large volume of files. For existing Azure Files workloads, see Monitoring metadata operations.

## Latency

When thinking about latency, it's important to first understand how latency is determined. The most common measurements are the latency associated with end-to-end latency and service latency metrics. Using these metrics can help identify client-side latency and/or networking issues by determining how much time your application traffic spends in transit to and from the client.

- **End-to-end latency (SuccessE2ELatency)** is the time it takes for a transaction to perform a complete round trip between the client and the Azure Files service.

- **Service Latency (SuccessServerLatency)** is the time it takes for a transaction to round-trip only within the Azure Files service. This doesn't include any client latency.

It's common to confuse client latency with service latency (in this case, Azure Files capabilities). For example, if the service latency is reporting low latency and the end-to-end is reporting very high latency, that suggests that all the time is spent in transit to and from the client, and not in the Azure Files service.

Furthermore, as the diagram below illustrates, the farther you are away from the service, the slower the latency experience will be, and the more difficult it will be to achieve performance scale limits with Azure Files. This is especially true when accessing Azure Files from on premises. While options like ExpressRoute are effective, they still don't match the performance of an application (compute + storage) that's running exclusively in the same Azure region.

> [!NOTE]
> Using a VM in Azure to test performance between on-premises and Azure is an effective and practical way to baseline the networking capabilities of the connection to Azure. Often a workload can be slowed down by an undersized or incorrectly routed ExpressRoute circuit or VPN gateway.

## Queue depth

Queue depth is the number of outstanding IO requests that a storage resource can service. As the disks used by storage systems have evolved from hard disk drive spindles (IDE, SATA, SAS) to solid state devices (SSD, NVMe), they have also evolved to support higher queue depth.

High queue depth can be achieved in several different ways in combination with many clients, files, and threads. To determine the queue depth, simply multiply the number of clients by the number of files by the number of threads (clients x files x threads = queue depth).

A workload consisting of a single client that serially interacts with a single file within a large dataset is an example of low queue depth. In contrast, a workload that supports parallelism with multiple threads and multiple files can easily achieve high queue depth. Because Azure Files is a distributed file service that spans thousands of Azure cluster nodes and is designed to run workloads at scale, it's easy to achieve high queue depth.

The table below illustrates the various combinations you can use to achieve higher queue depth. While you can exceed the optimal queue depth of 64, you won't see any additional performance gains if you do.

| **Clients** | **Files** | **Threads** | **Queue depth** |
|-------------|-----------|-------------|-----------------|
| 1           | 1         | 1           | 1               |
| 1           | 1         | 2           | 2               |
| 1           | 2         | 2           | 4               |
| 2           | 2         | 2           | 8               |
| 2           | 2         | 4           | 16              |
| 2           | 4         | 4           | 32              |
| 1           | 8         | 8           | 64              |
| 4           | 4         | 2           | 64              |

> [!TIP]
> To achieve upper performance limits, make sure that your workload or benchmarking test is multi-threaded with multiple files.

## Single versus multi-thread applications

Azure Files is best suited for multi-threaded applications. The easiest way to understand the performance impact that multi-threading has on a workload is to walk though the scenario by IO. In the following scenario, we have a workload that needs to copy 10,000 small files as quickly as possible to or from an Azure file share.

This table breaks down the time needed (in milliseconds) to create a single 16 KiB file on an Azure file share, based on a single-thread application that's writing in 4 KiB block sizes. 

| **IO operation** | **Create** | **4 KiB write** | **4 KiB write** | **4 KiB write** | **4 KiB write** | **Close** | **Total** |
|------------------|------------|-----------------|-----------------|-----------------|-----------------|-----------|-----------|
| Thread 1         | 3 ms       | 2 ms            | 2 ms            | 2 ms            | 2 ms            | 3 ms      | **14 ms** |

In this example, it would take approximately 14 ms to create a single 16 KiB file from the six operations. If a single-threaded application wants to move 10,000 files to an Azure file share, that translates to 140,000 ms (14 ms * 10,000) or 140 seconds, because each file is moved sequentially one at a time. Keep in mind that the time to service each request is primarily determined by how close the compute and storage are located to each other, as discussed in the previous section.

By using eight threads instead of one, the above workload can be reduced from 140,000 ms (140 seconds) down to 17,500 ms (17.5 seconds). As the table below shows, when you're moving eight files in parallel instead of one file at a time, you can move the same amount of file data in 87.5% less time.

| **IO operation** | **Create** | **4 KiB write** | **4 KiB write** | **4 KiB write** | **4 KiB write** | **Close** | **Total** |
|------------------|------------|-----------------|-----------------|-----------------|-----------------|-----------|-----------|
| Thread 1         | 3 ms       | 2 ms            | 2 ms            | 2 ms            | 2 ms            | 3 ms      | **14 ms** |
| Thread 2         | 3 ms       | 2 ms            | 2 ms            | 2 ms            | 2 ms            | 3 ms      | **14 ms** |
| Thread 3         | 3 ms       | 2 ms            | 2 ms            | 2 ms            | 2 ms            | 3 ms      | **14 ms** |
| Thread 4         | 3 ms       | 2 ms            | 2 ms            | 2 ms            | 2 ms            | 3 ms      | **14 ms** |
| Thread 5         | 3 ms       | 2 ms            | 2 ms            | 2 ms            | 2 ms            | 3 ms      | **14 ms** |
| Thread 6         | 3 ms       | 2 ms            | 2 ms            | 2 ms            | 2 ms            | 3 ms      | **14 ms** |
| Thread 7         | 3 ms       | 2 ms            | 2 ms            | 2 ms            | 2 ms            | 3 ms      | **14 ms** |
| Thread 8         | 3 ms       | 2 ms            | 2 ms            | 2 ms            | 2 ms            | 3 ms      | **14 ms** |



## See also
- [Troubleshoot Azure file shares performance issues](storage-troubleshooting-files-performance.md)
- [Planning for an Azure Files deployment](storage-files-planning.md)
- [Understanding billing](understanding-billing.md)
- [Azure Files pricing](https://azure.microsoft.com/pricing/details/storage/files/)
