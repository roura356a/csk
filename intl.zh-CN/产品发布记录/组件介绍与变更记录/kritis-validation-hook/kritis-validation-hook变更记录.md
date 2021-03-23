---
keyword: [kritis-validation-hook, 发布记录]
---

# kritis-validation-hook变更记录

kritis-validation-hook组件是部署可信容器环节中进行容器镜像签名验证的关键组件。本文为您介绍kritis-validation-hook组件相关内容的最新动态。

## 组件介绍

关于kritis-validation-hook组件更多信息，请参见[kritis-validation-hook组件介绍](/intl.zh-CN/产品发布记录/组件介绍与变更记录/kritis-validation-hook/kritis-validation-hook组件介绍.md)。

## 2021年03月

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v0.3.1.4-ga89b624-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/kritis-server:v0.3.1.4-ga89b624-aliyun|2021年03月24日|新功能：支持对仓库名称包含反斜杠（ / ）的镜像进行签名验证。|此次升级不会对业务造成影响。|

## 2020年11月

|版本号|镜像地址|变更时间|变更内容|
|---|----|----|----|
|v0.2.7.2-g5fa671a-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/kritis-server:v0.2.7.2-g5fa671a-aliyun|2020年11月24日|新功能：支持镜像验签白名单功能，验签时不会对白名单内的镜像进行签名验证。|
|v0.2.6.4-g94b0940-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/kritis-server:v0.2.6.4-g94b0940-aliyun|2020年11月16日|新功能：支持对启用了ACK镜像版本不可变功能的镜像进行签名验证。具体操作，请参见[开启镜像版本不可变]()。|

## 2020年8月

|版本号|镜像地址|变更时间|变更内容|
|---|----|----|----|
|v0.2.5.26-g75d5297-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/kritis-server:v0.2.5.26-g75d5297-aliyun|2020年08月12日|新增：-   默认在验签失败时，将在命名空间kube-system下产生一条原因为**FailedKritisAdmission**的事件。
-   新增dry-run模式（默认关闭）。

当开启dry-run模式时将放行验签失败的请求，同时会在命名空间kube-system下产生一条原因为**DryRunKritisAdmission**的事件。 |

## 2020年6月

|版本号|镜像地址|变更时间|变更内容|
|---|----|----|----|
|v0.2.4.1-ge5c1265-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/kritis-server:v0.2.4.1-ge5c1265-aliyun|2020年06月22日|支持跨地域验证已加签的ACR镜像。|

## 2020年4月

|版本号|镜像地址|变更时间|变更内容|
|---|----|----|----|
|v0.2.3.1-00e70883-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/kritis-server:v0.2.3.1-00e70883-aliyun|2020年04月07日|优化程序性能、改进程序日志内容。|

## 2020年3月

|版本号|镜像地址|变更时间|变更内容|
|---|----|----|----|
|v0.2.2.3-fe8a6319-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/kritis-server:v0.2.2.3-fe8a6319-aliyun|2020年03月18日|新功能：与容器镜像服务深度合作，支持对经过KMS签名的容器镜像进行签名验证，确保在ACK上只部署可信容器镜像。|

**相关文档**  


[kritis-validation-hook组件介绍](/intl.zh-CN/产品发布记录/组件介绍与变更记录/kritis-validation-hook/kritis-validation-hook组件介绍.md)

[使用kritis-validation-hook组件实现自动验证容器镜像签名](/intl.zh-CN/Kubernetes集群用户指南/应用/镜像/使用kritis-validation-hook组件实现自动验证容器镜像签名.md)

