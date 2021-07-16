---
keyword: aliyun-acr-credential-helper组件变更
---

# aliyun-acr-credential-helper

aliyun-acr-credential-helper是一个可以在ACK集群中免密拉取ACR默认版或企业版私有镜像的组件。本文介绍aliyun-acr-credential-helper组件的功能介绍、使用说明及变更记录。

## 组件介绍

aliyun-acr-credential-helper会默认安装在所有ACK集群中。通过读取ACK集群内的kube-system命名空间中的acr-configuration的配置，进行私有镜像拉取。

-   支持拉取容器镜像服务企业版实例和个人版实例中的私有镜像。
-   支持拉取集群当前用户容器镜像服务中的私有镜像，通过跨账号授权或AccessKey ID和AccessKey Secret配置可以拉取其他用户的私有镜像。
-   支持跨地域拉取容器镜像服务中的私有镜像。

## 使用说明

关于免密组件的使用详情，请参见[使用免密组件拉取容器镜像](/intl.zh-CN/Kubernetes集群用户指南/应用/镜像/使用免密组件拉取容器镜像.md)或[通过自建Kubernetes集群的免密组件拉取容器镜像](/intl.zh-CN/Kubernetes集群用户指南/多云混合云/注册集群管理/通过自建Kubernetes集群的免密组件拉取容器镜像.md)。

## 变更记录

**2021年01月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v21.01.26.0-9ac7d9b-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/aliyun-acr-credential-helper:v21.01.26.0-9ac7d9b-aliyun|2021年01月26日|优化鉴权方式。|此次升级不会对业务造成影响。|

**2020年08月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v20.08.20.0-c2da10b-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/aliyun-acr-credential-helper:v20.08.20.0-c2da10b-aliyun|2020年08月24日|修复因令牌过期导致拉取私有镜像失败的问题。|此次升级不会对业务造成影响。|

**2020年07月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v20.07.13.0-2866ccd-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/aliyun-acr-credential-helper:v20.07.13.0-2866ccd-aliyun|2020年07月13日|新功能：

-   支持以内网访问的方式进行OpenAPI调用。
-   支持自定义AccessKey ID和AccessKey Secret的方式获取镜像拉取凭证。

改进：减少OpenAPI调用频次。

|此次升级不会对业务造成影响。|

**2020年03月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v20.03.16.0-36d5d7e-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/aliyun-acr-credential-helper:v20.03.16.0-36d5d7e-aliyun|2020年03月16日|新功能：支持拉取跨账号私有镜像。|此次升级不会对业务造成影响。|

