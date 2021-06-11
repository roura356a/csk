---
keyword: [ack-virtual-node, 虚拟节点组件, 变更记录]
---

# ack-virtual-node

本文为您介绍ack-virtual-node组件，以及相关内容的最新动态。

有关在应用目录中部署ack-virtual-node组件的详情，请参见[在ACK集群中部署虚拟节点Addon](/intl.zh-CN/Kubernetes集群用户指南/弹性容器实例ECI/通过部署ACK虚拟节点组件创建ECI Pod.md)。

## 组件介绍

ack-virtual-node是基于社区开源项目Virtual Kubelet，扩展了对Aliyun Provider的支持，并做了大量优化，实现Kubernetes与弹性容器实例ECI的无缝连接。Kubernetes集群可以轻松获得极大的弹性能力，而不必受限于集群的节点计算容量。您可以灵活动态的按需创建ECI Pod，免去集群容量规划的麻烦。

ack-virtual-node将应用Pod以Serverless容器（ECI）方式运行，提供极致弹性、免容量规划、按需使用按需计费的能力。在Job类任务、CI/CD、Spark大数据计算、在线应用弹性等场景中可以显著提升应用部署的弹性效率，以及降低应用的计算成本。

关于ECI Pod功能的详细信息，请参见[ECI实例概述](/intl.zh-CN/Serverless Kubernetes集群用户指南/ECI Pod/ECI实例概述.md)。

![vk](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0265670061/p168908.png)

## 使用说明

使用ack-virtual-node创建Pod。

1.  设置权限。

    在注册集群中安装组件前，您需要在接入集群中设置AK用来访问云服务的权限。设置AK前，您需要创建RAM用户并为其添加访问相关云资源的权限。

    1.  创建RAM用户。

        有关如何创建RAM用户的具体步骤，请参见[创建RAM用户](/intl.zh-CN/用户管理/基本操作/创建RAM用户.md)。

    2.  创建权限策略。

        有关创建权限策略的具体操作步骤，请参见[创建自定义策略](/intl.zh-CN/权限策略管理/自定义策略/创建自定义策略.md)。

        ack-virtual-node组件权限策略信息如下。

        ```
        {
            "Version": "1",
            "Statement": [
                {
                    "Action": [
                        "eci:CreateContainerGroup",
                        "eci:DeleteContainerGroup",
                        "eci:DescribeContainerGroups",
                        "eci:DescribeContainerLog",
                        "eci:UpdateContainerGroup",
                        "eci:UpdateContainerGroupByTemplate",
                        "eci:CreateContainerGroupFromTemplate",
                        "eci:RestartContainerGroup",
                        "eci:ExportContainerGroupTemplate",
                        "eci:DescribeContainerGroupMetric",
                        "eci:DescribeMultiContainerGroupMetric",
                        "eci:ExecContainerCommand",
                        "eci:CreateImageCache",
                        "eci:DescribeImageCaches",
                        "eci:DeleteImageCache"
                    ],
                    "Resource": [
                        "*"
                    ],
                    "Effect": "Allow"
                }
            ]
        }
        ```

    3.  为RAM用户添加权限。

        有关如何为RAM用户授权的具体步骤，请参见[为RAM用户授权](/intl.zh-CN/用户管理/授权管理/为RAM用户授权.md)。

    4.  为RAM用户创建AK。

        有关如何为子账户创建AK，请参见[获取AccessKey]()。

        创建ack-virtual-node组件使用的Secret的代码如下。

        ```
        kubectl create nskubectl -n kube-system create secret generic alibaba-addon-secret --from-literal='access-key-id=<your access key id>' --from-literal='access-key-secret=<your access key secret>'
        ```

        您需要将上述代码中`<your access key id>`和`<your access key secret>`替换为您获取的AK信息。

2.  安装ack-virtual-node组件。

    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

    2.  在集群列表页面，选择目标集群，并在目标集群右侧**操作**列下，选择**更多** \> **系统组件管理**。

    3.  找到**ack-virtual-node**组件，然后单击右侧的**安装**。

3.  创建ECI Pod。

    ack-virtual-node支持两种创建ECI Pod的方法：

    -   配置Pod的标签：给Pod添加eci=true的标签。

        ```
        kubectl run nginx --image nginx -l eci=true
        ```

    -   配置Namespace的标签：给Namespace添加eci=true的标签。

        ```
        kubectl create ns vk
        
        kubectl label namespace vk eci=true
        
        kubectl -n vk run nginx --image nginx
        ```


## 变更记录

**2021年05月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v2.0.0.122-bdb884460-aliyun|registry-vpc.$RegionId.aliyuncs.com/acs/virtual-nodes-eci:v2.0.0.122-bdb884460-aliyun|2021年05月24日|修复节点并发下重复创建的问题。|此次升级不会对业务造成影响。|
|v2.0.0.113-650b21c48-aliyun|registry-vpc.$RegionId.aliyuncs.com/acs/virtual-nodes-eci:v2.0.0.113-650b21c48-aliyun|2021年05月13日|支持PrivateZone热更新。|此次升级不会对业务造成影响。|
|v2.0.0.110-49c928287-aliyun|registry-vpc.$RegionId.aliyuncs.com/acs/virtual-nodes-eci:v2.0.0.110-49c928287-aliyun|2021年05月06日|兼容NodeSelector调度方式，即Pod的`type=virtual-kubelet`时会被VK接管创建。|此次升级不会对业务造成影响。|

**2021年04**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v2.0.0.102-045a06eb4-aliyun|registry-vpc.$RegionId.aliyuncs.com/acs/virtual-nodes-eci:v2.0.0.102-045a06eb4-aliyun|2021年04月22日|修复Virutal Kubelet节点显示问题。|此次升级不会对业务造成影响。|
|v2.0.0.101-cd8bcfa04-aliyun|registry-vpc.$RegionId.aliyuncs.com/acs/virtual-nodes-eci:v2.0.0.101-cd8bcfa04-aliyun|2021年04月16日|优化对PrivateZone的接口调用问题，避免重复调用BindZoneVpc。|此次升级不会对业务造成影响。|
|v2.0.0.93-ef86bbfa1-aliyun|registry-vpc.$RegionId.aliyuncs.com/acs/virtual-nodes-eci:v2.0.0.93-ef86bbfa1-aliyun|2021年04月13日|修改eciLogtailMacheineGroupKey的值，兼容ENV和CRD日志采集功能。|此次升级不会对业务造成影响。|
|v2.0.0.90-15deb126e-aliyun|registry-vpc.$RegionId.aliyuncs.com/acs/virtual-nodes-eci:v2.0.0.90-15deb126e-aliyun|2021年04月06日|新增ECI Profile功能。|此次升级不会对业务造成影响。|

**2021年03月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v2.0.0.86-9005a977d-aliyun|registry-vpc.$RegionId.aliyuncs.com/acs/virtual-nodes-eci:v2.0.0.86-9005a977d-aliyun|2021年03月17日|-   优化/etc/hosts，添加Domain和DNS注解。
-   缩短clusterrolebinding检查时间。
-   优化多组件选主机制。

|此次升级不会对业务造成影响。|

**2021年02月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v2.0.0.80-f9a46a994-aliyun|registry-vpc.$RegionId.aliyuncs.com/acs/virtual-nodes-eci:v2.0.0.80-f9a46a994-aliyun|2021年02月25日|优化CreatePod状态判断功能：只有为pending时才创建Pod。|此次升级不会对业务造成影响。|
|v2.0.0.76-6e9e19bd5-aliyun|registry-vpc.$RegionId.aliyuncs.com/acs/virtual-nodes-eci:v2.0.0.76-6e9e19bd5-aliyun|2021年02月22日|-   支持PProf调试。
-   支持选主开关控制。
-   为EndPointSlice更新ClusterRole。
-   在Pod中添加K8s版本注解。
-   不同K8s版本支持配置不同Webhook。

|此次升级不会对业务造成影响。|

**2020年12月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v2.0.0.618-7fd50d738-aliyun|registry-vpc.$RegionId.aliyuncs.com/acs/virtual-nodes-eci:v2.0.0.618-7fd50d738-aliyun|2020年12月04日|-   支持更好的Pod兼容性。
-   支持多个VK Controller选主逻辑。

|需要您确认ECI Pod的VPC和安全组配置正确，且可以访问集群APIserver，否则会导致Pod一直等待状态。|

**2020年09月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.0.0.10-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/virtual-nodes-eci:v1.0.0.10-aliyun|2020年09月21日|支持扩展集群弹性，应用Pod以Serverless容器（ECI）方式运行。|此次升级不会对业务造成影响。|

**2020年03月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.0.0.2-aliyun|registry-vpc.$RegionId.aliyuncs.com/acs/virtual-nodes-eci:v1.0.0.2-aliyun|2020年03月12日|-   使用StatefulSet部署virtual-nodes-eci controller，方便修改副本数量以创建多个VK虚拟节点，支持更大规模ECI Pod。
-   节点名字变更为virtual-node-eci-$n。
-   支持访问ClusterIP Service。
-   支持Spot可抢占实例类型。
-   支持CSI挂载Disk Volume。

|如果您的virtual-nodes-eci controller是以Deployment形式部署，请先删除virtual-kubelet节点上的ECI，再更新或者重新部署组件。|

