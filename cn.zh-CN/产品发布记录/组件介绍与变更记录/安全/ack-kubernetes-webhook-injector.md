---
keyword: [ack-kubernetes-webhook-injector, 加入或移出白名单]
---

# ack-kubernetes-webhook-injector

在对权限要求相对较高的云上场景中，需要将Pod的IP地址动态地加入或移出指定的阿里云产品白名单，以实现对权限最细粒度的控制。您可以使用ack-kubernetes-webhook-injector通过为Pod添加注解的方式动态地将Pod IP加入或移出白名单。本文介绍ack-kubernetes-webhook-injector组件的使用说明和变更记录。

## 组件介绍

ack-kubernetes-webhook-injector是一款可以从多种阿里云产品白名单中动态加入或移出Pod IP的K8s组件，免去手动配置Pod IP到云产品白名单的操作。ack-kubernetes-webhook-injector组件架构如下图所示：

![webhook](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3071293261/p283778.png)

## 使用说明

关于ack-kubernetes-webhook-injector组件使用，请参见[使用ack-kubernetes-webhook-injector动态配置安全组和RDS白名单策略](/cn.zh-CN/Kubernetes集群用户指南/安全/基础设施安全/使用ack-kubernetes-webhook-injector动态配置安全组和RDS白名单策略.md)。

## 变更记录

**2021年04月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v0.0.3-d63ac7e|registry-vpc.cn-hangzhou.aliyuncs.com/acs/k8s-webhook-injector:v0.0.3-d63ac7e|2021年04月12日|新增SLB访问控制功能。|此次升级不会对业务造成影响。|

