# 容器网络FAQ

本文为您介绍容器网络中的常见问题。

-   [手动升级了Flannel镜像版本后，如何解决无法兼容1.16以上版本集群的问题？](#section_z0i_qcj_rz7)
-   [如何解决Pod启动后存在时延的问题？](#section_g60_wqh_qtq)
-   [如何让Pod访问自己暴露的服务？](#section_w3o_3as_8lq)
-   [如何选择Kubernetes集群Terway和Flannel网络插件？](#section_ylr_ln7_pfe)
-   [如何规划集群网络？](#section_8td_x8b_qez)
-   [ACK是否支持hostPort的端口映射？](#section_hlo_gm1_u0l)
-   [ACK集群是否支持VPC多路由表？](#section_9lf_6ud_9zg)
-   [如何查看集群的网络类型？](#section_bgi_kgm_etw)
-   [如何查看集群中使用的云资源？](#section_8b1_slo_xiu)

## 手动升级了Flannel镜像版本后，如何解决无法兼容1.16以上版本集群的问题？

问题现象：

集群版本升级到1.16之后，集群节点变成NotReady。

问题原因：

手动升级了Flannel版本，而并没有升级Flannel的配置，导致Kubelet无法识别。

解决方法：

1.  编辑Flannel，增加`cniVersion`字段。

    ```
    kubectl edit cm kube-flannel-cfg -n kube-system 
    ```

    返回结果中增加`cniVersion`字段。

    ```
    "name": "cb0",      
    "cniVersion":"0.3.0",
    "type": "flannel",
    ```

2.  重启Flannel。

    ```
    kubectl delete pod -n kube-system -l app=flannel
    ```


## 如何解决Pod启动后存在时延的问题？

问题现象：

Pod启动后网络需要延迟一会才能通信。

问题原因：

配置Network Policy会有一定的时延，关闭Network Policy后，就能解决该问题。

解决方法：

1.  修改Terway的ConfigMap，增加禁用NetworkPolicy的配置。

    ```
    kubectl edit cm -n kube-system eni-config 
    ```

    在返回结果中增加以下字段。

    ```
    disable_network_policy: "true"
    ```

2.  如果Terway版本不是最新的，请在控制台升级Terway版本。

    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

    2.  在控制台左侧导航栏中，单击**集群**。

    3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

    4.  在集群管理页面选择**运维管理** \> **组件管理**。

    5.  在组件管理页面单击**网络**页签，单击目标Terway组件区域的**升级**。

    6.  在**提示**对话框中单击**确认**。

3.  重启所有Terway的Pod。

    ```
     kubectl delete pod -n kube-system -l app=terway-eniip
    ```


## 如何让Pod访问自己暴露的服务？

问题现象：

Pod无法访问自己暴露的服务，存在时好时坏或者调度到自己就出问题的现象。

问题原因：

Flannel目前默认不允许回环访问。

解决方法：

-   使用Headless Service暴露服务和访问，具体操作，请参见[Headless Services](https://kubernetes.io/zh/docs/concepts/services-networking/service/#headless-services)。

    **说明：** 推荐使用此方法。

-   重建集群使用Terway的网络插件，具体操作，请参见[使用Terway网络插件](/cn.zh-CN/Kubernetes集群用户指南/网络/容器网络CNI/使用Terway网络插件.md)。
-   修改Flannel的配置，然后重建Flannel和Pod。

    **说明：** 不推荐此方法，可能会被后续升级覆盖。

    1.  在cni-config.json中增加`hairpinMode: true`。

        ```
        kubectl edit cm kube-flannel-cfg -n kube-system
        ```

        返回结果中增加以下字段。

        ```
        hairpinMode: true
        ```

    2.  重启Flannel。

        ```
        kubectl delete pod -n kube-system -l app=flannel   
        ```

    3.  删除并重新创建Pod。

## 如何选择Kubernetes集群Terway和Flannel网络插件？

下面为您详细介绍在ACK创建集群时使用的两种网络插件：Terway和Flannel。

在创建Kubernetes集群时，阿里云容器服务提供以下两种网络插件：

-   Flannel：使用的是简单稳定的社区的[Flannel](https://github.com/coreos/flannel) CNI插件，配合阿里云的VPC的高速网络，能给集群高性能和稳定的容器网络体验，但功能偏简单，支持的特性少，例如：不支持基于Kubernetes标准的Network Policy。
-   Terway：是阿里云容器服务自研的网络插件，功能上完全兼容Flannel，支持将阿里云的弹性网卡分配给容器，支持基于Kubernetes标准的NetworkPolicy来定义容器间的访问策略，支持对单个容器做带宽的限流。对于不需要使用Network Policy的用户，可以选择Flannel，其他情况建议选择Terway。了解更多Terway网络插件的相关内容，请参见[使用Terway网络插件](/cn.zh-CN/Kubernetes集群用户指南/网络/容器网络CNI/使用Terway网络插件.md)。

## 如何规划集群网络？

在创建ACK集群时，需要指定专有网络VPC、虚拟交换机、 Pod网络CIDR（地址段）和Service CIDR（地址段）。建议您提前规划ECS地址、Kubernetes Pod地址和Service地址。详情请参见[Kubernetes集群网络规划](/cn.zh-CN/Kubernetes集群用户指南/网络/Kubernetes集群网络规划.md)。

## ACK是否支持hostPort的端口映射？

-   不支持hostPort。
-   容器服务ACK的Pod地址是可以直接被VPC中其他资源访问的，不需要额外的端口映射。
-   如果需要把服务暴露到外部，可以使用NodePort或者LoadBalancer类型的Service。

## ACK集群是否支持VPC多路由表？

目前只有专有版集群支持配置VPC多路由表。具体操作，请参见[使用VPC的多路由表功能](/cn.zh-CN/Kubernetes集群用户指南/网络/容器网络CNI/使用VPC的多路由表功能.md)。如果需ACK托管版集群支持使用VPC的多路由表功能，请[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex)。

## 如何查看集群的网络类型？

ACK支持两种容器网络类型，分别是Flannel网络类型和Terway网络类型。通过以下步骤查看您创建集群时所选择的网络类型。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
2.  在控制台左侧导航栏中，单击**集群**。
3.  在集群列表页面中，单击目标集群名称或者目标集群右侧操作列下的**详情**。
4.  单击**基本信息**页签，在**集群信息**区域查看集群的容器网络类型，即**网络插件**右侧的值。
    -   如果**网络插件**右侧显示**terway-eniip**，则容器的网络类型为Terway网络。
    -   如果**网络插件**右侧显示**Flannel**，则容器的网络类型为Flannel网络。

## 如何查看集群中使用的云资源？

通过以下步骤查看集群中使用的云资源信息，包括虚拟机、虚拟专有网络VPC、Worker RAM角色等。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
2.  在控制台左侧导航栏中，单击**集群**。
3.  在集群列表页面中，单击目标集群名称或者目标集群右侧操作列下的**详情**。
4.  单击**集群资源**页签，查看集群中使用的云资源信息。
    -   如果您集群网络类型为Terway网络，**集群资源**页签中同时显示**节点虚拟交换机**和**Pod虚拟交换机**信息。
    -   如果您集群网络类型为Flannel网络，**集群资源**页签中显示**节点虚拟交换机**信息。

