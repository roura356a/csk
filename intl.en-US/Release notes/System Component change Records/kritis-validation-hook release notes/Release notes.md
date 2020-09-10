# Release notes

This topic lists the latest changes to kritis-validation-hook.

## August 2020

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v0.2.5.26-g75d5297-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/kritis-server:v0.2.5.26-g75d5297-aliyun|August 12, 2020|New features:-   If a container image fails the signature verification, a cluster event is generated. The cause of this event is **FailedKritisAdmission**.
-   Supports the dry run mode. By default, this mode is disabled.

When the dry run mode is enabled, container images that fail the signature verification can be deployed. If an image that fails the signature verification is deployed, a cluster event is generated. The cause of this event is **DryRunKritisAdmission**. |

## June 2020

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v0.2.4.1-ge5c1265-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/kritis-server:v0.2.4.1-ge5c1265-aliyun|June 22, 2020|Supports signature verification for signed Alibaba Cloud Container Registry \(ACR\) images in different regions.|

## April 2020

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v0.2.3.1-00e70883-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/kritis-server:v0.2.3.1-00e70883-aliyun|April 07, 2020|Improves performance and optimizes log content.|

## March 2020

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v0.2.2.3-fe8a6319-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/kritis-server:v0.2.2.3-fe8a6319-aliyun|March 18, 2020|New features: kritis-validation-hook is integrated with Container Registry. This allows you to verify the signatures of KMS-signed images. This ensures that only trusted images are deployed in clusters of Alibaba Cloud Container Service for Kubernetes \(ACK\).|

**Related topics**  


[kritis-validation-hook introduction](/intl.en-US/Release notes/System Component change Records/kritis-validation-hook release notes/kritis-validation-hook introduction.md)

[Use kritis-validation-hook to automatically verify the signatures of container images](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Use kritis-validation-hook to automatically verify the signatures of container images.md)

