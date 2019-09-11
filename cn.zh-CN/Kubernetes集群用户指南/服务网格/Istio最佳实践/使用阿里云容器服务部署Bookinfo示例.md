# 使用阿里云容器服务部署Bookinfo示例 {#task_1846571 .task}

本文介绍了使用阿里云容器服务部署运行bookinfo示例的过程。

-   您已成功创建一个 Kubernetes 集群，参见[创建Kubernetes集群](../cn.zh-CN/Kubernetes集群用户指南/集群管理/创建集群/创建Kubernetes 集群.md#)，且集群版本为v1.12及以上。
-   您已经创建一个命名空间，参见[创建命名空间](../cn.zh-CN/Kubernetes集群用户指南/命名空间管理/创建命名空间.md#)。
-   您已经成功部署Istio，参见[部署Istio](../cn.zh-CN/Kubernetes集群用户指南/服务网格/Istio管理/部署Istio.md#)。
-   您已经启用 Sidecar 自动注入功能，参见[设置 Sidecar 自动注入](../cn.zh-CN/Kubernetes集群用户指南/服务网格/Istio管理/设置 Sidecar 自动注入.md#)。

## Bookinfo应用介绍 {#section_iuz_dvj_4va .section}

Bookinfo是一个样例应用，用来演示多种 Istio 特性，它由四个单独的微服务构成：

-   `productpage` ：`productpage` 微服务会调用 `details` 和 `reviews` 两个微服务，用来生成页面。
-   `details` ：这个微服务包含了书籍的信息。
-   `reviews` ：这个微服务包含了书籍相关的评论。它还会调用`ratings` 微服务。
-   `ratings` ：`ratings` 微服务中包含了由书籍评价组成的评级信息。

`reviews` 微服务有 3 个版本：

-   v1 版本不会调用 `ratings` 服务。
-   v2 版本会调用 `ratings` 服务，并使用 1 到 5 个黑色星形图标来显示评分信息。
-   v3 版本会调用 `ratings` 服务，并使用 1 到 5 个红色星形图标来显示评分信息。

下图展示了这个应用的端到端架构。

![架构图](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/217501/156818273359173_zh-CN.png)

## 部署Bookinfo应用 {#section_2k4_59c_s38 .section}

本例中，您已经完成部署Istio，并且istio-test命名空间中，开启Sidecar自动注入。

完成后，您可以看到istio-test命名空间下新增标签`istio-injection:enabled`。

![部署Istio](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/217501/156818273359174_zh-CN.png)

您可以使用控制台分别部署productpage，detail，rating及reviews服务的两个版本（v1、v2）。

本例中以部署productpage服务为例进行介绍。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
2.  在Kubernetes菜单下，单击左侧导航栏中的**服务网格** \> **虚拟服务**，然后单击页面右上角的**创建**。
3.  设置**应用名称**、**应用版本** 、 **集群**、**命名空间**和**副本数量**，副本数量即应用包含的Pod数量。然后单击**下一步** 进入容器配置页面。 

    本例中使用 istio-test 命名空间。

    ![配置应用基本信息](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/217501/156818273359175_zh-CN.png)

4.  设置容器配置。 设置镜像名称、镜像版本、所需资源，容器端口等配置信息。

    ![容器设置](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/217501/156818273359176_zh-CN.png)

    **说明：** 

    本示例中，界面参数取值参考如下：

    -   **镜像名称**：您可以单击选择镜像，在弹出的对话框中选择所需的镜像并单击**确定**。您还可以填写私有 registry。本例中为docker.io/istio/example-bookinfo-productpage-v1 。
    -   **镜像版本**：您可以单击选择镜像版本选择镜像的版本。若不指定，默认为 latest。本例中为1.15.0。
    -   **所需资源**：即为该应用预留资源额度，包括 CPU 和 内存两种资源，即容器独占该资源，防止因资源不足而被其他服务或进程争占资源，导致应用不可用。本例中CPU为0.25Core，内存为256MiB。
    -   **端口映射**：您需要添加主机端口和容器端口，支持 TCP/UDP 协议。本例中，容器端口为9080，协议为TCP。
    界面其他参数详细信息请参见[镜像创建无状态Deployment应用](../cn.zh-CN/Kubernetes集群用户指南/应用管理/镜像创建无状态Deployment应用.md#)。

5.  完成容器配置后，单击**下一步**。
6.  进行服务配置。 
    1.  在服务栏单击**创建**，在弹出的对话框中进行配置。 

        ![服务配置](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/217501/156818273459180_zh-CN.png)

        -   **名称**：您可自主设置，默认为`applicationname-svc`。
        -   **类型**：您可以从下面 3 种服务类型中进行选择。
            -   虚拟集群 IP：即 ClusterIP，指通过集群的内部 IP 暴露服务，选择该项，服务只能够在集群内部可以访问。
            -   节点端口：即 NodePort，通过每个 Node 上的 IP 和静态端口（NodePort）暴露服务。NodePort 服务会路由到 ClusterIP 服务，这个 ClusterIP 服务会自动创建。通过请求 `<NodeIP>:<NodePort>`，可以从集群的外部访问一个 NodePort 服务。
            -   负载均衡：即 LoadBalancer，是阿里云提供的负载均衡服务，可选择公网访问或内网访问。负载均衡可以路由到 NodePort 服务和 ClusterIP 服务。
        -   **端口映射**：您需要添加服务端口和容器端口，支持 TCP/UDP 协议。
        -   **注解**：为该服务添加一个注解（annotation），支持负载均衡配置参数，参见[通过负载均衡（Server Load Balancer）访问服务](../cn.zh-CN/Kubernetes集群用户指南/网络管理/通过负载均衡（Server Load Balancer）访问服务.md#)。
        -   **标签**：您可为该服务添加一个标签，标识该服务。
    2.  单击**创建**。 

        ![创建完成](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/217501/156818273459181_zh-CN.png)


您可以参考上述步骤分别创建details，ratings，reviews（v1）这三个服务。

创建完成后，等待所有的service和 pod 都已经正常启动。

## 创建服务网关 {#section_56s_h0u_rde .section}

1.  在 Kubernetes 菜单下， 单击左侧导航栏中的**服务网格** \> **服务网关**，进入服务网关页面。
2.  选择所需的集群及命名空间后，单击右上角**新建**。
3.  填写如下信息，单击**创建**，部署服务网关。 

    ![部署服务网关](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/217501/156818273459214_zh-CN.png)

    参数说明：

    -   **名称**：新创建的服务网关的名称。
    -   **服务网关**：服务代理网关。默认为**istio-ingressgateway**。代理网关必须和新创建的网关部署在同一命名空间。
    -   **端口配置**：单击**添加**，可以增加多个端口的配置。
        -   名称：端口的名称
        -   协议：HTTP/TCP
        -   端口：1-65535
        -   Host：支持多个host
    创建完成后，您可以在服务网关列表中，看到新创建的服务网关。

    ![服务网关](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/217501/156818273459215_zh-CN.png)

4.  将上述服务绑定到Bookinfo-gateway网关，请参见[绑定虚拟服务](../cn.zh-CN/Kubernetes集群用户指南/服务网格/绑定虚拟服务.md#)。 
    1.  在 Kubernetes 菜单下， 单击左侧导航栏中的**服务网格** \> **虚拟服务**，可以看到虚拟服务（Virtual Service）列表。 

        ![虚拟服务列表](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/217501/156818273459218_zh-CN.png)

        **说明：** 未绑定网关的虚拟服务在**服务网关**列会显示**绑定**按钮。

    2.  在目标虚拟服务的服务网关列单击**绑定**，弹出添加页面。 

        ![绑定服务网关](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/217501/156818273459220_zh-CN.png)

    3.  在服务网关中选择对应的网关名称，单击**确定**，即完成虚拟服务的绑定。
5.  在服务网关列表中，在目标服务网关对应的服务列中单击**istio-ingressgateway**，并输入路由/productpage。 

    ![服务网关](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/217501/156818273459226_zh-CN.png)

    可以看到如下页面。

    ![bookinfo示例](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/217501/156818273459225_zh-CN.png)


## 部署reviews-v2 版本 {#section_muv_6xd_2ep .section}

1.  在 Kubernetes 菜单下， 单击左侧导航栏中的**服务网格** \> **虚拟服务**，可以看到虚拟服务（Virtual Service）列表。
2.  在目标服务的操作列单击**管理**，进入服务详情页面。 

    ![管理服务](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/217501/156818273559229_zh-CN.png)

3.  单击**增加灰度版本**。 

    ![增加灰度版本](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/217501/156818273559231_zh-CN.png)

4.  进入创建应用界面。您可以根据需要配置界面参数。 
    1.  在应用基本信息页面，填写**应用版本**和**副本数量**后，单击**下一步**。
    2.  在容器配置页面，设置镜像名称、镜像版本、所需资源，容器端口等配置信息后，单击**下一步**。 

        ![容器配置](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/217501/156818273559236_zh-CN.png)

        **说明：** 

        本示例中，界面参数取值参考如下：

        -   **镜像名称**：您可以单击选择镜像，在弹出的对话框中选择所需的镜像并单击**确定**。您还可以填写私有 registry。本例中为docker.io/istio/example-bookinfo-productpage-v2 。
        -   **镜像版本**：您可以单击选择镜像版本选择镜像的版本。若不指定，默认为 latest。本例中为1.15.0。
        -   **所需资源**：即为该应用预留资源额度，包括 CPU 和 内存两种资源，即容器独占该资源，防止因资源不足而被其他服务或进程争占资源，导致应用不可用。本例中CPU为0.25Core，内存为256MiB。
        -   **端口映射**：您需要添加主机端口和容器端口，支持 TCP/UDP 协议。本例中，容器端口为9080，协议为TCP。
        界面其他参数详细信息请参见[镜像创建无状态Deployment应用](../cn.zh-CN/Kubernetes集群用户指南/应用管理/镜像创建无状态Deployment应用.md#)。

    3.  在灰度策略页面，策略类型选择基于流量比例发布，流量比例中，V2流量比例设置为50%后，单击**创建**。
5.  待pod启动完成后，输入路由/productpage，会看到v2版本的界面。 由于v1、v2流量比例各50%，可能要多刷新几次界面。

    ![bookinfo-sample](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/217501/156818273559239_zh-CN.png)


您可以使用压测工具（例如siege）模拟多次访问，再通过kiali观察流量拓扑。

在部署Istio时已经默认安装了kiali，您只需要新建一个Ingress就可以方便的进行访问。请参见[通过界面创建路由（Ingress）](../cn.zh-CN/Kubernetes集群用户指南/网络管理/通过界面创建路由（Ingress）.md#)。

**说明：** 本例中，目标集群选择已部署Istio的集群，命名空间选择istio-system。

创建完成后，您可以在路由列表中，看到kiali的访问地址。

![访问地址](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/217501/156818273559243_zh-CN.png)

单击kiali的访问地址，打开Kiali控制台，可以看到如下调用效果。

![调用结果](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/217501/156818273559244_zh-CN.png)

**说明：** 用户名密码均默认为admin。

