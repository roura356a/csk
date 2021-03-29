---
keyword: [Flannel, VPC模式, kubernetes集群]
---

# Flannel

Flannel是一种容器网络接口CNI（Container Network Interface）插件，在阿里云上使用的Flannel网络模式采用阿里云VPC模式。您可以通过使用Flannel网络组件实现Kubernetes集群内部的网络互通。本文为您介绍了Flannel相关内容的最新动态。

Flannel网络插件是ACK的网络插件，使用独立于VPC网段的Pod的网段分配给Pod。在ACK中，Flannel插件采用的是阿里云VPC模式，报文经过阿里云VPC的路由表直接转发，不需要Vxlan等隧道封装技术封装报文，所以比Flannel默认的Vxlan模式具有更高的通信性能。更多信息，请参见[flannel](https://github.com/flannel-io/flannel/blob/master/Documentation/alicloud-vpc-backend.md)和[网络概述](/cn.zh-CN/Kubernetes集群用户指南/网络/网络概述.md)。

## 2019年08月

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v0.11.0.2-g6e46593e-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/flannel:v0.11.0.2-g6e46593e-aliyun|2019年08月02日|修复Flannel版本升级后源IP地址无法保留的问题。|此次升级不会对业务造成影响。|

