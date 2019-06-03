# 创建Kubernetes NodePort服务 {#concept_371722 .concept}

本文主要介绍在Kubernetes-piggymetrics-cluster集群中，创建一个NodePort类型的Service，用于后续从Swarm集群的SLB 上引入生产流量。

## 前提条件 {#section_mp0_oub_iep .section}

您已经创建好一个Kubernetes托管版集群。请参见[创建Kubernetes 托管版集群](../../../../intl.zh-CN/用户指南/Kubernetes集群/集群管理/创建Kubernetes 托管版集群.md#)。

## 操作步骤 {#section_ftk_dcy_ygu .section}

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)，在左侧导航栏选择**路由与负载均衡** \> **服务**，在目标集群Kubernetes-piggymetrics-cluster右侧单击**创建**。
2.  在创建服务页面，填写对应的内容，并单击**创建**。请参考[创建服务](../../../../intl.zh-CN/用户指南/Kubernetes集群/应用管理/创建服务.md#)。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/301856/155954564148034_zh-CN.png)

    **说明：** 

    -   名称：输入服务的名称，本例中为 gateway-swarm-slb。
    -   类型：选择服务类型，即服务访问方式，请选择节点端口类型。

        节点端口：即 NodePort，通过每个 Node 上的 IP 和静态端口（NodePort）暴露服务。NodePort 服务会路由到 ClusterIP 服务，这个 ClusterIP 服务会自动创建。通过请求`<NodeIP>:<NodePort>`，可以从集群的外部访问一个 NodePort 服务。

    -   关联部署：选择服务要绑定的后端对象，本例中是前面创建的 gateway服务。
    -   端口映射：添加服务端口和容器端口，容器端口需要与后端的 pod 中暴露的容器端口一致。

        节点端口范围为：30000~32767。


