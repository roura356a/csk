# Overview

Serverless Kubernetes \(ASK\) clusters can be automatically bound to Alibaba Cloud disks, Apsara File Storage NAS \(NAS\) file systems, and Object Storage Service \(OSS\) buckets that are mounted to pods. This topic describes the supported storage services and how to use the services.

|Alibaba Cloud storage|Static volume|Dynamic volume|
|---------------------|-------------|--------------|
|Alibaba Cloud disk|You can use a statically provisioned disk in the following ways:-   Use the disk as a volume
-   Use the disk by creating a pair of persistent volume \(PV\) and persistent volume claim \(PVC\)

|Disks can be mounted and used as dynamic volumes.|
|NAS file system|You can use a statically provisioned NAS file system in the following ways:-   Through the FlexVolume plug-in
    -   Use the NAS file system as a volume
    -   Use the NAS file system by creating a pair of PV and PVC
-   Through the Network File System \(NFS\) driver

|NAS file systems cannot be mounted and used as dynamic volumes.|
|OSS buckets|You can use a statically provisioned OSS bucket in the following ways:-   Use the OSS bucket as a volume
-   Use the OSS bucket by creating a pair of PV and PVC

|OSS buckets cannot be mounted and used as dynamic volumes.|

