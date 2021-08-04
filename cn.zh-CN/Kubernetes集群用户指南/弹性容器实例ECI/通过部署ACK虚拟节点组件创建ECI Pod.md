---
keyword: [虚拟节点, ack-virtual-node组件, 创建弹性容器实例ECI Pod]
---

# 通过部署ACK虚拟节点组件创建ECI Pod

虚拟节点（Virtual Node）实现了Kubernetes与弹性容器实例ECI的无缝连接，让Kubernetes集群轻松获得极大的弹性能力，而不必受限于集群的节点计算容量。您可以灵活动态地按需创建ECI Pod，免去集群容量规划的麻烦。本文主要介绍虚拟节点和ECI，通过ack-virtual-node组件如何部署虚拟节点及如何创建ECI Pod。

-   您需要创建一个Kubernetes托管版或者专有版集群（高于1.14版本）。具体操作，请参见[创建Kubernetes托管版集群](/cn.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)或[创建Kubernetes专有版集群](/cn.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes专有版集群.md)。
-   您需要开通弹性容器实例服务。登录[弹性容器实例控制台](https://eci.console.aliyun.com/)开通相应的服务。
-   您需要确认集群所在区域在ECI支持的地域列表内。登录[弹性容器实例控制台](https://eci.console.aliyun.com/)查看已经支持的地域和可用区。

## 虚拟节点和弹性容器实例ECI

[阿里云弹性容器实例ECI（Elastic Container Instance）](https://cn.aliyun.com/product/eci)是面向容器的无服务器弹性计算服务，提供免运维、强隔离、快速启动的容器运行环境。使用ECI无需购买和管理底层ECS服务器，让您更加关注在容器应用而非底层基础设施的维护工作。您可按需创建ECI，仅为容器配置的资源付费（按量按秒计费）。

虚拟节点Virtual Node非常适合运行在如下多个场景，帮助用户极大降低计算成本，提升计算弹性效率：

-   在线业务的波峰波谷弹性伸缩：如在线教育、电商等行业有着明显的波峰波谷计算特征。使用虚拟节点可以显著减少固定资源池的维护，降低计算成本。
-   数据计算：使用虚拟节点承载Spark、Presto等计算场景，有效降低计算成本。
-   CI/CD Pipeline：Jenkins、Gitlab-Runner。
-   Job任务：定时任务、AI。

阿里云容器服务基于虚拟节点和ECI提供了多种Serverless Container产品形态，包括Serverless Kubernetes（ASK）和ACK on ECI，充分支撑各种弹性和免节点运维场景的用户诉求。

![virtual node](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/7273551161/p85192.png)

## 步骤一：在ACK集群中部署ack-virtual-node组件

**说明：**

-   在Serverless Kubernetes集群中您无需手动部署ack-virtual-node组件，Virtual Node Controller默认被托管，您可以直接创建ECI Pod。托管版或专有版则需要先部署ack-virtual-node组件后才可以创建ECI Pod。
-   在Kubernetes版（ACK）托管版集群中通过**组件管理**页面部署ack-virtual-node组件，Virtual Node Controller默认被托管。
-   在Kubernetes版（ACK）专有版集群中通过**应用目录**页面部署ack-virtual-node组件，Virtual Node Controller安装在集群节点上。

**ACK托管版集群**

在ACK托管版集群中部署ack-virtual-node组件的操作步骤如下：

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**运维管理** \> **组件管理**。

5.  在**组件管理**页面，选择**ack-virtual-node**组件，然后单击**安装**。

    这时会以集群默认虚拟交换机和安全组作为ack-virtual-node的初始ECI配置参数。如果需要修改ECI配置参数，请参见[相关操作](#section_9ec_223_45i)。

    ![组件管理ack-virtual-node组件](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/2220361161/p232042.png)


**ACK专有版集群**

在ACK专有版集群中部署ack-virtual-node组件的操作步骤如下：

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，选择**市场** \> **应用目录**。

3.  在**应用目录**页面单击**阿里云应用**页签，然后选中**ack-virtual-node**应用。

    **阿里云应用**包含较多应用，您可在**应用目录**页面左上角的**名称**文本框中搜索**ack-virtual-node**，支持关键字搜索。

4.  在应用目录-ack-virtual-node页面右侧的创建页面，选择目标集群。

    **命名空间**已设定为kube-system，**发布名称**已设定为ack-virtual-node。

5.  在应用目录-ack-virtual-node页面，单击**参数**页签，配置虚拟节点参数。

    |参数|描述|获取路径|
    |--|--|----|
    |ALIYUN\_CLUSTERID|集群ID|您可以在集群基本信息的**基本信息**区域中，获取**集群ID**的值。|
    |ALIYUN\_RESOURCEGROUP\_ID|资源组ID|如果不配置则为默认资源组。如果需要配置，请登录[资源管理控制台](https://resourcemanager.console.aliyun.com/)，获取**资源组ID**的值。|
    |ECI\_REGION|地域名称|您可以在集群基本信息的**基本信息**区域中，获取**地域**的值。**说明：** 例如，华东1：cn-hangzhou |
    |ECI\_VSWITCH|虚拟交换机|您可以在节点列表单击某个节点，在**实例详情**页签的**配置信息**区域中，获取虚拟交换机的值。 **说明：**

请确认当前交换机在ECI支持的可用区列表中。

虚拟交换机支持多可用区。因此，这里可以填写多个vSwitch，例如`ECI_VSWITCH: "vsw-xxxxxxx1, vsw-xxxxxxx2, vsw-xxxxxxx3"`。 |
    |ECI\_SECURITY\_GROUP|安全组ID|您可以在节点列表单击某个节点，在**本实例安全组**页签的**安全组列表**区域中，获取安全组ID的值。|
    |ECI\_ACCESS\_KEY|用户AccessKey ID|请参见[获取AccessKey]()。请授权RAM的**AliyunECIFullAccess**策略。更多信息，请参见[子账号鉴权]()。 |
    |ECI\_SECRET\_KEY|用户AccessKey Secret|请参见[获取AccessKey]()。请授权RAM的**AliyunECIFullAccess**策略。更多信息，请参见[子账号鉴权]()。 |

6.  在应用目录-ack-virtual-node页面右侧的创建页面，选择目标集群。

    **命名空间**已设定为kube-system，**发布名称**已设定为ack-virtual-node，然后单击**创建**。

    ![创建插件](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/4106659951/p47256.png)

7.  查看**virtual-node-controller**部署状态。

    更多信息，请参见[在CloudShell上通过kubectl管理Kubernetes集群](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/在CloudShell上通过kubectl管理Kubernetes集群.md)。

    执行以下命令查看**virtual-node-controller**部署状态。

    ```
    kubectl -n kube-system get deploy ack-virtual-node-controller
    ```

    预期输出：

    ```
    NAME                     READY   AGE
    virtual-node-controller  1/1     1m
    ```


## 步骤二：创建ECI Pod

**说明：** 在Serverless Kubernetes集群中所有Pod都是ECI Pod，无需给Pod配置特殊标签，因此以下操作不适用于Serverless Kubernetes集群。此操作以Kubernetes版（ACK）集群为例创建ECI Pod。

您可以通过以下两种方法创建ECI Pod。

-   **配置Pod标签**。

    给Pod添加标签`alibabacloud.com/eci=true`，Pod将以ECI方式运行，其节点是虚拟节点，示例如下：

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

-   **配置命名空间标签**。

    给Pod所在的命名空间添加标签`alibabacloud.com/eci=true`，Pod将以ECI方式运行，其节点是虚拟节点，示例如下：

    1.  执行以下命令创建虚拟节点。

        ```
        kubectl create ns vk
        ```

    2.  执行以下命令给Pod所在的命名空间添加标签。

        ```
        kubectl label namespace vk alibabacloud.com/eci=true
        ```

    3.  执行以下命令让命名空间中的Pod调度到虚拟节点上。

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

**说明：** 此操作只针对ACK集群，不适用于Serverless Kubernetes集群。

**修改ACK虚拟节点配置**

关于修改ACK虚拟节点配置的操作，请参见[配置ECI Profile](/cn.zh-CN/Kubernetes集群用户指南/弹性容器实例ECI/配置ECI Profile.md)。

**删除ACK虚拟节点**

1.  卸载ack-virtual-node组件。

-   在ACK托管版集群中，删除所有ECI Pod后，在组件管理页面卸载ack-virtual-node组件即可。

-   在ACK专有版集群中，删除所有ECI Pod后，在Helm管理页面删除ack-virtual-node组件即可。

2.  通过命令`kubectl delete node <node name>`删除相关虚拟节点。

    **说明：** 当集群中存在ECI Pod时，卸载ack-virtual-node组件会导致ECI实例的残留。


**相关文档**  


[基于ECI运行Job任务](/cn.zh-CN/Kubernetes集群用户指南/弹性容器实例ECI/基于ECI运行Job任务.md)

