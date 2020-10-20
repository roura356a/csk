---
keyword: [虚拟节点, Virtual Node Addon, 创建弹性容器实例ECI Pod]
---

# 部署虚拟节点Chart

本文主要介绍虚拟节点和ECI，以及如何通过Virtual Node Addon插件部署虚拟节点。

## 虚拟节点和弹性容器实例ECI

[阿里云弹性容器实例ECI（Elastic Container Instance）](https://cn.aliyun.com/product/eci)是面向容器的无服务器弹性计算服务，提供免运维、强隔离、快速启动的容器运行环境。使用ECI无需购买和管理底层ECS服务器，让用户更加关注在容器应用而非底层基础设施的维护工作。用户可按需创建ECI，仅为容器配置的资源付费（按量按秒计费）。

虚拟节点Virtual Node来源于Kubernetes社区的Virtual Kubelet技术，其实现了Kubernetes与弹性容器实例ECI的无缝连接，让Kubernetes集群轻松获得极大的弹性能力，而不必受限于集群的节点计算容量。关于Virtual Kubelet的工作原理及其架构，请参见[Virtual Kubelet](https://virtual-kubelet.io/docs/architecture/)。

因为Virtual Node可以轻松支持更高弹性和Pod容量，灵活动态的按需创建ECI Pod，免去集群容量规划的麻烦，所以它非常适合运行在如下多个场景，帮助用户极大降低计算成本，提升计算弹性效率。

-   在线业务的波峰波谷弹性伸缩：如在线教育、电商等行业有着明显的波峰波谷计算特征。使用虚拟节点可以显著减少固定资源池的维护，降低计算成本。
-   数据计算：使用虚拟节点承载Spark、Presto等计算场景，有效降低计算成本。
-   CI/CD Pipeline：Jenkins、Gitlab-Runner。
-   Job任务：定时任务、AI。

阿里云容器服务基于虚拟节点和ECI提供了多种Serverless Container产品形态，包括Serverless Kubernetes（ASK）和ACK on ECI，充分支撑各种弹性和免节点运维场景的用户诉求。

![virtual node](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/4106659951/p85192.png)

## 在ACK集群中部署虚拟节点Addon

**说明：** 在Serverless Kubernetes集群中我们无需手动部署虚拟节点Addon，用户可以直接创建ECI Pod。托管版或专有版则需要先部署ack-virtual-node addon后才可以创建ECI Pod。

在容器服务Kubernetes版（ACK）集群中部署虚拟节点Addon前：

-   您需要创建一个Kubernetes托管版或者专有版集群。详情请参见[创建Kubernetes托管版集群](/cn.zh-CN/Kubernetes集群用户指南/集群管理/创建集群/创建Kubernetes托管版集群.md)。
-   您需要开通弹性容器实例服务。登录[弹性容器实例控制台](https://eci.console.aliyun.com/)开通相应的服务。
-   您需要确认集群所在区域在ECI支持的地域列表内。登录[弹性容器实例控制台](https://eci.console.aliyun.com/)查看已经支持的地域和可用区。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，选择**市场** \> **应用目录**。

3.  在**应用目录**页面单击**阿里云应用**页签，然后选中**ack-virtual-node**应用。

    **阿里云应用**包含较多应用，您可在页面右上角搜索**ack-virtual-node**，支持关键字搜索。

4.  在应用目录-ack-virtual-node页面右侧的创建页面，选择目标集群。

    **命名空间**已设定为kube-system，**发布名称**已设定为ack-virtual-node。

5.  在应用目录-ack-virtual-node页面，单击**参数**页签，配置虚拟节点参数，然后单击右侧创建页面的**创建**。

    ![创建插件](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/4106659951/p47256.png)

    |参数|参数含义|获取路径|
    |--|----|----|
    |ALIYUN\_CLUSTERID|集群ID|您可以在集群基本信息的**基本信息**区域中，获取**集群ID**的值。|
    |ALIYUN\_RESOURCEGROUP\_ID|资源组ID|如果不配置则为默认资源组。如果需要配置，请登录[资源管理控制台](https://resourcemanager.console.aliyun.com/)，获取**资源组ID**的值。|
    |ECI\_REGION|地域名称|您可以在集群基本信息的**基本信息**区域中，获取**地域**的值。**说明：** 例如，华东1：cn-hangzhou |
    |ECI\_VSWITCH|虚拟交换机|您可以在节点列表单击某个节点，在**实例详情**页签的**配置信息**区域中，获取虚拟交换机的值。 **说明：**

请确认当前交换机在ECI支持的可用区列表中。

虚拟交换机支持多可用区。因此，这里可以填写多个vSwitch，例如`ECI_VSWITCH: "vsw-xxxxxxx1, vsw-xxxxxxx2, vsw-xxxxxxx3"`。 |
    |ECI\_SECURITY\_GROUP|安全组ID|您可以在节点列表单击某个节点，在**本实例安全组**页签的**安全组列表**区域中，获取安全组ID的值。|
    |ECI\_ACCESS\_KEY|用户AccessKey ID|请参见[如何获取AccessKey]()。请授权RAM的**AliyunECIReadOnlyAccess**策略，详情请参见[子账号鉴权]()。 |
    |ECI\_SECRET\_KEY|用户AccessKey secret|请参见[如何获取AccessKey]()。请授权RAM的**AliyunECIReadOnlyAccess**策略，详情请参见[子账号鉴权]()。 |

6.  安装完成后，您可查看新建的虚拟节点**virtual-node-eci**。

    1.  在控制台左侧导航栏中，单击**集群**。

    2.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**应用管理**。

    3.  在集群管理页左侧导航栏中，单击**节点管理**，可查看虚拟节点**virtual-node-eci**。

        ![添加节点](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/4106659951/p146959.png)

7.  执行以下命令查看**virtual-node-controller**和**virtual-node-admission-controller**部署状态。详情请参见[在CloudShell上通过kubectl管理Kubernetes集群](/cn.zh-CN/Kubernetes集群用户指南/集群管理/管理与访问集群/在CloudShell上通过kubectl管理Kubernetes集群.md)。

    执行命令：

    ```
    kubectl -n kube-system get statefulset virtual-node-eci
    ```

    返回结果如下：

    ```
    NAME              READY   AGE
    virtual-node-eci  1/1     1m
    ```

    执以命令：

    ```
    kubectl -n kube-system get deploy ack-virtual-node-affinity-admission-controller
    ```

    返回结果如下：

    ```
    NAME                                             READY   UP-TO-DATE   AVAILABLE   AGE
    ack-virtual-node-affinity-admission-controller   1/1     1            1           1m
    ```

    执行命令：

    ```
    kubectl -n kube-system get pod|grep virtual-node-eci
    ```

    返回结果如下：

    ```
    virtual-node-eci-0                                                1/1     Running   0          1m
    ```

    执行命令：

    ```
    kubectl get no|grep virtual-node-eci
    ```

    返回结果如下：

    ```
    virtual-node-eci-0        Ready    agent    1m   v1.11.2-aliyun-1.0.207
    ```


## 调度Pod到虚拟节点

**说明：** 此操作不适用于Serverless Kubernetes集群。

当集群中存在虚拟节点时，您可以把Pod调度到虚拟节点上，Virtual Node Controller将会创建出相应的ECI Pod。您可以通过以下三种方法操作。

-   **配置Pod的nodeSelector和tolerations**。

    虚拟节点有特殊的Taints，Pod需要配置nodeSelector和tolerations后才能指定调度到虚拟节点上。示例如下：

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: nginx
    spec:
      containers:
      - image: nginx
        imagePullPolicy: Always
        name: nginx
      nodeSelector:
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Exists
    ```

-   **配置Pod标签**。

    基于**virtual-node-affinity-admission-controller**的webhook，对于有特定label（eci=true）的Pod，webhook会将其自动调度到虚拟节点上。示例如下：

    执行命令：

    ```
    kubectl run nginx --image nginx -l eci=true
    ```

    ```
    kubectl get pod -o wide|grep virtual-node-eci
    ```

    返回结果如下：

    ```
    nginx-7fc9f746b6-r4xgx     0/1     ContainerCreating   0          20s   192.168.*.*   virtual-node-eci-0        <none>           <none>
    ```

-   **配置Namespace标签**。

    基于**virtual-node-affinity-admission-controller**的webhook，对于有特定label的namespace（virtual-node-affinity-injection=enabled）中创建的Pod，webhook会将其自动调度到虚拟节点上。示例如下：

    执行命令：

    ```
    kubectl create ns vk
    ```

    ```
    kubectl label namespace vk virtual-node-affinity-injection=enabled
    ```

    ```
    kubectl -n vk run nginx --image nginx
    ```

    ```
    kubectl -n vk get pod -o wide|grep virtual-node-eci
    ```

    返回结果如下：

    ```
    nginx-6f489b847d-vgj4d      1/1     Running             0          1m   192.168.*.*   virtual-node-eci-0        <none>           <none>
    ```


## 修改虚拟节点Controller的配置

**说明：** 此操作不适用于Serverless Kubernetes集群。

虚拟节点Controller的配置决定了其调度ECI Pod的行为和ECI运行环境配置，包括vswitch和安全组配置等。我们可以根据需要灵活的修改Controller配置，修改配置后不会影响已经运行的ECI Pod，会立即生效于新建ECI Pod。

修改虚拟节点Controller配置的方法如下。

```
`kubectl -n kube-system edit statefulset virtual-node-eci`
```

常用的变更操作如下。

-   **更新virtual-node controller版本。**

    当需要使用更新虚拟节点功能时，需要更新virtual-node controller镜像至最新版本。例如支持ECI Pod clusterIP访问的vk镜像版本需要高于v1.0.0.2-aliyun。

-   **修改安全组配置ECI\_SECURITY\_GROUP。**

    用户可以修改此环境变量，改变ECI Pod的安全组。

-   **修改vswitch配置ECI\_VSWITCH。**

    用户可以修改此环境变量，改变ECI Pod所在的vswitch。我们建议用户配置多个vswitch支持多可用区，这样当单可用区库存不足时，Controller会选择另外一个可用区创建ECI Pod。

-   **修改kube-proxy配置ECI\_KUBE\_PROXY。**

    此环境变量默认值为**true**，表示ECI Pod默认可以访问集群中的ClusterIP Service。如果ECI Pod无需访问clusterIP service时，例如Job计算场景，用户可以设置此环境变量为**false**关闭kube-proxy功能。另外在一些规模化场景，例如集群中需要启动大量ECI Pod时，ECI中的kube-proxy和kubernetes apiserver之间的并发连接数也会大量增加，用户同样可以选择关闭kube-proxy功能，减少对apiserver的压力提升可扩展性，改用privatezone方式让ECI Pod访问集群中的service。

-   **创建多个虚拟节点。**

    我们推荐使用单个虚拟节点支撑3000个ECI Pod，当需要创建更多ECI Pod时，可以创建更多的虚拟节点，这样集群中能够支撑的ECI Pod数量也会相应成倍增长。方法是修改statefulset的副本数量，其数量代表集群中虚拟节点的数量，virtual-node-controller pod与虚拟节点一一对应，分别管理虚拟节点上的ECI Pod，controller之间互不影响。所示如下：

    执行命令：

    ```
    kubectl -n kube-system scale statefulset virtual-node-eci --replicas=4
    ```

    返回结果如下：

    ```
    statefulset.apps/virtual-node-eci scaled
    ```

    执行命令：

    ```
    kubectl get no
    ```

    返回结果如下：

    ```
    NAME                            STATUS     ROLES    AGE     VERSION
    cn-hangzhou.192.168.1.1         Ready      <none>   63d     v1.12.6-aliyun.1
    cn-hangzhou.192.168.1.2         Ready      <none>   63d     v1.12.6-aliyun.1
    virtual-node-eci-0              Ready      agent     6m     v1.11.2-aliyun-1.0.207
    virtual-node-eci-1              Ready      agent     1m     v1.11.2-aliyun-1.0.207
    virtual-node-eci-2              Ready      agent     1m     v1.11.2-aliyun-1.0.207
    virtual-node-eci-3              Ready      agent     1m     v1.11.2-aliyun-1.0.207
    ```


## 删除虚拟节点

**说明：** 此操作不适用于Serverless Kubernetes集群。

通常情况下我们无需删除虚拟节点，虚拟节点不同于真实节点，不会占用集群计算资源。如果用户需要删除虚拟节点，我们建议手动先驱逐虚拟节点上的Pod，或者删除所有ECI Pod，然后删除Controller和节点。在ECI Pod存在时删除virtual-node controller可能会导致ECI实例的残留。

执行命令，删除虚拟节点：

```
kubectl drain virtual-node-eci-0 ...
```

```
kubectl -n kube-system delete statefulset virtual-node-eci
```

```
kubectl delete no virtual-node-eci-0 ...
```

