---
keyword: Release notes for aliyun-acr-credential-helper
---

# aliyun-acr-credential-helper

You can use the aliyun-acr-credential-helper component to pull private images without a password from instances of Container Registry Enterprise Edition and Personal Edition. This topic describes the features, usage notes, and release notes of the aliyun-acr-credential-helper component.

## Introduction

The aliyun-acr-credential-helper component is installed in Container Service for Kubernetes \(ACK\) clusters by default. The component retrieves the required information from the acr-configuration ConfigMap that is created in the kube-system namespace and then pulls private images.

-   You can use aliyun-acr-credential-helper to pull private images from instances of Container Registry Enterprise Edition and Personal Edition.
-   You can use the component to pull private images from your Container Registry instances. You can also pull private images from other accounts after authorization or by using the AccessKey ID and AccessKey secret.
-   You can use the component to pull private images from Container Registry instances that are deployed in different regions.

## Usage notes

For more information about how to use aliyun-acr-credential-helper, see [Use aliyun-acr-credential-helper to pull images without a password](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Image/Use aliyun-acr-credential-helper to pull images without a password.md) or [Pull images without a password in a self-managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Management of external clusters/Pull images without a password in a self-managed Kubernetes cluster.md).

## Release notes

**January 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v21.01.26.0-9ac7d9b-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/aliyun-acr-credential-helper:v21.01.26.0-9ac7d9b-aliyun|2021-01-26|The authentication methods are optimized.|No impact on workloads|

**August 2020**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v20.08.20.0-c2da10b-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/aliyun-acr-credential-helper:v20.08.20.0-c2da10b-aliyun|2020-08-24|The following issue is fixed: Private images fail to be pulled because the token is expired.|No impact on workloads|

**July 2020**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v20.07.13.0-2866ccd-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/aliyun-acr-credential-helper:v20.07.13.0-2866ccd-aliyun|2020-07-13|New Features:

-   API operations can be called over internal networks.
-   Images can be pulled by setting the imagePullSecrets field. This field specifies the AccessKey ID and AccessKey secret of the Resource Access Management \(RAM\) role that is assumed to pull images.

Optimization: The required number of API calls to pull images is reduced.

|No impact on workloads|

**March 2020**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v20.03.16.0-36d5d7e-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/aliyun-acr-credential-helper:v20.03.16.0-36d5d7e-aliyun|2020-03-16|New features: Private images can be pulled from a different account.|No impact on workloads|

