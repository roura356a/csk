---
keyword: [kritis-validation-hook, release notes]
---

# kritis-validation-hook

kritis-validation-hook is a key component that is used to verify image signatures. This topic describes the features and usage notes of kritis-validation-hook. This topic also lists the latest changes to kritis-validation-hook.

## Introduction

kritis-validation-hook is a key component that is used to verify image signatures. You can use signature verification to ensure that only images signed by trusted authorities are deployed. This reduces the risk of malicious code execution. For more information about kritis-validation-hook, see [kritis-validation-hook introduction](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Image/kritis-validation-hook introduction.md).

## Usage notes

For more information about how to use kritis-validation-hook, see [Use kritis-validation-hook to automatically verify the signatures of container images](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Image/Use kritis-validation-hook to automatically verify the signatures of container images.md).

## Release notes

**June 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v0.4.0.1-gb2862c4-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/kritis-server:v0.4.0.1-gb2862c4-aliyun|2021-06-10|New feature: kritis-validation-hook can be installed in registered Kubernetes clusters.|No impact on workloads|

**March 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v0.3.1.4-ga89b624-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/kritis-server:v0.3.1.4-ga89b624-aliyun|2021-03-24|New features: Images from repositories whose names contain forward slashes \(/\) are supported.|No impact on workloads|

**November 2020**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v0.2.7.2-g5fa671a-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/kritis-server:v0.2.7.2-g5fa671a-aliyun|2020-11-24|The image verification whitelist feature is supported. When kritis-validation-hook verifies image signatures, the signatures of the images that are included in the whitelist are not verified.|No impact on workloads|
|v0.2.6.4-g94b0940-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/kritis-server:v0.2.6.4-g94b0940-aliyun|2020-11-16|New features: Signature verification is supported for Container Service for Kubernetes \(ACK\) images whose image versions are immutable. For more information, see [Configure a repository to be immutable]().|No impact on workloads|

**August 2020**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v0.2.5.26-g75d5297-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/kritis-server:v0.2.5.26-g75d5297-aliyun|2020-08-12|-   If a container image fails to pass the signature verification, a cluster event is generated in the kube-system namespace. The cause of this failure is **FailedKritisAdmission**.
-   The dry run mode is supported. By default, this mode is disabled.

When the dry run mode is enabled, container images that fail to pass the signature verification can be deployed. If an image that fails to pass the signature verification is deployed, a cluster event is generated in the kube-system namespace. The cause of this event is **DryRunKritisAdmission**.


|No impact on workloads|

**June 2020**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v0.2.4.1-ge5c1265-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/kritis-server:v0.2.4.1-ge5c1265-aliyun|2020-06-22|Signature verification is supported for signed Container Registry images used across regions.|No impact on workloads|

**April 2020**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v0.2.3.1-00e70883-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/kritis-server:v0.2.3.1-00e70883-aliyun|2020-04-07|Program performance is improved and log content is optimized.|No impact on workloads|

**March 2020**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v0.2.2.3-fe8a6319-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/kritis-server:v0.2.2.3-fe8a6319-aliyun|2020-03-18|kritis-validation-hook is integrated with Container Registry. You can verify the signatures of Key Management Service \(KMS\)-signed images. This allows you to make sure that only trusted images are deployed in ACK clusters.|No impact on workloads|

