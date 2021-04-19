---
keyword: [ack-virtual-node, 变更记录]
---

# ack-virtual-node变更记录

ack-virtual-node组件是用于扩展Kubernetes集群弹性能力的组件，可部署在ACK或者线下Kubernetes集群中。本文介绍ack-virtual-node组件的最新动态。

ack-virtual-node将应用Pod以Serverless容器（ECI）方式运行，提供极致弹性、免容量规划、按需使用按需计费的能力。在Job类任务、CI/CD、Spark大数据计算、在线应用弹性等场景中可以显著提升应用部署的弹性效率，以及降低应用的计算成本。

关于ECI Pod功能的详细信息，请参见[ECI实例概述](/intl.zh-CN/Serverless Kubernetes集群用户指南/ECI Pod/ECI实例概述.md)。

![vk](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0265670061/p168908.png)

## 变更记录

|版本号|镜像地址|变更时间|变更内容|
|---|----|----|----|
|v1.0.0.10-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/virtual-nodes-eci:v1.0.0.10-aliyun|2020年9月21日|支持扩展集群弹性，应用Pod以Serverless容器（ECI）方式运行。|

[ack-virtual-node组件介绍及使用](/intl.zh-CN/产品发布记录/组件介绍与变更记录/其他/ack-virtual-node/ack-virtual-node组件介绍.md)

  


