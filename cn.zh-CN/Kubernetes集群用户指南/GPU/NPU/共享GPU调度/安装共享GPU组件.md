---
keyword: [cGPU组件, 安装GPU隔离模块, kubectl-inspect-cgpu]
---

# 安装共享GPU组件

目前阿里云ACK提供了GPU共享调度能力，服务于使用共享单GPU的模型预测场景，同时也可以通过Nvidia驱动在内核态的控制保障GPU显存的隔离。本文介绍如何在GPU节点上安装GPU隔离模块及安装GPU分配查询工具，实现GPU的调度和隔离能力。

-   [创建GPU集群](/cn.zh-CN/Kubernetes集群用户指南/GPU/NPU/GPU资源调度/Kubernetes GPU集群支持GPU调度.md)

**说明：** 支持专有版GPU集群和ACK Pro版集群，不支持托管版GPU集群。

-   [通过kubectl连接Kubernetes集群](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl连接Kubernetes集群.md)

## 使用须知

|配置|支持版本|
|--|----|
|Kubernetes|1.16.06|
|Helm版本|3.0及以上版本|
|Nvidia驱动版本|418.87.01及以上版本|
|Docker版本|19.03.5|
|操作系统|CentOS 7.6、CentOS 7.7、Ubuntu 16.04和Ubuntu 18.04|
|支持显卡|Telsa P4、Telsa P100、 Telsa T4和Telsa v100（16 GB）|

## 步骤一：为带有GPU设备的节点打标签

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，选择**集群** \> **节点**。

3.  在节点列表页面，选择目标集群并单击页面右上角**标签管理**。

4.  在控制台左侧导航栏中，单击**集群**。

5.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

6.  在**节点管理**页面，单击页面右上角**标签与污点管理**。

7.  在**标签与污点管理**页面，批量选择节点，然后单击**添加标签**。

    ![添加标签](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2875659951/p101294.png)

8.  在弹出的添加对话框中，填写标签**名称**和**值**。

    **说明：**

    -   您可以设置**名称**为**cgpu**，**值**为**true**，使共享生效。
    -   删除cgpu标签并不能关闭共享，您可以设置**名称**为**cgpu**，**值**为**false**，关闭共享。
    ![添加标签](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4875659951/p101645.png)

9.  单击**确定**。


## 步骤二：为添加标签的节点安装共享GPU组件

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，选择**市场** \> **应用目录**。

3.  在应用目录页面，选中并单击**ack-cgpu**。

4.  在应用目录-ack-cgpu页面右侧的**创建**面板中，选中目标集群，然后单击**创建**。

    您无需设置**命名空间**和**发布名称**，系统显示默认值。

    ![cgpu](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2875659951/p101994.png)

    您可以执行命令`helm get manifest cgpu -n kube-system | kubectl get -f -`查看cGPU组件是否安装成功。当出现以下命令详情时，说明cGPU组件安装成功。

    ```
    helm get manifest cgpu -n kube-system | kubectl get -f -
    ```

    ```
    NAME                                    SECRETS   AGE
    serviceaccount/gpushare-device-plugin   1         39s
    serviceaccount/gpushare-schd-extender   1         39s
    
    NAME                                                           AGE
    clusterrole.rbac.authorization.k8s.io/gpushare-device-plugin   39s
    clusterrole.rbac.authorization.k8s.io/gpushare-schd-extender   39s
    
    NAME                                                                  AGE
    clusterrolebinding.rbac.authorization.k8s.io/gpushare-device-plugin   39s
    clusterrolebinding.rbac.authorization.k8s.io/gpushare-schd-extender   39s
    
    NAME                             TYPE       CLUSTER-IP    EXTERNAL-IP   PORT(S)           AGE
    service/gpushare-schd-extender   NodePort   10.6.13.125   <none>        12345:32766/TCP   39s
    
    NAME                                       DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR    AGE
    daemonset.apps/cgpu-installer              4         4         4       4            4           cgpu=true        39s
    daemonset.apps/device-plugin-evict-ds      4         4         4       4            4           cgpu=true        39s
    daemonset.apps/device-plugin-recover-ds    0         0         0       0            0           cgpu=false   39s
    daemonset.apps/gpushare-device-plugin-ds   4         4         4       4            4           cgpu=true        39s
    
    NAME                                     READY   UP-TO-DATE   AVAILABLE   AGE
    deployment.apps/gpushare-schd-extender   1/1     1            1           38s
    
    NAME                           COMPLETIONS   DURATION   AGE
    job.batch/gpushare-installer   3/1 of 3      3s         38s
    ```


**相关文档**  


[共享GPU概述](/cn.zh-CN/Kubernetes集群用户指南/GPU/NPU/共享GPU调度/共享GPU概述.md)

[运行共享GPU示例](/cn.zh-CN/Kubernetes集群用户指南/GPU/NPU/共享GPU调度/运行共享GPU示例.md)

[监控和隔离GPU资源](/cn.zh-CN/Kubernetes集群用户指南/GPU/NPU/共享GPU调度/监控和隔离GPU资源.md)

