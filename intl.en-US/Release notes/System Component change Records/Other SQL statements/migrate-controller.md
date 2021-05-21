---
keyword: [migrate-controller, release notes for migrate-controller]
---

# migrate-controller

The migrate-controller component is developed by Alibaba Cloud based on the open source project Velero. You can use this component to migrate Kubernetes applications. This topic lists the latest changes to migrate-controller.

## Introduction

For more information about how to use migrate-controller, see [Install migrate-controller](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Switch traffic to the Kubernetes application/Install migrate-controller.md).

Velero is a cloud native tool that can be used to back up, restore, and migrate applications in Kubernetes clusters. Velero is written in Go. Velero ensures data security when you use it to back up, restore, and migrate applications and persistent volumes \(PVs\) in ACK clusters. For more information about the source code of the Velero project, see [velero](https://github.com/vmware-tanzu/velero). For more information about the source code of the Velero plug-in for Alibaba Cloud, see [velero-plugin](https://github.com/AliyunContainerService/velero-plugin).

**Note:** Velero requires Kubernetes versions later than V1.10.

## March 2021

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.3.1-3026c10-aliyun|registry-vpc.\{\{.Region\}\}.aliyuncs.com/acs/velero-installer:v1.3.1-3026c10-aliyun|2021-03-31|Features are upgraded and bugs are fixed.|Ongoing backup tasks may be interrupted.|
|v1.3.1-665c3ea-aliyun|registry.\{\{.Region\}\}.aliyuncs.com/acs/velero-installer:v1.3.1-665c3ea-aliyun|2021-03-31|Volume snapshots are supported in registered external Kubernetes clusters.|Ongoing backup tasks may be interrupted.|
|v1.3.0-9274575-aliyun|registry-vpc.\{\{.Region\}\}.aliyuncs.com/acs/velero-installer:v1.0.1.2-h-e616322-aliyun|2021-03-29|Volume snapshots are supported to back up volumes.|Ongoing backup tasks may be interrupted. To upgrade the component, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm) to contact technical support.|

## December 2020

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.1.2-h-e616322-aliyun|registry.\{\{.Region\}\}.aliyuncs.com/acs/velero-installer:v1.0.1.2-h-e616322-aliyun|2020-12-24|Velero is upgraded to the open source version 1.5.2 in registered external Kubernetes clusters.|Ongoing backup tasks may be interrupted. To upgrade the component, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm) to contact technical support.|
|v1.0.1.2-a-e616322-aliyun|registry-vpc.\{\{.Region\}\}.aliyuncs.com/acs/velero-installer:v1.0.1.2-h-e616322-aliyun|2020-12-30|Velero is upgraded to the open source version 1.5.2 in managed and dedicated Kubernetes clusters.|Ongoing backup tasks may be interrupted. To upgrade the component, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm) to contact technical support.|

## September 2020

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.1.1-30d319f-aliyun|registry.\{\{.Region\}\}.aliyuncs.com/acs/velero-installer:v1.0.1.1-30d319f-aliyun|2020-09-21|Application migration, backup, and restoration are supported in registered external Kubernetes clusters and managed Kubernetes clusters.|First release.|

**Related topics**  


[Install migrate-controller](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Switch traffic to the Kubernetes application/Install migrate-controller.md)

[Use migrate-controller to back up and restore applications](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Switch traffic to the Kubernetes application/Use migrate-controller to back up and restore applications.md)

[Use migrate-controller to migrate applications across clusters](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Switch traffic to the Kubernetes application/Use migrate-controller to migrate applications across clusters.md)

