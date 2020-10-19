# 使用阿里云容器服务部署Bookinfo示例

本文介绍了使用阿里云容器服务部署运行Bookinfo示例的过程。

在部署Bookinfo之前，请确保您已完成以下操作：

-   [创建Kubernetes托管版集群](/intl.zh-CN/Kubernetes集群用户指南/集群管理/创建集群/创建Kubernetes托管版集群.md)。

    **说明：** 集群版本为v1.12及以上。

-   [创建命名空间](/intl.zh-CN/Kubernetes集群用户指南/命名空间管理/创建命名空间.md)。
-   [部署Istio](/intl.zh-CN/Kubernetes集群用户指南/服务网格/Istio管理/部署Istio.md)。
-   [设置Sidecar自动注入](/intl.zh-CN/Kubernetes集群用户指南/服务网格/Istio管理/设置Sidecar自动注入.md)。

## Bookinfo应用介绍

Bookinfo是一个样例应用，用来演示多种Istio特性，它由四个单独的微服务构成：

-   `productpage`：调用`details`和`reviews`两个微服务，用来生成页面。
-   `details`：包含了书籍的信息。
-   `reviews`：包含了书籍相关的评论。它还会调用`ratings`微服务。

    `reviews`微服务有3个版本：

    -   v1版本不会调用`ratings`服务。
    -   v2版本会调用`ratings`服务，并使用1到5个黑色星形图标来显示评分信息。
    -   v3版本会调用`ratings`服务，并使用1到5个红色星形图标来显示评分信息。
-   `ratings`：包含了由书籍评价组成的评级信息。

下图展示了该应用的端到端架构。

![架构图](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/9695659951/p59173.png)

## 部署Bookinfo应用

![部署Istio](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/9695659951/p59174.png)

您可以使用控制台分别部署productpage、detail、rating及reviews服务的两个版本（v1、v2）。

本例中以部署productpage服务为例进行介绍。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，选择**服务网格** \> **虚拟服务**。

3.  在**虚拟服务（Virtual Service）**页面，单击右上角的**新建**。

4.  设置**应用名称**、**应用版本**、**集群**、**命名空间**和**副本数量**，副本数量即应用包含的Pod数量。然后单击**下一步**进入容器配置页面。

    本例中使用istio-test命名空间。

    ![配置应用基本信息](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/0795659951/p59175.png)

5.  设置容器配置。

    设置镜像名称、镜像Tag、所需资源、容器端口等配置信息。

    ![容器配置界面](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/7363659951/p94985.png)

    **说明：**

    本示例中，界面参数取值参考如下：

    -   **镜像名称**：您可以单击选择镜像，在弹出的对话框中选择所需的镜像并单击**确定**。您还可以填写私有registry。本例中为docker.io/istio/examples-bookinfo-productpage-v1。
    -   **镜像Tag**：您可以单击选择镜像版本选择镜像的Tag。若不指定，默认为latest。本例中为1.15.0。
    -   **所需资源**：即为该应用预留资源额度，包括CPU和内存两种资源，即容器独占该资源，防止因资源不足而被其他服务或进程争占资源，导致应用不可用。本例中CPU为0.25 Core，内存为256 MiB。
    -   **端口映射**：您需要添加主机端口和容器端口，支持TCP/UDP协议。本例中，容器端口为9080，协议为TCP。
    界面其他参数详细信息请参见[使用镜像创建无状态Deployment应用](/intl.zh-CN/Kubernetes集群用户指南/应用管理/使用镜像创建无状态Deployment应用.md)。

6.  完成容器配置后，单击**下一步**。

7.  进行服务配置。

    1.  在服务栏单击**创建**，在弹出的对话框中进行配置。

        ![服务配置](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/0795659951/p59180.png)

        -   **名称**：您可自主设置，默认为`applicationname-svc`。
        -   **类型**：您可以从下面3种服务类型中进行选择。
            -   虚拟集群IP：即ClusterIP，指通过集群的内部IP暴露服务，选择该项，服务只能够在集群内部可以访问。
            -   节点端口：即NodePort，通过每个Node上的IP和静态端口（NodePort）暴露服务。NodePort服务会路由到ClusterIP服务，这个ClusterIP服务会自动创建。通过请求`<NodeIP>:<NodePort>`，可以从集群的外部访问一个NodePort服务。
            -   负载均衡：即LoadBalancer，是阿里云提供的负载均衡服务，可选择公网访问或内网访问。负载均衡可以路由到NodePort服务和ClusterIP服务。
        -   **端口映射**：您需要添加服务端口和容器端口，支持TCP/UDP协议。
        -   **注解**：为该服务添加一个注解（annotation），支持负载均衡配置参数，请参见[通过Annotation配置负载均衡](/intl.zh-CN/Kubernetes集群用户指南/网络管理/Service管理/通过Annotation配置负载均衡.md)。
        -   **标签**：您可为该服务添加一个标签，标识该服务。
    2.  单击**创建**。

        ![创建完成](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/0795659951/p59181.png)


您可以参考以下Yaml文件分别创建details，ratings，reviews（v1）这三个服务。

-   创建details

    ```
    apiVersion: v1
    kind: Service
    metadata:
      name: details
      labels:
        app: details
        service: details
    spec:
      ports:
      - port: 9080
        name: http
      selector:
        app: details
    ---
    apiVersion: v1
    kind: ServiceAccount
    metadata:
      name: bookinfo-details
      labels:
        account: details
    ---
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: details-v1
      labels:
        app: details
        version: v1
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: details
          version: v1
      template:
        metadata:
          labels:
            app: details
            version: v1
        spec:
          serviceAccountName: bookinfo-details
          containers:
          - name: details
            image: docker.io/istio/examples-bookinfo-details-v1:1.15.0
            imagePullPolicy: IfNotPresent
            ports:
            - containerPort: 9080
    ```

-   创建ratings

    ```
    apiVersion: v1
    kind: Service
    metadata:
      name: ratings
      labels:
        app: ratings
        service: ratings
    spec:
      ports:
      - port: 9080
        name: http
      selector:
        app: ratings
    ---
    apiVersion: v1
    kind: ServiceAccount
    metadata:
      name: bookinfo-ratings
      labels:
        account: ratings
    ---
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: ratings-v1
      labels:
        app: ratings
        version: v1
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: ratings
          version: v1
      template:
        metadata:
          labels:
            app: ratings
            version: v1
        spec:
          serviceAccountName: bookinfo-ratings
          containers:
          - name: ratings
            image: docker.io/istio/examples-bookinfo-ratings-v1:1.15.0
            imagePullPolicy: IfNotPresent
            ports:
            - containerPort: 9080
    ```

-   创建reviews（v1）

    ```
    apiVersion: v1
    kind: Service
    metadata:
      name: reviews
      labels:
        app: reviews
        service: reviews
    spec:
      ports:
      - port: 9080
        name: http
      selector:
        app: reviews
    ---
    apiVersion: v1
    kind: ServiceAccount
    metadata:
      name: bookinfo-reviews
      labels:
        account: reviews
    ---
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: reviews-v1
      labels:
        app: reviews
        version: v1
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: reviews
          version: v1
      template:
        metadata:
          labels:
            app: reviews
            version: v1
        spec:
          serviceAccountName: bookinfo-reviews
          containers:
          - name: reviews
            image: docker.io/istio/examples-bookinfo-reviews-v1:1.15.0
            imagePullPolicy: IfNotPresent
            ports:
            - containerPort: 9080
    ---
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: reviews-v2
      labels:
        app: reviews
        version: v2
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: reviews
          version: v2
      template:
        metadata:
          labels:
            app: reviews
            version: v2
        spec:
          serviceAccountName: bookinfo-reviews
          containers:
          - name: reviews
            image: docker.io/istio/examples-bookinfo-reviews-v2:1.15.0
            imagePullPolicy: IfNotPresent
            ports:
            - containerPort: 9080
    ---
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: reviews-v3
      labels:
        app: reviews
        version: v3
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: reviews
          version: v3
      template:
        metadata:
          labels:
            app: reviews
            version: v3
        spec:
          serviceAccountName: bookinfo-reviews
          containers:
          - name: reviews
            image: docker.io/istio/examples-bookinfo-reviews-v3:1.15.0
            imagePullPolicy: IfNotPresent
            ports:
            - containerPort: 9080
    ```


创建完成后，等待所有的service和pod都已经正常启动。

## 创建服务网关

1.  在控制台左侧导航栏中，选择**服务网格** \> **服务网关**。

2.  选择所需的集群及命名空间后，单击右上角**新建**。

3.  填写以下信息，单击**创建**，部署服务网关。

    ![部署服务网关](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/0795659951/p59214.png)

    参数说明：

    -   **名称**：新创建的服务网关的名称。
    -   **服务网关**：服务代理网关。默认为**istio-ingressgateway**。代理网关必须和新创建的网关部署在同一命名空间。
    -   **端口配置**：单击**添加**，可以增加多个端口的配置。
        -   名称：端口的名称
        -   协议：HTTP/TCP
        -   端口：1-65535
        -   Host：支持多个host
    创建完成后，您可以在服务网关列表中，看到新创建的服务网关。

    ![服务网关](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/0795659951/p59215.png)

4.  将上述服务绑定到Bookinfo-gateway网关，请参见[绑定虚拟服务](/intl.zh-CN/Kubernetes集群用户指南/服务网格/绑定虚拟服务.md)。

    1.  在Kubernetes菜单下， 单击左侧导航栏中的**服务网格** \> **虚拟服务**，可以看到虚拟服务（Virtual Service）列表。

        ![虚拟服务列表](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/0795659951/p59218.png)

        **说明：** 未绑定网关的虚拟服务在**服务网关**列会显示**绑定**按钮。

    2.  在目标虚拟服务的服务网关列单击**绑定**，弹出添加页面。

        ![绑定服务网关](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/0795659951/p59220.png)

    3.  在服务网关中选择对应的网关名称，单击**确定**，即完成虚拟服务的绑定。

5.  在服务网关列表中，在目标服务网关对应的服务列中单击**istio-ingressgateway**，并输入路由/productpage。

    ![服务网关](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/0795659951/p59226.png)

    可以看到如下页面。

    ![bookinfo示例](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/0795659951/p59225.png)


## 部署reviews-v2版本

1.  在Kubernetes菜单下， 单击左侧导航栏中的**服务网格** \> **虚拟服务**，可以看到虚拟服务（Virtual Service）列表。

2.  在目标服务的操作列单击**管理**，进入服务详情页面。

    ![管理服务](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/0795659951/p59229.png)

3.  单击**增加灰度版本**。

    ![增加灰度版本](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/0795659951/p59231.png)

4.  进入创建应用界面。您可以根据需要配置界面参数。

    1.  在应用基本信息页面，填写**应用版本**和**副本数量**后，单击**下一步**。

    2.  在容器配置页面，设置镜像名称、镜像版本、所需资源，容器端口等配置信息后，单击**下一步**。

        ![容器配置](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/1795659951/p59236.png)

        **说明：**

        本示例中，界面参数取值参考如下：

        -   **镜像名称**：您可以单击选择镜像，在弹出的对话框中选择所需的镜像并单击**确定**。您还可以填写私有registry。本例中为docker.io/istio/examples-bookinfo-productpage-v2。
        -   **镜像Tag**：您可以单击选择镜像版本选择镜像的版本。若不指定，默认为latest。本例中为1.15.0。
        -   **所需资源**：即为该应用预留资源额度，包括CPU和内存两种资源，即容器独占该资源，防止因资源不足而被其他服务或进程争占资源，导致应用不可用。本例中CPU为0.25 Core，内存为256 MiB。
        -   **端口映射**：您需要添加主机端口和容器端口，支持TCP/UDP协议。本例中，容器端口为9080，协议为TCP。
        界面其他参数详细信息请参见[使用镜像创建无状态Deployment应用](/intl.zh-CN/Kubernetes集群用户指南/应用管理/使用镜像创建无状态Deployment应用.md)。

    3.  在灰度策略页面，策略类型选择基于流量比例发布，流量比例中，V2流量比例设置为50%后，单击**创建**。

5.  待pod启动完成后，输入路由/productpage，会看到v2版本的界面。

    由于v1、v2流量比例各50%，可能要多刷新几次界面。

    ![bookinfo-sample](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/1795659951/p59239.png)


您可以使用压测工具（例如siege ）模拟多次访问，再通过kiali观察流量拓扑。

在部署Istio时已经默认安装了kiali，您只需要新建一个Ingress就可以方便的进行访问。请参见[创建路由（Ingress）](/intl.zh-CN/Kubernetes集群用户指南/网络管理/Ingress管理/创建路由（Ingress）.md)。

**说明：** 本例中，目标集群选择已部署Istio的集群，命名空间选择istio-system。

创建完成后，您可以在路由列表中，看到kiali的访问地址。

![访问地址](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/1795659951/p59243.png)

单击kiali的访问地址，打开Kiali控制台，可以看到如下调用效果。

![调用结果](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/1795659951/p59244.png)

**说明：** 用户名密码均默认为admin。

