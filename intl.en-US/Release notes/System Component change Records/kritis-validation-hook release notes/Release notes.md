# Release notes

This topic lists the latest changes to kritis-validation-hook.

## November 2020

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v0.2.7.2-g5fa671a-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/kritis-server:v0.2.7.2-g5fa671a-aliyun|November 24, 2020|New features: The image verification whitelist feature is supported. When kritis-validation-hook verifies image signatures, the signatures of the images that are included in the whitelist are not verified.|
|v0.2.6.4-g94b0940-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/kritis-server:v0.2.6.4-g94b0940-aliyun|November 16, 2020|New features: Signature verification is supported for ACK images whose image versions are immutable. For more information, see [Configure a repository to be immutable]().|

## August 2020

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v0.2.5.26-g75d5297-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/kritis-server:v0.2.5.26-g75d5297-aliyun|August 12, 2020|New features:-   If a container image fails to pass the signature verification, a cluster event is generated in the kube-system namespace. The cause of this failure is **FailedKritisAdmission**.
-   The dry run mode is supported. By default, this mode is disabled.

When the dry run mode is enabled, container images that fail to pass the signature verification can be deployed. If an image that fails to pass the signature verification is deployed, a cluster event is generated in the kube-system namespace. The cause of this failure is **DryRunKritisAdmission**. |

## June 2020

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v0.2.4.1-ge5c1265-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/kritis-server:v0.2.4.1-ge5c1265-aliyun|June 22, 2020|Signature verification is supported for signed Container Registry images in different regions.|

## April 2020

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v0.2.3.1-00e70883-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/kritis-server:v0.2.3.1-00e70883-aliyun|April 7, 2020|Program performance is improved and log content is optimized.|

## March 2020

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v0.2.2.3-fe8a6319-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/kritis-server:v0.2.2.3-fe8a6319-aliyun|March 18, 2020|New features: kritis-validation-hook is integrated with Container Registry. You can verify the signatures of KMS-signed images. This allows you to make sure that only trusted images are deployed in ACK clusters.|

**Related topics**  


[kritis-validation-hook introduction](/intl.en-US/Release notes/System Component change Records/kritis-validation-hook release notes/kritis-validation-hook introduction.md)

[Use kritis-validation-hook to automatically verify the signatures of container images](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Use kritis-validation-hook to automatically verify the signatures of container images.md)

