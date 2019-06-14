# 如何使用Terway网络插件 {#concept_bfw_jr5_vfb .concept}

本文介绍如何使用阿里云容器服务Kubernetes集群的Terway网络插件。

## Terway网络插件 {#section_yjj_ky1_wfb .section}

Terway网络插件是阿里云容器服务自研的网络插件，功能上完全兼容Flannel：

-   支持将阿里云的弹性网卡分配给容器。
-   支持基于Kubernetes标准的NetworkPolicy来定义容器间的访问策略，兼容Calico的Network Policy。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/64408/156047900932414_zh-CN.png)

在Terway网络插件中，每个Pod拥有自己网络栈和IP地址。同一台ECS内的Pod之间通信，直接通过机器内部的转发，跨ECS的Pod通信，报文通过VPC的vRouter转发。由于不需要使用VxLAN等的隧道技术封装报文，因此具有较高的通信性能。

## 使用Terway网络插件 {#section_fmw_h51_wfb .section}

1.  登录 [容器服务管理控制台](https://cs.console.aliyun.com)。
2.  在 Kubernetes 菜单下，单击左侧导航栏的**集群**，进入集群列表页面。
3.  单击页面右上角的创建**Kubernetes 集群**，进入创建 Kubernetes 集群页面。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/64408/156047900932371_zh-CN.png)

    默认进入**kubernetes**集群配置页面。

    **说明：** 本文以创建经典Dedicated Kubernetes模式为例，具体可参考[创建Kubernetes集群](intl.zh-CN/用户指南/Kubernetes集群/集群管理/创建Kubernetes集群.md#)。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/64408/156047900932372_zh-CN.png)

4.  设置启动用的网络插件，选择**Terway**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/64408/156047901032375_zh-CN.png)


## Flannel与Terway {#section_vct_xw1_wfb .section}

在创建Kubernetes集群时，阿里云容器服务提供两种网络插件：Terway和Flannel：

**说明：** 如何选择，可参考[如何选择Kubernetes集群网络插件：Terway和Flannel](../../../../intl.zh-CN/常见问题/如何选择Kubernetes集群网络插件：Terway和Flannel.md#)

-   Flannel：使用的是简单稳定的社区的[Flannel](https://github.com/coreos/flannel) CNI插件，配合阿里云的VPC的高速网络，能给集群高性能和稳定的容器网络体验，但功能偏简单，支持的特性少，例如：不支持基于Kubernetes标准的Network Policy。
-   Terway：是阿里云容器服务自研的网络插件，功能上完全兼容Flannel，支持将阿里云的弹性网卡分配给容器，支持基于Kubernetes标准的NetworkPolicy来定义容器间的访问策略，支持对单个容器做带宽的限流。对于不需要使用Network Policy的用户，可以选择Flannel，其他情况建议选择Terway。

    **说明：** 

    -   Terway的Network Policy是通过集成Calico的Felix组件实现的，因此Network Policy的能力和Calico完全一致。对于最初为了使用Calico而自建集群的客户，目前可以通过Terway转换到容器服务Kubernetes上来。

    -   Terway目前集成的Felix版本为2.6.6。


