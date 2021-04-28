# aliyun-acr-credential-helper

Container Registry provides the aliyun-acr-credential-helper component for you to pull private images without a password from instances of Container Registry Enterprise Edition and Personal Edition. This topic lists the latest changes to aliyun-acr-credential-helper.

## January 2021

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v21.01.26.0-9ac7d9b-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/aliyun-acr-credential-helper:v21.01.26.0-9ac7d9b-aliyun|January 26, 2021|The authentication methods are optimized.|None|

## August 2020

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v20.08.20.0-c2da10b-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/aliyun-acr-credential-helper:v20.08.20.0-c2da10b-aliyun|August 24, 2020|The following issue is fixed: Private images fail to be pulled because the token is expired.|None|

## July 2020

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v20.07.13.0-2866ccd-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/aliyun-acr-credential-helper:v20.07.13.0-2866ccd-aliyun|July 13, 2020|New Features:

-   API operations can be called over internal networks.
-   Images can be pulled by setting the imagePullSecrets field. This field specifies the AccessKey ID and AccessKey Secret of the Resource Access Management \(RAM\) role that is assumed to pull images.

Optimization: The required number of API calls to pull images is reduced.

|None|

## March 2020

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v20.03.16.0-36d5d7e-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/aliyun-acr-credential-helper:v20.03.16.0-36d5d7e-aliyun|March 16, 2020|New features: Private images can be pulled from a different account.|None|

