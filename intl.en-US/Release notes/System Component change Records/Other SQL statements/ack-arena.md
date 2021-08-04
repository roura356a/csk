---
keyword: [introduction to ack-arena, ack-arena usage notes, ack-arena release notes]
---

# ack-arena

The ack-arena component is used to simplify the installation of open source Arena. You can install ack-arena in the Container Service for Kubernetes \(ACK\) console with a few clicks. This topic introduces ack-arena, describes the usage notes of ack-arena, and lists the latest changes to ack-arena.

## Introduction

Arena is a lightweight client that is used to manage Kubernetes-based machine learning tasks. Arena allows you to streamline data preparation, model development, model training, and model prediction throughout a complete lifecycle of machine learning. This improves the work efficiency of data scientists. Arena is also deeply integrated with the basic services of Alibaba Cloud. It supports GPU sharing and Cloud Paralleled File System \(CPFS\). Arena can run in deep learning frameworks optimized by Alibaba Cloud. This maximizes the performance and utilization of heterogeneous computing resources provided by Alibaba Cloud.

ack-arena is used to simplify the installation of open source Arena. You can install ack-arena in the ACK console with a few clicks. This way, you can deploy Arena in your ACK clusters in an efficient manner.

## Usage notes

For more information about how to use ack-arena, see [Install Arena](/intl.en-US/Cloud-native AI User Guide/Environment preparation/Install Arena.md).

## Release notes

**June 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|0.8.5-264b96a-aliyun|-   registry.cn-beijing.aliyuncs.com/acs/arena-binary-installer:0.8.5-264b96a-aliyun
-   registry.cn-beijing.aliyuncs.com/acs/arena-deploy-manager:0.8.5-264b96a-aliyun

|2021-06-08|-   The issue that the total number of GPUs is 0 in the output of the arena top node command is fixed.
-   The issue that role-based access control \(RBAC\) permissions are not granted to manage CronJobs is fixed.
-   Inference tasks of the NVIDIA Triton type are supported.
-   The arena-uninstall command is supported to uninstall Arena.

|No impact on workloads|

**April 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|0.8.0-ba37c8a-aliyun|-   registry.cn-beijing.aliyuncs.com/acs/arena-binary-installer:0.8.0-ba37c8a-aliyun
-   registry.cn-beijing.aliyuncs.com/acs/arena-deploy-manager:0.8.0-ba37c8a-aliyun

|2021-04-06|-   The issue that Spark jobs cannot be submitted is fixed.
-   The issue that the LogViewer URL cannot be obtained when no chief pods are provisioned is fixed.
-   Arena SDK for Python and Arena SDK for Java are supported.
-   Inference tasks of the Seldon type can be submitted.
-   kubeconfig scripts can be generated for multiple tenants to use Arena.
-   The startup sequence of roles can be customized for TensorFlow training jobs.

|No impact on workloads|

**January 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|0.7.1-3559f56-aliyun|-   registry.cn-beijing.aliyuncs.com/acs/arena-binary-installer:0.7.1-3559f56-aliyun
-   registry.cn-beijing.aliyuncs.com/acs/arena-deploy-manager:0.7.1-3559f56-aliyun

|2021-01-27|The issue that et-operator is not installed in the arena-system namespace is fixed.|No impact on workloads|
|0.7.0-c6f5800-aliyun|-   registry.cn-beijing.aliyuncs.com/acs/arena-binary-installer:0.7.0-c6f5800-aliyun
-   registry.cn-beijing.aliyuncs.com/acs/arena-deploy-manager:0.7.0-c6f5800-aliyun

|2021-01-25|-   The issue that multiple jobs are deleted by running the arena serve delete command is fixed.
-   The output formats of the arena list, arena get, arena serve list, and arena serve get commands are modified.
-   Arena SDK for Go is supported.
-   Application Real-Time Monitoring Service \(ARMS\) Prometheus is supported.
-   The `-g` option is supported by the arena get command to display GPUs.
-   The `-c` option is supported by the arena logs command to specify a container.

|No impact on workloads|

