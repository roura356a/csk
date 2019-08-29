# Kubernetes GPU集群支持GPU调度 {#task_1664343 .task}

本文主要为您介绍Kubernetes GPU集群可以支持GPU调度。

您需要开通容器服务、资源编排（ROS）服务和访问控制（RAM）服务。

登录 [容器服务管理控制台](https://cs.console.aliyun.com/)、[ROS 管理控制台](https://ros.console.aliyun.com/) 和 [RAM 管理控制台](https://ram.console.aliyun.com/) 开通相应的服务。

**说明：** 容器服务 Kubernetes 集群部署依赖阿里云资源编排 ROS 的应用部署能力，所以创建 Kubernetes 集群前，您需要开通 ROS。

自从1.8版本开始，Kubernetes已经明确表示要通过统一的[设备插件方式](https://kubernetes.io/docs/concepts/cluster-administration/device-plugins/)支持像Nvidia GPU，InfiniBand，FPGA等硬件加速设备，而社区的GPU方案将在1.10全面弃用，并在1.11版本彻底从主干代码移除。若您需要通过阿里云Kubernetes集群+GPU运行机器学习，图像处理等高运算密度等任务，无需安装nvidia driver和CUDA，就能实现一键部署和弹性扩缩容等功能。

创建集群过程中，容器服务会进行如下操作：

-   创建 ECS，配置管理节点到其他节点的 SSH 的公钥登录，通过 CloudInit 安装配置 Kubernetes 集群。
-   创建安全组，该安全组允许 VPC 入方向全部 ICMP 端口的访问。
-   如果您不使用已有的 VPC 网络，会为您创建一个新的 VPC 及 VSwitch，同时为该 VSwitch 创建 SNAT。
-   创建 VPC 路由规则。
-   创建 NAT 网关及 EIP。
-   创建 RAM 子账号和 AK，该子账号拥有 ECS 的查询、实例创建和删除的权限，添加和删除云盘的权限，SLB 的全部权限，云监控的全部权限，VPC 的全部权限，日志服务的全部权限，NAS 的全部权限。Kubernetes 集群会根据用户部署的配置相应的动态创建 SLB，云盘，VPC路由规则。
-   创建内网 SLB，暴露 6443 端口。
-   创建公网 SLB，暴露 6443、8443和 22 端口（如果您在创建集群的时候选择开放公网 SSH 登录，则会暴露 22 端口；如果您选择不开放公网 SSH 访问，则不会暴露 22 端口）。

## 使用限制 {#section_pbf_0by_x3x .section}

-   随集群一同创建的负载均衡实例只支持按量付费的方式。
-   Kubernetes 集群仅支持专有网络 VPC。
-   每个账号默认可以创建的云资源有一定的配额，如果超过配额创建集群会失败。请在创建集群前确认您的配额。如果您需要提高您的配额，请提交工单申请。
    -   每个账号默认最多可以创建 5 个集群（所有地域下），每个集群中最多可以添加 40 个节点。如果您需要创建更多的集群或者节点，请提交工单申请。

        **说明：** Kubernetes集群中，VPC默认路由条目不超过48条，意味着Kubernetes集群使用VPC时，默认节点上限是48个，如果需要更大的节点数，需要您先对目标VPC开工单，提高VPC路由条目，再对容器服务提交工单。

    -   每个账号默认最多可以创建 100 个安全组。
    -   每个账号默认最多可以创建 60 个按量付费的负载均衡实例。
    -   每个账号默认最多可以创建 20 个EIP。
-   ECS 实例使用限制：
    -   仅支持 CentOS 操作系统。
    -   支持创建按量付费和包年包月的ECS实例。

**说明：** 实例创建后，您可以通过 ECS 管理控制台将[按量付费转预付费](../../../../../intl.zh-CN/产品定价/按量付费转预付费.md#)。

## 创建GN5型Kubernetes集群 {#section_mjp_yzl_zy6 .section}

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
2.  在 Kubernetes 菜单下，单击左侧导航栏的**集群** \> **集群**，进入集群列表页面。
3.  单击页面右上角的创建**Kubernetes 集群**，进入创建 Kubernetes 集群页面。 

    ![创建集群](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16641/156704119910863_zh-CN.png)

    默认进入**kubernetes**集群配置页面。

    **说明：** 为了创建GPU集群，通常情况下，Worker节点使用GPU类型的ECS。其他集群的参数配置，请参见[ZH-CN\_TP\_16639.md\#](intl.zh-CN/Kubernetes集群用户指南/集群管理/创建集群/创建Kubernetes 集群.md#)。

    ![配置页面](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16641/156704119910864_zh-CN.png)

4.  设置 Worker 节点的配置信息。本例中将Worker节点作为GPU工作节点，选择GPU计算型gn5。 
    1.  若您选择新增实例，则需要选择 Worker 节点的系列和规格，以及需要创建的 Worker 节点的数量（本示例创建2个GPU节点）。 

        ![节点设置](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16641/156704119910865_zh-CN.png)

    2.  若您选择添加已有实例，则需要预先在此地域下创建GPU云服务器。
5.  完成其他配置后，单击**创建集群**，启动部署。
6.  集群创建成功后，单击左侧导航栏中的**集群** \> **节点**，进入节点列表页面。
7.  选择所需的集群，选择创建集群时配置的Worker节点，单击操作列的**更多** \> **详情**，查看该节点挂载的GPU设备。

## 运行TensorFLow的GPU实验环境 {#section_5ve_oks_fi0 .section}

数据科学家通常习惯使用Jupyter作为TensorFlow实验环境，我们这里可以用一个例子向您展示如何快速部署一个Jupyter应用。

1.  在 Kubernetes 菜单下，单击左侧导航栏的**应用** \> **无状态**，进入无状态（Deployment）页面。
2.  单击页面右上角的创建**使用模板创建**。
3.  选择所需的集群，命名空间，选择样例模板或自定义，然后单击**创建**。 

    ![创建应用](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16641/156704119910866_zh-CN.png)

    本例中，示例模板是一个Jupyter应用，包括一个deployment和service。

    ``` {#codeblock_2d8_awk_5e0}
    ---
    # Define the tensorflow deployment
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: tf-notebook
      labels:
        app: tf-notebook
    spec:
      replicas: 1
      selector: # define how the deployment finds the pods it mangages
        matchLabels:
          app: tf-notebook
      template: # define the pods specifications
        metadata:
          labels:
            app: tf-notebook
        spec:
          containers:
          - name: tf-notebook
            image: tensorflow/tensorflow:1.4.1-gpu-py3
            resources:
              limits:
                nvidia.com/gpu: 1                      #指定调用nvidia gpu的数量
            ports:
            - containerPort: 8888
              hostPort: 8888
            env:
              - name: PASSWORD                         # 指定访问Jupyter服务的密码，您可以按照您的需要修改
                value: mypassw0rd
    
    # Define the tensorflow service
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: tf-notebook
    spec:
      ports:
      - port: 80
        targetPort: 8888
        name: jupyter
      selector:
        app: tf-notebook
      type: LoadBalancer                           #阿里云的负载均衡访问内部服务和负载均衡
    ```

    旧的GPU部署方案，您必须要定义如下的nvidia驱动所在的数据卷。

    ``` {#codeblock_h6t_w5e_01t}
    volumes:
        - hostPath:
            path: /usr/lib/nvidia-375/bin
            name: bin
        - hostPath:
            path: /usr/lib/nvidia-375
            name: lib
    ```

    这需要您在编写部署文件时，强依赖于所在的集群，导致缺乏可移植性。但是在Kubernetes 1.9.3及之后的版本中，最终用户无需指定这些hostPath，nvidia的插件会自发现驱动所需的库链接和执行文件。

4.  单击左侧导航栏中的**路由与负载均衡** \> **服务**，选择所需的集群和命名空间，选择tf-notebook服务，查看外部端点。 

    ![查看服务](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16641/156704119910867_zh-CN.png)

5.  在浏览器中访问Jupyter实例，访问地址是`http://EXTERNAL-IP`，输入模板中配置的密码。
6.  您可通过如下的程序，验证这个Jupyter实例可以使用GPU。它将列出Tensorflow可用的所有设备。 

    ``` {#codeblock_bip_hxh_x6r}
    from tensorflow.python.client import device_lib
    
    def get_available_devices():
        local_device_protos = device_lib.list_local_devices()
        return [x.name for x in local_device_protos]
    
    print(get_available_devices())
    ```

    ![查看结果](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16641/156704119910868_zh-CN.png)


