---
keyword: [static volume, dynamic volume]
---

# Overview

Container Service for Kubernetes \(ACK\) clusters can be automatically bound to disks of Alibaba Cloud, Aspara File Storage NAS \(NAS\) file systems, and Object Storage Service \(OSS\) buckets that are mounted to pods. This topic describes the storage services that are supported by ACK and how to use these services.

ACK supports static volumes and dynamic volumes.

|Alibaba Cloud storage|Static volume|Dynamic volume|
|---------------------|-------------|--------------|
|Alibaba Cloud disk|You can statically provision a disk volume in the following ways:-   Directly create a disk volume
-   Create a pair of persistent volume \(PV\) and persistent volume claim \(PVC\)

|Supported|
|NAS file system|You can statically provision a NAS volume in the following ways:-   Use the FlexVolume plug-in
    -   Directly create a NAS volume
    -   Create a pair of persistent volume \(PV\) and persistent volume claim \(PVC\)
-   Use the Network File System \(NFS\) driver

|Supported|
|OSS bucket|You can statically provision an OSS volume in the following ways:-   Directly create a disk volume
-   Create a pair of persistent volume \(PV\) and persistent volume claim \(PVC\)

|Not supported|

