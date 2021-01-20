# aliyun-acr-credential-helper

aliyun-acr-credential-helper is a component that can be used to pull images from instances of Container Registry Enterprise Edition and default instances. You do not need to provide a password to pull the images. This topic lists the latest changes to aliyun-acr-credential-helper.

## August 2020

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v20.08.20.0-c2da10b-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/aliyun-acr-credential-helper:v20.08.20.0-c2da10b-aliyun|August 24, 2020|The following issue is fixed: The failure to pull private images occurs due to an expired token.|

## July 2020

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v20.07.13.0-2866ccd-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/aliyun-acr-credential-helper:v20.07.13.0-2866ccd-aliyun|July 13, 2020|New features:

-   API operations can be called over internal networks.
-   Images can be pulled with the specified imagePullSecrets. These imagePullSecrets include the AccessKey IDs and AccessKey secrets of Resource Access Management \(RAM\) roles.

Improvements: The required number of API calls to pull images is reduced. |

## March 2020

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v20.03.16.0-36d5d7e-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/aliyun-acr-credential-helper:v20.03.16.0-36d5d7e-aliyun|March 16, 2020|New features: Private images can be pulled.|

