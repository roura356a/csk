---
keyword: [migrate-controller, release notes of migrate-controller]
---

# migrate-controller

This topic describes the features of the migrate-controller component and lists the latest changes to this component.

## Overview

The migrate-controller component is developed by Alibaba Cloud based on the open source project Velero. You can use this component to migrate Container Service for Kubernetes \(ACK\) applications. For more information, see [Install migrate-controller](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Manage components/Install migrate-controller.md).

Velero is a cloud native tool that can be used to back up, restore, and migrate cluster applications. Velero is written in Golang. This tool ensures data security when you use it to back up, restore, and migrate applications and persistent volumes \(PVs\) in ACK clusters. For more information about source code of the Velero project, see [velero](https://github.com/vmware-tanzu/velero). For more information about the source code of the Velero plug-in for Alibaba Cloud, see [velero-plugin](https://github.com/AliyunContainerService/velero-plugin).

**Note:** Velero requires Kubernetes version later than V1.10.

## Release notes

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.0.1.1-30d319f-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/velero-installer:v1.0.1.1-30d319f-aliyun|September 18, 2020|-   Orchestration of ACK applications and the backup and restoration of PV data are supported.
-   Application orchestration and PV data migration are supported.
-   Scheduled backups are supported. |

**Related topics**  


[Install migrate-controller](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Manage components/Install migrate-controller.md)

