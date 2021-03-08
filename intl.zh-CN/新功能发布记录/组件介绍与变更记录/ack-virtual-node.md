---
keyword: [ack-virtual-node, 虚拟节点组件, 变更记录]
---

# ack-virtual-node

本文为您介绍ack-virtual-node组件相关内容的最新动态。

有关在应用目录中部署ack-virtual-node组件的详情，请参见[在ACK集群中部署虚拟节点Addon](/intl.zh-CN/Kubernetes集群用户指南/弹性容器实例ECI/通过部署ACK虚拟节点组件创建ECI Pod.md)。

## 2020年12月

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v2.0.0.618-7fd50d738-aliyun|registry-vpc.$RegionId.aliyuncs.com/acs/virtual-nodes-eci:v2.0.0.618-7fd50d738-aliyun|2020年12月04日|-   支持更好的Pod兼容性。
-   支持多个vk controller选主逻辑。

|需要您确认ECI Pod的VPC和安全组配置正确，且可以访问集群APIserver，否则会导致Pod一直等待状态。|

## 2020年03月

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.0.0.2-aliyun|registry-vpc.$RegionId.aliyuncs.com/acs/virtual-nodes-eci:v1.0.0.2-aliyun|2020年03月12日|-   使用statefulset部署virtual-nodes-eci controller，方便修改副本数量以创建多个vk虚拟节点，支持更大规模ECI Pod。
-   节点名字变更为virtual-node-eci-$n。
-   支持访问clusterIP service。
-   支持spot可抢占实例类型。
-   支持CSI挂载disk volume。

|如果您的virtual-nodes-eci controller是以deployment形式部署，请先删除virtual-kubelet节点上的ECI，再更新或者重新部署组件。|

