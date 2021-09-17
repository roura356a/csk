---
keyword: ACK NodeLocal DNSCache
---

# ACK NodeLocal DNSCache

本文介绍ACK NodeLocal DNSCache组件信息、使用说明和变更记录。

## 组件介绍

ACK NodeLocal DNSCache是基于社区开源项目NodeLocal DNSCache的一套DNS本地缓存解决方案。ACK NodeLocal DNSCache的Helm Chart名称是ack-node-local-dns。本方案主要由DNS本地缓存DaemonSet和DNSConfig动态注入控制器Deployment组成，控制器会监听Pod创建的请求，动态地修改DNSConfig，使Pod可以使用DNS本地缓存的加速能力。

## 使用说明

您可以通过组件使用多种方式使业务容器Pod接入DNS本地缓存。具体操作，请参见[在ACK集群中使用NodeLocal DNSCache](/intl.zh-CN/Kubernetes集群用户指南/网络/服务发现DNS/在ACK集群中使用NodeLocal DNSCache.md)。

## 变更记录

**2021年09月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|1.3.4|-   缓存组件：registry.cn-hangzhou.aliyuncs.com/acs/k8s-dns-node-cache:v1.15.13-6-7e6778ac
-   控制器：registry.cn-hangzhou.aliyuncs.com/acs/node-local-dns-admission-controller:v1.0.2-8b46b2f-aliyun

|2021年09月16日|新增默认Memory资源限制，默认使用内网拉取镜像。|无|

**2021年04月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|1.3.3|-   缓存组件：registry.cn-hangzhou.aliyuncs.com/acs/k8s-dns-node-cache:v1.15.13-6-7e6778ac
-   控制器：registry.cn-hangzhou.aliyuncs.com/acs/node-local-dns-admission-controller:v1.0.2-8b46b2f-aliyun

|2021年04月21日|组件首次上线。|无|

