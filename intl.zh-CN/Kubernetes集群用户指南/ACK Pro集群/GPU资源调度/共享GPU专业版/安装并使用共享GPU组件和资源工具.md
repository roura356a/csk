---
keyword: [安装共享GPU资源, GPU资源查询工具, 共享调度]
---

# 安装并使用共享GPU组件和资源工具

ACK提供了GPU共享调度能力，服务于共享单GPU的模型预测场景，同时也可以通过Nvidia驱动内核态保障GPU显存的隔离。本文介绍如何在GPU节点上安装共享GPU组件和GPU资源查询工具，实现GPU的调度和隔离能力。

-   支持共享GPU调度的节点不能设置CPU Policy为`static`。
-   创建ACK Pro集群。ACK Pro集群的实例规格类型需要设置为**异构计算GPU/FPGA/NPU**，其他配置请参见[创建ACK Pro版集群](/intl.zh-CN/Kubernetes集群用户指南/ACK Pro集群/创建ACK Pro版集群.md)。

    **说明：** 仅支持在ACK Pro集群安装共享GPU组件，如果您使用的是专有版集群，您可以[提交工单](https://workorder-intl.console.aliyun.com/console.htm)申请白名单。

-   [t16645.md\#](/intl.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl管理Kubernetes集群.md)。
-   共享GPU组件的安装没有地域限制，但是目前只有以下地域支持GPU显存隔离能力。如果您需要使用显存隔离能力，请确保集群所在地域在此范围内。

    |地域|地域ID|
    |--|----|
    |华北2（北京）|cn-beijing|
    |华东2（上海）|cn-shanghai|
    |华东1（杭州）|cn-hangzhou|
    |华北（张家口）|cn-zhangjiakou|
    |华南1（深圳）|cn-shenzhen|
    |西南1（成都）|cn-chengdu|
    |华南2（河源）|cn-heyuan|
    |中国（香港）|cn-hongkong|
    |印度尼西亚（雅加达）|ap-southeast-5|
    |新加坡|ap-southeast-1|
    |美国（弗吉尼亚）|us-east-1|
    |美国（硅谷）|us-west-1|


## 使用须知

|配置|支持版本|
|--|----|
|Kubernetes|1.18.8及其以上|
|Helm版本|3.0及以上版本|
|Nvidia驱动版本|418.87.01及以上版本|
|Docker版本|19.03.5|
|操作系统|CentOS 7.6、CentOS 7.7、Ubuntu 16.04和Ubuntu 18.04，Alibaba Cloud Linux 2.x|
|支持显卡|Tesla P4、Tesla P100、 Tesla T4和Tesla v100|

## 步骤一：安装共享GPU组件

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，选择**市场** \> **应用目录**。

3.  在应用目录页面的搜索框中搜索ack-ai-installer，找到ack-ai-installer，单击**ack-ai-installer**。

4.  在应用目录-ack-ai-installer页面创建区域选择目标集群，然后单击**创建**。

    安装ack-ai-installer成功后，将会跳转到ack-ai-installer详情页面，您可以看到ack-ai-installer下的各个组件。


## 步骤二：开启GPU共享调度能力和显存隔离能力

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面单击已安装ack-ai-installer组件集群**操作**列的**节点池**。

4.  在节点池页面单击**创建节点池**。

5.  在创建节点池页面，设置创建节点池的配置项。

    关于配置项的详细说明，请参见[创建ACK Pro版集群](/intl.zh-CN/Kubernetes集群用户指南/ACK Pro集群/创建ACK Pro版集群.md)。部分配置项说明如下：

    -   数量：设置节点池初始节点数量。如不需要创建节点，可以填写为0。
    -   操作系统：选择节点的操作系统，包括CentOS、Alibaba Cloud Linux 2.x、Windows。
    -   ECS标签：您可以为ECS实例添加标签。
    -   自定义资源组：您可以指定节点池所扩容节点的资源组信息。
    -   节点标签：为集群节点添加标签。关于节点标签的详细说明，请参见[ACK调度GPU使用的节点标签说明](/intl.zh-CN/Kubernetes集群用户指南/GPU/NPU/GPU调度/ACK调度GPU使用的节点标签说明.md)。
        -   开启GPU共享调度能力，同时开启GPU显存隔离能力。

            单击**节点标签**的![节点标签](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/7405585061/p183919.png)，设置**键**为ack.node.gpu.schedule，**值**为cgpu。

            **说明：** 如果只需要节点池支持共享调度，不支持显存隔离，您可以设置**节点标签**的**键**为ack.node.gpu.schedule，**值**为share。

        -   在节点上使用Binpack算法为Pod选择GPU卡。

            单击**节点标签**的![节点标签](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/7405585061/p183919.png)，设置**键**为ack.node.gpu.placement，设置**值**为binpack。

6.  单击**确认配置**。


## 步骤三：添加GPU节点

完成创建节点池后，您还可以在节点池中添加GPU节点。添加GPU节点时，您需要将实例规格类型设置为**异构计算GPU/FPGA/NPU**。具体操作，请参见[添加已有节点](/intl.zh-CN/Kubernetes集群用户指南/节点与节点池/节点/添加已有节点.md)或[扩容节点池](/intl.zh-CN/Kubernetes集群用户指南/节点与节点池/节点池/管理节点池.md)。

**说明：** 如果您添加节点池时已经创建GPU节点，您可以跳过此步骤。

## 步骤四：安装和使用GPU资源查询工具

1.  配置kubeconfig文件。具体操作，请参见[t16645.md\#](/intl.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl管理Kubernetes集群.md)。

2.  下载kubectl-inspect-cgpu。

    -   如果您使用的是Linux系统，您可以通过以下命令下载kubectl-inspect-cgpu。

        ```
        wget http://aliacs-k8s-cn-beijing.oss-cn-beijing.aliyuncs.com/gpushare/kubectl-inspect-cgpu-linux -O /usr/local/bin/kubectl-inspect-cgpu
        ```

    -   如果您使用的是macOS系统，您可以通过以下命令下载kubectl-inspect-cgpu。

        ```
        wget http://aliacs-k8s-cn-beijing.oss-cn-beijing.aliyuncs.com/gpushare/kubectl-inspect-cgpu-darwin -O /usr/local/bin/kubectl-inspect-cgpu
        ```

3.  为kubectl-inspect-cgpu添加执行权限。

    ```
    chmod +x /usr/local/bin/kubectl-inspect-cgpu
    ```

4.  查看集群GPU使用情况。

    ```
    kubectl inspect cgpu
    ```

    预期输出：

    ```
    NAME                       IPADDRESS      GPU0(Allocated/Total)  GPU Memory(GiB)
    cn-shanghai.192.168.6.104  192.168.6.104  0/15                   0/15
    ----------------------------------------------------------------------
    Allocated/Total GPU Memory In Cluster:
    0/15 (0%)
    ```


