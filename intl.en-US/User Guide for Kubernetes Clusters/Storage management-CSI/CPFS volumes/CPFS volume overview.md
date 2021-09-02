---
keyword: [CPFS volume, overview]
---

# CPFS volume overview

You can use Cloud Paralleled File System \(CPFS\) volumes in Container Service for Kubernetes \(ACK\) clusters. This topic describes the features, specifications, use scenarios, limits, and billing rules of CPFS volumes.

## Features

CPFS is a fully managed and expandable parallel file system solution provided by Alibaba Cloud to meet the requirements in high-performance computing scenarios. CPFS provides a unified namespace that allows concurrent access from hundreds of clients. It provides tens of GB/s of throughput, millions of input/output operations per second \(IOPS\), and sub-millisecond latency.

## CPFS specifications

## Scenarios

CPFS is applicable to scenarios that require high throughput, including AI training, video rendering, gene sequencing, autonomous driving, astronomy, meteorology, oil and gas exploration, and Electronic design automation \(EDA\)-aided simulation and design.

You can perform the following operations on a CPFS volume to meet your business requirements.

|Operation|References|
|---------|----------|
|Store application data|For more information, see:

-   [Static volumes](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/CPFS volumes/Static volumes.md)
-   [Dynamically provisioned CPFS volumes](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/CPFS volumes/Dynamically provisioned CPFS volumes.md) |

## Billing rules

For more information about the billable items, billing methods, and prices of CPFS, see [Billable item and billing methods]().

