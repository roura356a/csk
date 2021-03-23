---
keyword: [挂载公网EIP, Terway模式, Pod独占的公网]
---

# 为Pod挂载独立公网EIP

Pod的IP地址一般是私网的IP地址，但是在ACK的Terway网络模式中，某些场景下的Pod需要独立的公网IP地址。本文介绍如何为Terway网络中的Pod挂载独立的公网EIP。

一般情况下，Terway网络模式中的Pod访问公网的流量是通过主机SNAT（Source Network Address Translation）或者外部SNAT通过EIP而实现，对于Pod的公网入口流量一般是通过LoadBalancer类型的Service流入。在有的场景中Pod需要独立的公网地址，例如：

-   Pod的对外映射端口是随机的，一般常见于UDP（User Datagram Protocol）的游戏服务器或电话会议等。例如RTSP协议，对不同的客户端使用不同的端口。
-   Pod访问公网流量存在争抢，Pod需要独立的公网出口。

关于ACK集群Terway网络模式的更多信息，请参见[使用Terway网络插件](/cn.zh-CN/Kubernetes集群用户指南/网络/容器网络CNI/使用Terway网络插件.md)。

## 步骤一：升级Terway组件至最新版本

您需升级Terway组件到支持EIP功能的版本。关于升级Terway组件的具体步骤，请参见[管理组件](/cn.zh-CN/Kubernetes集群用户指南/集群/升级集群/管理组件.md)。

**说明：** 目前Terway组件`v1.0.10.280-gdc2cb6c-aliyun`及以上版本支持EIP功能。建议您升级Terway组件至最新版本。

## 步骤二：为Terway部署EIP相关的配置和权限

Terway为Pod配置EIP需要EIP相关的权限去申请和配置EIP，所以需要部署额外的配置和权限。



1.  为Terway模式的RAM角色配置EIP相关权限。

    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

    2.  在控制台左侧导航栏中，单击**集群**。

    3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

    4.  单击**集群资源**页签，然后单击**Worker RAM角色**链接。

    5.  在**权限管理**页签，单击目标授权策略名称，进入授权策略详情页面。

        ![授权策略详情](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0106659951/p11193.png)

    6.  单击**修改策略内容**，从右侧滑出侧边栏修改策略内容面板，在**策略内容**的`Action`字段中补充以下策略，然后单击**确定**。

        ![修改策略内容](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8576260161/p11195.png)

        ```
        "vpc:DescribeVSwitches",
        "vpc:AllocateEipAddress",
        "vpc:DescribeEipAddresses",
        "vpc:AssociateEipAddress",
        "vpc:UnassociateEipAddress",
        "vpc:ReleaseEipAddress"
        ```

        **说明：** 在策略内容的任意一个`Action`字段的最后一行补充英文逗号（，）。

    您需要在RAM控制台中为这个角色添加以下EIP相关权限。

    ```
    "vpc:DescribeVSwitches",
    "vpc:AllocateEipAddress",
    "vpc:DescribeEipAddresses",
    "vpc:AssociateEipAddress",
    "vpc:UnassociateEipAddress",
    "vpc:ReleaseEipAddress"
    ```

    1.  在[RAM角色AliyunCSManagedNetworkRole](https://ram.console.aliyun.com/roles/AliyunCSManagedNetworkRole)页面的RAM角色列表中，单击目标授权策略名称右侧的**添加权限**。

    2.  在添加权限面板的**选择权限**区域，单击**+新建权限策略**创建自定义策略。

        关于如何创建自定义策略，请参见[创建自定义策略](/cn.zh-CN/权限策略管理/自定义策略/创建自定义策略.md)。

    3.  在您创建的自定义权限策略中添加以下权限。

        关于如何修改自定义策略内容，请参见[修改自定义策略内容](/cn.zh-CN/权限策略管理/自定义策略/修改自定义策略内容.md)。

        ```
        "vpc:DescribeVSwitches",
        "vpc:AllocateEipAddress",
        "vpc:DescribeEipAddresses",
        "vpc:AssociateEipAddress",
        "vpc:UnassociateEipAddress",
        "vpc:ReleaseEipAddress"
        ```

2.  部署Terway配置以支持EIP功能。

    1.  执行以下命令修改Terway的配置ConfigMap。

        ```
        kubectl edit cm eni-config -n kube-system
        ```

    2.  在eni\_conf中增加以下内容。

        ```
        "enable_eip_pool": "true"
        ```

        **说明：** 如果您希望在指定EIP的时候能够强制解绑之前的实例，还需要在eni\_conf中增加参数"allow\_eip\_rob": "true"。

    3.  执行以下命令重建Terway实例。

        ```
        kubectl delete pod -n kube-system -l app=terway-eniip
        ```


## 步骤三：通过注解为指定Pod创建和关联EIP

通过指定Pod中的annotations可以创建或者关联EIP到此Pod中。根据不同场景，配置注解方式如下：

-   **自动分配EIP场景**
    -   添加以下注解，为Pod创建并绑定一个EIP：

        ```
        k8s.aliyun.com/pod-with-eip: "true"
        ```

        示例YAML如下。

        ```
        apiVersion: apps/v1
        kind: Deployment
        metadata:
          name: nginx-deployment-basic
          labels:
            app: nginx
        spec:
          replicas: 2
          selector:
            matchLabels:
              app: nginx
          template:
            metadata:
              annotations:
                k8s.aliyun.com/pod-with-eip: "true" # 为Nginx容器自动分配公网EIP地址。
              labels:
                app: nginx
            spec:
              containers:
              - name: nginx
                image: nginx
        ```

    -   添加以下注解，指定EIP的带宽，默认带宽5 Mbps（与EIP的默认值保持一致）：

        ```
        k8s.aliyun.com/eip-bandwidth: 5
        ```

-   **指定EIP场景**

    添加以下注解，为Pod指定EIP实例ID：

    ```
    k8s.aliyun.com/pod-eip-instanceid: "<youreipInstanceId>"
    ```

    **说明：**

    -   由于单个EIP不支持关联多个Pod，所以不适用于Deployment和StatefulSet等场景。
    -   默认情况下如果EIP已经绑定了实例，则会创建EIP失败。如果希望解绑之前的实例再绑定新的实例，需要在上述[修改ConfigMap](#substep_8ko_vpt_wxa)中配置"allow\_eip\_rob": "true"。
    -   指定EIP实例ID的场景只能用于单个副本实例的情况，例如，Deployment不能超过1个Replicas。

## 验证配置

当Pod变成Running状态之后，可以观察部署完后的Pod中annotations的k8s.aliyun.com/allocated-eipAddress值来查看到它分配到的关联EIP地址，通过这个EIP即可访问到Pod。

**相关文档**  


[Kubernetes集群网络规划](/cn.zh-CN/Kubernetes集群用户指南/网络/Kubernetes集群网络规划.md)

[使用Terway网络插件](/cn.zh-CN/Kubernetes集群用户指南/网络/容器网络CNI/使用Terway网络插件.md)

