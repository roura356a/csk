---
keyword: [升级cGPU, cGPU版本]
---

# 升级节点cGPU版本

ACK集群支持GPU共享调度时节点需要安装cGPU模块。本文介绍如何通过命令及控制台升级节点上的cGPU模块。

-   您已通过kubectl连接集群。具体操作，请参见[通过kubectl连接Kubernetes集群](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl连接Kubernetes集群.md)。
-   集群已安装ack-cgpu或ack-ai-installer组件。具体操作，请参见[安装ack-cgpu组件](/cn.zh-CN/Kubernetes集群用户指南/调度/组件安装及功能简介/ack-cgpu/安装共享GPU组件.md)或[安装ack-ai-installer组件](/cn.zh-CN/Kubernetes集群用户指南/调度/组件安装及功能简介/ack-ai-installer/安装ack-ai-installer组件.md)。
-   升级节点上无正在运行的业务。

## 通过命令升级节点cGPU版本

cgpu-installer的DaemonSet用于在节点上安装cGPU驱动模块。在升级cGPU时，需要将cgpu-installer的镜像版本修改为待升级的版本。

目前支持的cGPU镜像版本有：v0.8.10及v0.8.12。

**说明：** 升级节点cGPU版本过程中会重启节点，所以在升级节点cGPU版本之前，请确认节点上无正在运行的业务。

1.  执行以下命令修改cgpu-installer的镜像，升级节点的cGPU模块版本。

    ```
    kubectl  edit ds cgpu-installer -n kube-system
    ```

    修改镜像的版本，以其他版本升级到v0.8.10版本为例：

    ![镜像版本](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2750795161/p251051.png)

2.  卸载节点上旧版本cGPU。

    1.  登录节点。关于登录方式，请参见[通过密码认证登录Linux实例](/cn.zh-CN/实例/连接实例/使用VNC连接实例/通过密码认证登录Linux实例.md)。

    2.  执行以下命令停止Docker服务。

        ```
        systemctl stop docker
        ```

    3.  执行以下命令卸载cGPU。

        ```
        bash /usr/local/cgpu-installer/uninstall.sh
        ```

        **说明：**

        若/usr/local/cgpu-installer/uninstall.sh不存在，请执行以下命令卸载旧版本cGPU。

        ```
        wget http://aliacs-k8s-cn-beijing.oss-cn-beijing.aliyuncs.com/gpushare/cgpu-uninstall.sh -O /usr/local/cgpu-installer/uninstall.sh
        ```

3.  重启卸载旧版本cGPU的节点。具体操作，请参见[重启实例](/cn.zh-CN/实例/管理实例/重启实例.md)。


**结果验证**

当节点重启后，登录该节点，执行以下命令查询cGPU版本。

```
cat /proc/cgpu_km/version
```

预期输出：

```
0.8.10
```

从预期输出可以得出，节点的cGPU版本已经成功升级到v0.8.10版本。

## 通过控制台升级节点cGPU版本

cgpu-installer的DaemonSet用于在节点上安装cGPU驱动模块。在升级cGPU时，需要将cgpu-installer的镜像版本修改为待升级的版本。

目前支持的cGPU镜像版本有：v0.8.10及v0.8.12。

1.  执行以下命令修改cgpu-installer的镜像，升级节点的cGPU模块版本。

    ```
    kubectl  edit ds cgpu-installer -n kube-system
    ```

    修改镜像的版本，以其他版本升级到v0.8.10版本为例：

    ![镜像版本](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2750795161/p251051.png)

2.  移除待升级cGPU版本的节点。

    具体操作，请参见[移除节点](/cn.zh-CN/Kubernetes集群用户指南/节点与节点池/节点/移除节点.md)。

    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

    2.  在控制台左侧导航栏中，单击**集群**。

    3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

    4.  在集群管理页左侧导航栏中，选择**节点管理** \> **节点**。

    5.  在节点页面选中待升级节点，单击**批量移除**。

    6.  在移除节点对话框选中**自动排空节点（drain）**。

    7.  单击**确定**。

3.  创建节点池。

    创建一个新的节点池用于添加上面移除的安装有cGPU模块的节点。

    具体操作，请参见[创建节点池](/cn.zh-CN/Kubernetes集群用户指南/节点与节点池/节点池/创建节点池.md)。

    1.  在集群管理页左侧导航栏中，选择**节点管理** \> **节点池**。

    2.  在节点池页面右上角，单击**创建节点池**。

    3.  在创建节点池页面，设置创建节点池的配置项。

        有关配置项的详细说明，请参见[创建Kubernetes专有版集群](/cn.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes专有版集群.md)。部分配置项说明如下：

        |参数|描述|配置值|
        |--|--|---|
        |**数量**|设置节点池初始节点数量。|需要填写为0。|
        |**节点标签**|您可以为集群节点添加标签。|        -   若集群中安装了ack-ai-installer组件，则**键**中输入ack.node.gpu.schedule，**值**中输入cgpu。
        -   若集群中安装了ack-cgpu组件，则**键**中输入cgpu，**值**中输入true。 |

    4.  单击**确认配置**，完成节点池的创建。

4.  添加节点到创建的节点池。

    节点池创建完成后，需要将[步骤2](#step_c63_s6f_g0e)中移除的节点添加到[步骤3](#step_6nn_wov_c4r)创建的节点池。具体操作，请参见[添加已有节点](/cn.zh-CN/Kubernetes集群用户指南/节点与节点池/节点/添加已有节点.md)。


**结果验证**

当节点添加完成以后，验证节点的cGPU版本是否已升级。

1.  执行以下命令查询新添加节点所对应的cgpu-installer。

    ```
    kubectl get po -l name=cgpu-installer -n kube-system -o wide
    ```

    预期输出：

    ```
    NAME                   READY   STATUS    RESTARTS   AGE    IP                NODE                     NOMINATED NODE   READINESS GATES
    cgpu-installer-kkmp6   1/1     Running   0          4d2h   192.168.XXX.XX1   cn-beijing.192.168.XXX.XX1   <none>           <none>
    cgpu-installer-**2     1/1     Running   0          4d2h   192.168.XXX.XX2   cn-beijing.192.168.XXX.XX2   <none>           <none>
    cgpu-installer-**3     1/1     Running   0          4d2h   192.168.XXX.XX3   cn-beijing.192.168.XXX.XX3   <none>           <none>
    ```

2.  执行以下命令进入`cgpu-installer-kkmp6`的Pod。

    ```
    kubectl exec -ti cgpu-installer-kkmp6 -n kube-system -- bash
    ```

3.  执行以下命令查询cGPU当前版本。

    ```
    nsenter -t 1 -i -p -n -u -m -- cat /proc/cgpu_km/version
    ```

    预期输出：

    ```
    0.8.10
    ```

    从预期输出可以得出节点的cGPU版本已经为v0.8.10。


