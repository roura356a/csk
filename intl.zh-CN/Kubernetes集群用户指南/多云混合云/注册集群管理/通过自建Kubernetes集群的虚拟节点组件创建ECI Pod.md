---
keyword: [自建Kubernetes集群, 虚拟节点, 弹性负载, ack-virtual-node]
---

# 通过自建Kubernetes集群的虚拟节点组件创建ECI Pod

虚拟节点（Virtual Node）实现了Kubernetes与弹性容器实例ECI的无缝连接，让Kubernetes集群轻松获得极大的弹性能力，而不必受限于集群的节点计算容量。您可以灵活动态的按需创建ECI Pod，免去集群容量规划的麻烦。本文主要介绍虚拟节点和弹性容器实例ECI，以及通过ack-virtual-node组件如何创建ECI Pod。

-   已创建注册集群，并将自建Kubernetes集群（高于1.14版本）接入注册集群。具体操作，请参见[创建注册集群并接入本地数据中心集群](/intl.zh-CN/Kubernetes集群用户指南/多云混合云/注册集群管理/创建注册集群并接入本地数据中心集群.md)。
-   您需要开通弹性容器实例服务。登录[弹性容器实例控制台](https://eci.console.aliyun.com/)开通相应的服务。
-   您需要确认集群所在区域在ECI支持的地域列表内。登录[弹性容器实例控制台](https://eci.console.aliyun.com/)查看已经支持的地域和可用区。

## 虚拟节点和弹性容器实例ECI

[阿里云弹性容器实例ECI（Elastic Container Instance）](https://www.alibabacloud.com/zh/products/elastic-container-instance)是面向容器的无服务器弹性计算服务，提供免运维、强隔离、快速启动的容器运行环境。使用ECI无需购买和管理底层ECS服务器，让您更加关注在容器应用而非底层基础设施的维护工作。您可按需创建ECI，仅为容器配置的资源付费（按量按秒计费）。

虚拟节点Virtual Node非常适合运行在如下多个场景，帮助用户极大降低计算成本，提升计算弹性效率：

-   在线业务的波峰波谷弹性伸缩：如在线教育、电商等行业有着明显的波峰波谷计算特征。使用虚拟节点可以显著减少固定资源池的维护，降低计算成本。
-   数据计算：使用虚拟节点承载Spark、Presto等计算场景，有效降低计算成本。
-   CI/CD Pipeline：Jenkins、Gitlab-Runner。
-   Job任务：定时任务、AI。

阿里云容器服务基于虚拟节点和ECI提供了多种Serverless Container产品形态，包括Serverless Kubernetes（ASK）和ACK on ECI，充分支撑各种弹性和免节点运维场景的用户诉求。

![virtual node](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/7273551161/p85192.png)

## 步骤一：在注册集群中配置ack-virtual-node组件的RAM权限

若您在注册集群的**集群导入代理配置**区域使用私网接入IDC集群，则可以跳过此步骤，ack-virtual-node组件将托管到ACK集群。更多信息，请参见[将目标集群接入注册集群中](/intl.zh-CN/Kubernetes集群用户指南/多云混合云/注册集群管理/创建注册集群并接入本地数据中心集群.mdstep_dl9_6s6_2vx)。

在注册集群安装组件前，您需要在接入集群中设置AccessKey用来访问云服务的权限。设置AccessKey前，您需要创建RAM用户并为其添加访问相关云资源的权限。

1.  创建RAM用户。具体操作，请参见[创建RAM用户](/intl.zh-CN/用户管理/基本操作/创建RAM用户.md)。

2.  创建自定义权限策略。

    关于创建自定义权限策略的操作步骤，请参见[创建自定义策略](/intl.zh-CN/权限策略管理/自定义策略/创建自定义策略.md)。

    自定义策略模板如下所示：

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

3.  为RAM用户添加权限。具体操作，请参见[为RAM用户授权](/intl.zh-CN/用户管理/授权管理/为RAM用户授权.md)。

    可选择自定义权限策略或AliyunECIFullAccess策略为RAM用户授权。

4.  为RAM用户创建AccessKey。具体操作，请参见[获取AccessKey]()。

5.  使用AccessKey在注册集群中创建名为alibaba-addon-secret的Secret资源。

    安装ack-virtual-node组件时将自动引用此AccessKey访问对应的云服务资源。

    ```
    kubectl -n kube-system create secret generic alibaba-addon-secret --from-literal='access-key-id=<your access key id>' --from-literal='access-key-secret=<your access key secret>'
    ```

    **说明：** `<your access key id>`及`<your access key secret>`为上一步获取的AccessKey信息。


## 步骤二：在注册集群中部署ack-virtual-node组件

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**运维管理** \> **组件管理**。

5.  单击**其他**页签，在ack-virtual-node组件区域单击**安装**。

    在提示对话框单击**确定**。这时会以集群默认虚拟交换机和安全组作为ack-virtual-node的初始ECI配置参数。如果需要修改ECI配置参数，请参见[相关操作](#section_d8s_1d3_sui)。


## 步骤三：创建ECI Pod

您可以通过以下两种方法创建ECI Pod。

-   **方式一：配置Pod标签**。

    将Pod添加标签`alibabacloud.com/eci=true`，Pod将以ECI方式运行，其节点是虚拟节点，示例如下：

    1.  执行以下命令给Pod添加标签。

        ```
        kubectl run nginx --image nginx -l alibabacloud.com/eci=true
        ```

    2.  执行以下命令查看Pod。

        ```
        kubectl get pod -o wide|grep virtual-kubelet
        ```

        预期输出：

        ```
        nginx-7fc9f746b6-r4xgx     0/1     ContainerCreating   0          20s   192.168.XX.XX   virtual-kubelet        <none>           <none>
        ```

-   **方式二：配置命名空间标签**。

    将Pod所在的命名空间添加标签`alibabacloud.com/eci=true`，Pod将以ECI方式运行，其节点是虚拟节点，示例如下：

    1.  执行以下命令创建虚拟节点。

        ```
        kubectl create ns vk
        ```

    2.  执行以下命令将Pod所在的命名空间添加标签。

        ```
        kubectl label namespace vk alibabacloud.com/eci=true
        ```

    3.  执行以下命令将命名空间中的Pod调度到虚拟节点上。

        ```
        kubectl -n vk run nginx --image nginx
        ```

    4.  执行以下命令查看Pod。

        ```
        kubectl -n vk get pod -o wide|grep virtual-kubelet
        ```

        预期输出：

        ```
        nginx-6f489b847d-vgj4d      1/1     Running             0          1m   192.168.XX.XX   virtual-kubelet        <none>           <none>
        ```


## 相关操作

**修改虚拟节点配置**

虚拟节点Controller的配置决定了其调度ECI Pod的行为和ECI运行环境配置，包括虚拟交换机和安全组配置等。您可以根据需要灵活的修改Controller配置，修改配置后不会影响已经运行的ECI Pod，会立即生效于新建的ECI Pod。

执行以下命令修改虚拟节点Controller的配置。

```
kubectl -n kube-system edit deployment ack-virtual-node-controller
```

常用的变更操作如下：

-   **更新virtual-node controller版本**

    当您使用更新虚拟节点功能时，需要更新Virtual Node Controller镜像至最新版本。

-   **修改安全组配置ECI\_SECURITY\_GROUP**

    您可以修改此环境变量，改变ECI Pod的安全组。

-   **修改虚拟交换机配置ECI\_VSWITCH**

    您可以修改此环境变量，改变ECI Pod所在的虚拟交换机。建议配置多个虚拟交换机支持多可用区，当单可用区库存不足时，Controller会选择另外一个可用区创建ECI Pod。

-   **修改kube-proxy配置ECI\_KUBE\_PROXY**

    此环境变量默认值为true，表示ECI Pod默认可以访问集群中的ClusterIP Service。如果ECI Pod无需访问ClusterIP Service时，例如Job计算场景，您可以设置此环境变量为**false**关闭kube-proxy功能。另外在一些规模化场景，例如集群中需要启动大量ECI Pod时，ECI中的kube-proxy和Kubernetes Api Server之间的并发连接数也会大量增加，您同样可以选择关闭kube-proxy功能，减少对API Server的压力提升可扩展性。

-   **修改kube-system/eci-profile configmap**

    您可以修改此ConfigMap配置更多ECI相关参数，例如虚拟交换机及安全组等。


**删除虚拟节点**

1.  卸载ack-virtual-node组件。

    在注册集群中，删除所有ECI Pod后，在组件管理页面卸载ack-virtual-node组件即可。

2.  通过命令`kubectl delete no`删除相关虚拟节点。

    **说明：** 当集群中存在ECI Pod时，卸载ack-virtual-node组件会导致ECI实例的残留。


**相关文档**  


[注册集群概述](/intl.zh-CN/Kubernetes集群用户指南/多云混合云/注册集群管理/注册集群概述.md)

[通过部署ACK虚拟节点组件创建ECI Pod](/intl.zh-CN/Kubernetes集群用户指南/弹性容器实例ECI/通过部署ACK虚拟节点组件创建ECI Pod.md)

[基于ECI运行Job任务](/intl.zh-CN/Kubernetes集群用户指南/弹性容器实例ECI/基于ECI运行Job任务.md)

