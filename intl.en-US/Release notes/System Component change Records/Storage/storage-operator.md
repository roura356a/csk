---
keyword: [storage-operator, release notes for storage-operator]
---

# storage-operator

The storage-operator component is used to manage the lifecycle of storage components. This topic describes the release notes for storage-operator.

## Introduction

Storage Operator is used to manage the lifecycle of storage components. Storage Operator runs as a Deployment, which deploys and upgrades storage components based on the default configurations inherited from the image and the custom configurations provided by ConfigMaps. This helps reduce the complexity of container development and maintenance.

-   Default configurations: Storage Operator provides the default configurations of storage components. The default configurations vary based on the version of Storage Operator.
-   Custom configurations: ConfigMaps can be used to define custom configurations of storage components, such as version information, and whether to install the component.

Storage Operator preferably uses custom configurations. The default configurations are used only when the custom configurations are not specified.

![Storage-Operator](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/4352669161/p253634.png)

**Note:**

-   Each image of Storage Operator contains the default configuration file.
-   When Storage Operator runs as a Deployment, the Deployment reads configurations from a ConfigMap file that is mounted on the Deployment and contains configurations of storage components.
-   Storage Operator determines whether to deploy and upgrade a storage component by combining the default and custom configurations.

## Usage notes

For more information about how to deploy storage-operator, see [Use Storage Operator to deploy and upgrade storage components](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/Container Storage O&M/Use Storage Operator to deploy and upgrade storage components.md).

## Release notes

**June 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.18.8.37-c63030b-aliyun|registry-vpc.$\{region\}.aliyuncs.com/acs/storage-operator:v1.18.8.37-c63030b-aliyun|2021-06-25|-   Automatic expansion is supported.
-   Container Network File System \(CNFS\) is supported.

|No impact on workloads|

**March 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.18.8.28-18cca7b-aliyun|registry-vpc.$\{region\}.aliyuncs.com/acs/storage-operator:v1.18.8.28-18cca7b-aliyun|2021-03-25|New Features:

-   Batch snapshots are supported.
-   Scheduled snapshots are supported.
-   Cluster monitoring is supported.

|No impact on workloads|

