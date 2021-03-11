# aliyun-acr-credential-helper

aliyun-acr-credential-helper是一个可以在ACK集群中免密拉取ACR默认版或企业版私有镜像的组件。本文介绍aliyun-acr-credential-helper组件的最新动态。

## 2021年01月

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v21.01.26.0-9ac7d9b-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/aliyun-acr-credential-helper:v21.01.26.0-9ac7d9b-aliyun|2021年01月26日|优化鉴权方式。|无影响|

## 2020年08月

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v20.08.20.0-c2da10b-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/aliyun-acr-credential-helper:v20.08.20.0-c2da10b-aliyun|2020年08月24日|修复因令牌过期导致拉取私有镜像失败的问题。|无影响|

## 2020年07月

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v20.07.13.0-2866ccd-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/aliyun-acr-credential-helper:v20.07.13.0-2866ccd-aliyun|2020年07月13日|新功能：

-   支持以内网访问的方式进行OpenAPI调用。
-   支持自定义AccessKey ID和AccessKey Secret的方式获取镜像拉取凭证。

改进：减少OpenAPI调用频次。

|无影响|

## 2020年3月

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v20.03.16.0-36d5d7e-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/aliyun-acr-credential-helper:v20.03.16.0-36d5d7e-aliyun|2020年03月16日|新功能：支持拉取跨账号私有镜像。|无影响|

