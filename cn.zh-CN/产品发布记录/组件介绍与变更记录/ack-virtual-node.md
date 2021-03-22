---
keyword: [ack-virtual-node, 虚拟节点组件, 变更记录]
---

# ack-virtual-node

本文为您介绍ack-virtual-node组件，以及相关内容的最新动态。

有关在应用目录中部署ack-virtual-node组件的详情，请参见[在ACK集群中部署虚拟节点Addon](/cn.zh-CN/Kubernetes集群用户指南/弹性容器实例ECI/通过部署ACK虚拟节点组件创建ECI Pod.md)。

## ack-virtual-node组件介绍

ack-virtual-node是基于社区开源项目Virtual Kubelet，扩展了对Aliyun Provider的支持，并做了大量优化，实现Kubernetes与弹性容器实例ECI的无缝连接。Kubernetes集群可以轻松获得极大的弹性能力，而不必受限于集群的节点计算容量。您可以灵活动态的按需创建ECI Pod，免去集群容量规划的麻烦。

## 2021年03月

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v2.0.0.86-9005a977d-aliyun|registry-vpc.$RegionId.aliyuncs.com/acs/virtual-nodes-eci:v2.0.0.86-9005a977d-aliyun|2021年03月17日|-   优化/etc/hosts，添加Domain和DNS注解。
-   缩短clusterrolebinding检查时间。
-   优化多组件选主机制。

|此次升级不会对业务造成影响。|

## 2021年02月

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v2.0.0.80-f9a46a994-aliyun|registry-vpc.$RegionId.aliyuncs.com/acs/virtual-nodes-eci:v2.0.0.80-f9a46a994-aliyun|2021年02月25日|优化CreatePod状态判断功能：只有为pending时才创建Pod。|此次升级不会对业务造成影响。|
|v2.0.0.76-6e9e19bd5-aliyun|registry-vpc.$RegionId.aliyuncs.com/acs/virtual-nodes-eci:v2.0.0.76-6e9e19bd5-aliyun|2021年02月22日|-   支持PProf调试。
-   支持选主开关控制。
-   为EndPointSlice更新ClusterRole。
-   在Pod中添加K8s版本注解。
-   不同K8s版本支持配置不同Webhook。

|此次升级不会对业务造成影响。|

## 2020年12月

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v2.0.0.618-7fd50d738-aliyun|registry-vpc.$RegionId.aliyuncs.com/acs/virtual-nodes-eci:v2.0.0.618-7fd50d738-aliyun|2020年12月04日|-   支持更好的Pod兼容性。
-   支持多个VK Controller选主逻辑。

|需要您确认ECI Pod的VPC和安全组配置正确，且可以访问集群APIserver，否则会导致Pod一直等待状态。|

## 2020年03月

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.0.0.2-aliyun|registry-vpc.$RegionId.aliyuncs.com/acs/virtual-nodes-eci:v1.0.0.2-aliyun|2020年03月12日|-   使用StatefulSet部署virtual-nodes-eci controller，方便修改副本数量以创建多个VK虚拟节点，支持更大规模ECI Pod。
-   节点名字变更为virtual-node-eci-$n。
-   支持访问ClusterIP Service。
-   支持Spot可抢占实例类型。
-   支持CSI挂载Disk Volume。

|如果您的virtual-nodes-eci controller是以Deployment形式部署，请先删除virtual-kubelet节点上的ECI，再更新或者重新部署组件。|

