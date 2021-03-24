---
keyword: [aliyun-acr-acceleration-suite, release notes for aliyun-acr-acceleration-suite]
---

# aliyun-acr-acceleration-suite

This topic describes the features of the aliyun-acr-acceleration-suite component and lists the latest changes to aliyun-acr-acceleration-suite.

## Overview

The aliyun-acr-acceleration-suite component is a client plug-in that can load image resources based on your needs. This speeds up image loading. You can deploy this component on a worker node as a DaemonSet. Container Registry can automatically convert base images to minimal images. You can use this feature with aliyun-acr-acceleration-suite to deploy applications from minimal images. Image packages can be decompressed online and you do not need to download all files in the image package. This significantly accelerates application deployment and provides great elasticity for your business. For more information, see [Load resources of a container image on demand]().

**Note:** Only managed and dedicated Kubernetes clusters of Kubernetes 1.16.9 support minimal images. To use minimal images in an ACK cluster, you must set the container runtime to Docker and select Aliyun Linux 2.1903 or CentOS 7.7 as the operating system of the worker nodes when you create the cluster.

## Release notes

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|0.1.0|registry.cn-hangzhou.aliyuncs.com/acs/aliyun-acr-acceleration-suite:v0.1.0.0-00e2f5e1-aliyun|November 19, 2020|Allows you to manage image storage plug-ins, configure access to image repositories, and automatically inject minimal images to pods.|We recommend that you perform an upgrade during off-peak hours.|

