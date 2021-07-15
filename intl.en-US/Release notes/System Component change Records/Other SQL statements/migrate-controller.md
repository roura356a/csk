---
keyword: [migrate-controller, release notes for migrate-controller]
---

# migrate-controller

The migrate-controller component is developed by Alibaba Cloud based on the open source project Velero. You can use this component to migrate Kubernetes applications. This topic lists the latest changes to migrate-controller.

## Introduction

For more information about how to install migrate-controller, see [Install the application backup component](/intl.en-US/User Guide for Kubernetes Clusters/Disaster recovery center/Install the application backup component.md).

Velero is a cloud-native tool that can be used to back up, restore, and migrate applications in Kubernetes clusters. Velero is written in Go. Velero ensures data security when you use it to back up, restore, and migrate applications and persistent volumes \(PVs\) in ACK clusters. For more information about the source code of the Velero project, see [Velero](https://github.com/vmware-tanzu/velero). For more information about the source code of the Velero plug-in for Alibaba Cloud, see [velero-plugin](https://github.com/AliyunContainerService/velero-plugin).

**Note:** Velero requires Kubernetes versions later than V1.10.

## Usage notes

You can use migrate-controller to back up, restore, and migrate applications. For more information, see [Back up and restore applications](/intl.en-US/User Guide for Kubernetes Clusters/Disaster recovery center/Back up and restore applications.md) and [Migrate applications across clusters](/intl.en-US/User Guide for Kubernetes Clusters/Disaster recovery center/Migrate applications across clusters.md).

## Release notes

**March 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.3.1-3026c10-aliyun|registry-vpc.\{\{.Region\}\}.aliyuncs.com/acs/velero-installer:v1.3.1-3026c10-aliyun|2021-03-31|Features are upgraded and bugs are fixed.|Ongoing backup tasks may be interrupted.|
|v1.3.1-665c3ea-aliyun|registry.\{\{.Region\}\}.aliyuncs.com/acs/velero-installer:v1.3.1-665c3ea-aliyun|2021-03-31|Volume snapshots are supported in registered Kubernetes clusters.|Ongoing backup tasks may be interrupted.|
|v1.3.0-9274575-aliyun|registry-vpc.\{\{.Region\}\}.aliyuncs.com/acs/velero-installer:v1.0.1.2-h-e616322-aliyun|2021-03-29|Volume snapshots are supported to back up volumes.|Ongoing backup tasks may be interrupted. To upgrade the component, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm) to contact technical support team.|

**December 2020**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.1.2-h-e616322-aliyun|registry.\{\{.Region\}\}.aliyuncs.com/acs/velero-installer:v1.0.1.2-h-e616322-aliyun|2020-12-24|Velero is upgraded to the open source version 1.5.2 in registered Kubernetes clusters.|Ongoing backup tasks may be interrupted. To upgrade the component, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm) to contact technical support team.|
|v1.0.1.2-a-e616322-aliyun|registry-vpc.\{\{.Region\}\}.aliyuncs.com/acs/velero-installer:v1.0.1.2-h-e616322-aliyun|2020-12-30|Velero is upgraded to the open source version 1.5.2 in managed and dedicated Kubernetes clusters.|Ongoing backup tasks may be interrupted. To upgrade the component, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm) to contact technical support team.|

**September 2020**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.1.1-30d319f-aliyun|registry.\{\{.Region\}\}.aliyuncs.com/acs/velero-installer:v1.0.1.1-30d319f-aliyun|2020-09-21|Application migration, backup, and restoration are supported in registered Kubernetes clusters and managed Kubernetes clusters.|migrate-controller is released for the first time.|

