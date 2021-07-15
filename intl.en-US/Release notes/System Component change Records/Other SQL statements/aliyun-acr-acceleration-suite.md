---
keyword: [aliyun-acr-acceleration-suite, aliyun-acr-acceleration-suite release notes]
---

# aliyun-acr-acceleration-suite

This topic describes the features of the aliyun-acr-acceleration-suite component and lists the latest changes to aliyun-acr-acceleration-suite.

## Introduction

The aliyun-acr-acceleration-suite component is a client plug-in that enables on-demand image loading. This accelerates image loading. You can deploy this component on a worker node as a DaemonSet. Container Service for Kubernetes \(ACK\) collaborates with Container Registry to enable accelerated images. After you deploy aliyun-acr-acceleration-suite in a cluster, the system automatically converts a source image into an accelerated image. When an image pull request is received, the system decompresses data based on your needs without interrupting services. In this case, the system does not have to download or decompress full data. This speeds up application deployment and enables on-demand image loading.

**Note:** Only managed and dedicated Kubernetes clusters of Kubernetes 1.16.9 or later support accelerated images. When you create the cluster, set the container runtime to Docker and select one of the following operating systems: Aliyun Linux 2.1903, CentOS 7.6, CentOS 7.7, CentOS 7.8, and CentOS 7.9.

## Usage notes

For more information about aliyun-acr-acceleration-suite, see [Load resources of a container image on demand]().

## Release notes

**March 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|0.2.0|registry-vpc.cn-hangzhou.aliyuncs.com/acr-toolkit/aliyun-acr-acceleration-suite:v0.2.0.0-b745758c-aliyun|2021-03-15|-   HTTP readiness and HTTP liveness probes are supported for mutating admission webhooks.
-   Labels can be added to workloads to enable on-demand image loading. The key of the label is `k8s.aliyun.com/image-accelerate-mode`. The value of the label is `on-demand`.

**Note:** For more information about how to configure on-demand image loading, see [Load resources of a container image on demand]().


|No impact on workloads|

**November 2020**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|0.1.0|registry.cn-hangzhou.aliyuncs.com/acs/aliyun-acr-acceleration-suite:v0.1.0.0-00e2f5e1-aliyun|2020-11-19|Management of image storage plug-ins, configuration of access to image repositories, and automatic injection of accelerated images to pods are supported.|We recommend that you upgrade during off-peak hours.|

