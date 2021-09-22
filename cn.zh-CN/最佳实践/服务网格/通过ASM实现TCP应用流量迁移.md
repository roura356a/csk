---
keyword: [应用流量迁移, 灰度切换]
---

# 通过ASM实现TCP应用流量迁移

服务网格ASM的流量管理功能可以实现应用的流量迁移。本文将通过示例介绍如何通过ASM实现TCP应用流量的迁移。

-   已开通以下服务：
    -   [容器服务](https://cs.console.aliyun.com/)
    -   [负载均衡](https://slb.console.aliyun.com/)
-   已创建至少一个ACK集群。如果没有创建，请参见[创建Kubernetes专有版集群](/cn.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes专有版集群.md)和[创建Kubernetes托管版集群](/cn.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。
-   已创建一个ASM实例，并已将ACK集群添加到ASM实例中，请参见[创建ASM实例]()和[添加集群到ASM实例]()。

本文以Istio官方Task TCP-Traffic-Shifting为例来讲述如何实现在一个TCP服务的两个版本之间进行流量灰度切换。该Task中的服务是一个简单地Echo服务，在v1版本中，该服务在收到的数据在前面加上“one”并返回；在v2版本中，该服务在收到的数据前面加上“two”并返回。

## 步骤一：部署示例应用

1.  部署TCP- Echo应用的2个版本。

    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

    2.  在控制台左侧导航栏中，单击**集群**。

    3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**应用管理**。

    4.  在**无状态**页面**命名空间**下拉列表中选择命名空间。

    5.  单击右上方的**使用YAML创建资源**。

    6.  设置**示例模板**为**自定义**，将下面的Yaml模版粘贴到**模版**文本框中，单击**创建**。

        ```
        apiVersion: apps/v1
        kind: Deployment
        metadata:
          name: tcp-echo-v1
        spec:
          replicas: 1
          selector:
            matchLabels:
              app: tcp-echo
              version: v1
          template:
            metadata:
              labels:
                app: tcp-echo
                version: v1
            spec:
              containers:
              - name: tcp-echo
                image: docker.io/istio/tcp-echo-server:1.1
                imagePullPolicy: IfNotPresent
                args: [ "9000", "one" ]
                ports:
                - containerPort: 9000
        ---
        apiVersion: apps/v1
        kind: Deployment
        metadata:
          name: tcp-echo-v2
        spec:
          replicas: 1
          selector:
            matchLabels:
              app: tcp-echo
              version: v2
          template:
            metadata:
              labels:
                app: tcp-echo
                version: v2
            spec:
              containers:
              - name: tcp-echo
                image: docker.io/istio/tcp-echo-server:1.1
                imagePullPolicy: IfNotPresent
                args: [ "9000", "two" ]
                ports:
                - containerPort: 9000
        ```

        在**无状态**页面可以看到新创建的两个版本的TCP-Echo应用。

2.  创建一个服务，并将其对外暴露。

    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

    2.  在控制台左侧导航栏中，单击**集群**。

    3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**应用管理**。

    4.  在集群管理页左侧导航栏中，选择**网络** \> **服务**。

    5.  在**服务**页面**命名空间**下拉列表中选择命名空间，单击右上角的**创建**。

    6.  在**创建服务**对话框中，设置服务的相关信息，然后单击**创建**。

        需要设置的参数如下所示：

        -   **名称**：设为tcp-echo。
        -   **类型**：选择服务类型，即服务访问的方式。支持**虚拟集群IP**、**节点端口**和**负载均衡**。

            **说明：** 您的服务类型为**虚拟集群IP**时，才能设置**实例间发现服务（Headless Service）**。您可以使用无头Service与其他服务发现机制进行接口，而不必与Kubernetes的实现捆绑在一起。

        -   **关联**：选择tcp-echo-v1。

            **说明：** 将从关联的Deployment中提取app这个标签作为Service的Selector，这决定了Kubernetes service将流量转发到哪个Deployment。由于tcp-echo-v1和tcp-echo-v2两个Deployment拥有相同的标签，即app:tcp-echo，因此可以关联任一Deployment。

        -   **外部流量策略**：可选值为Local或Cluster。

            **说明：** 您的服务类型为**节点端口**或**负载均衡**时，才能设置**外部流量策略**。

        -   **端口映射**：**名称**设为tcp；**服务端口**和**容器端口**设为9000；**协议**设为TCP。
        -   **注解**：为该服务添加一个注解（annotation），配置负载均衡的参数，例如设置`service.beta.kubernetes.io/alicloud-loadbalancer-bandwidth:20`表示将该服务的带宽峰值设置为20Mbit/s，从而控制服务的流量。更多参数请参见[t16677.md\#](/cn.zh-CN/Kubernetes集群用户指南/网络/Service管理/通过Annotation配置负载均衡.md)。
        -   **标签**：您可为该服务添加一个标签，标识该服务。
        在**服务（Service）**页面可以看到新创建的服务，type-echo。


## 步骤二：设置服务网格ASM的路由规则

通过设置服务网格的Istio网关、虚拟服务和目标规则，将流量全部指向tcp-echo服务的v1版本。

1.  登录[ASM控制台](https://servicemesh.console.aliyun.com)。

2.  在左侧导航栏，选择**服务网格** \> **网格管理**。

3.  在**网格管理**页面，找到待配置的实例，单击实例的名称或在**操作**列中单击**管理**。

4.  创建服务网关。

    1.  在网格详情页面左侧导航栏选择**流量管理** \> **网关规则**，然后在右侧页面单击**新建**。

    2.  在**新建**面板，从**命名空间**下拉列表中选择default，并在文本框中输入以下Yaml文件内容，单击**确定**。

        ```
        apiVersion: networking.istio.io/v1alpha3
        kind: Gateway
        metadata:
          name: tcp-echo-gateway
        spec:
          selector:
            istio: ingressgateway
          servers:
          - port:
              number: 31400
              name: tcp
              protocol: TCP
            hosts:
            - "*"
        ```

5.  创建虚拟服务。

    1.  在网格详情页面左侧导航栏选择**流量管理** \> **虚拟服务**，然后在右侧页面单击**新建**。

    2.  在**新建**面板，从**命名空间**下拉列表中选择default，并在文本框中输入以下Yaml文件内容，单击**确定**。

        ```
        apiVersion: networking.istio.io/v1alpha3
        kind: VirtualService
        metadata:
          name: tcp-echo
        spec:
          hosts:
          - "*"
          gateways:
          - tcp-echo-gateway
          tcp:
          - match:
            - port: 31400
            route:
            - destination:
                host: tcp-echo
                port:
                  number: 9000
                subset: v1
        ```

6.  创建目标规则。

    1.  在网格详情页面左侧导航栏选择**流量管理** \> **目标规则**，然后在右侧页面单击**新建**。

    2.  在**新建**面板，从**命名空间**下拉列表中选择default，并在文本框中输入以下Yaml文件内容，单击**确定**。

        ```
        apiVersion: networking.istio.io/v1alpha3
        kind: DestinationRule
        metadata:
          name: tcp-echo-destination
        spec:
          host: tcp-echo
          subsets:
          - name: v1
            labels:
              version: v1
          - name: v2
            labels:
              version: v2
        ```


## 步骤三：部署入口网关

在入口网关中，添加端口31400，并指向Istio网关的31400端口。

1.  登录[ASM控制台](https://servicemesh.console.aliyun.com)。

2.  在左侧导航栏，选择**服务网格** \> **网格管理**。

3.  在**网格管理**页面，找到待配置的实例，单击实例的名称或在**操作**列中单击**管理**。

4.  在网格详情页面左侧导航栏单击**ASM网关**，然后在右侧页面单击**部署默认入口网关**。

5.  在**部署入口网关**面板，为集群添加入口网关。

    1.  从**部署集群**列表中选择要部署入口网关的集群。

    2.  指定负载均衡的类型为**公网访问**。

    3.  选择负载均衡。

        -   使用已有负载均衡：从已有负载均衡列表中选择。
        -   新建负载均衡：单击**新建负载均衡**，从下拉列表中选择所需的负载均衡规格。
        **说明：** 建议您为每个Kubernetes服务分配一个SLB。如果多个Kubernetes服务复用同一个SLB，存在以下风险和限制：

        -   使用已有的SLB会强制覆盖已有监听，可能会导致您的应用不可访问。
        -   Kubernetes通过Service创建的SLB不能复用，只能复用您手动在控制台（或调用OpenAPI）创建的SLB。
        -   复用同一个SLB的多个Service不能有相同的前端监听端口，否则会造成端口冲突。
        -   复用SLB时，监听的名字以及虚拟服务器组的名字被Kubernetes作为唯一标识符。请勿修改监听和虚拟服务器组的名字。
        -   不支持跨集群复用SLB。
    4.  单击**添加端口**，将**名称**设为tcp，**服务端口**和**容器端口**设为31400。

        **说明：** 服务端口指的是整个网格对外暴露的端口，是外部访问使用的端口；而容器端口指的是从服务端口进来的流量所指向的Istio网关端口，所以容器端口需要与Istio网关一致。

    5.  单击**确定**。


## 步骤四：检查部署结果

通过Kubectl确认tcp-echo服务的流量指向是否符合预期。

1.  通过Kubectl客户端连接至Kubernetes集群。详情请参见[步骤二：选择集群凭证类型](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl工具连接集群.md)。

2.  执行以下命令，获得服务的地址与端口。

    ```
    export INGRESS_HOST=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
    export INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="tcp")].port}')
    ```

3.  使用telnet指令向tcp-echo服务发起连接请求。

    ```
    telnet $INGRESS_HOST $INGRESS_PORT
    Trying xxx.xxx.xxx.xxx...
    Connected to xxx.xxx.xxx.xxx.
    Escape character is '^]'
    ```

4.  输入任意字符串，按Enter发送，返回的字符串前面带了“one”。这说明tcp-echo服务已经成功部署，且流量全部指向了tcp-echo-v1版本。


## 步骤五：按比例将流量路由到tcp-echo-v2

此处将20%的流量指向tcp-echo-v2版本，其余80%仍然打到tcp-echo-v1。

1.  修改ASM实例的虚拟服务配置。

    1.  在网格详情页面左侧导航栏选择**流量管理** \> **虚拟服务**。

    2.  在**虚拟服务**页面，单击tcp-echo所在**操作**列中的**YAML**。

    3.  在**编辑实例**页面的文本框中输入以下YAML文件内容，单击**确定**。

        ```
        apiVersion: networking.istio.io/v1alpha3
        kind: VirtualService
        metadata:
          name: tcp-echo
        spec:
          hosts:
          - "*"
          gateways:
          - tcp-echo-gateway
          tcp:
          - match:
            - port: 31400
            route:
            - destination:
                host: tcp-echo
                port:
                  number: 9000
                subset: v1
              weight: 80
            - destination:
                host: tcp-echo
                port:
                  number: 9000
                subset: v2
              weight: 20
        ```

2.  执行以下命令，向tcp-echo服务发起10次请求。

    ```
     for i in {1..10}; do \
    docker run -e INGRESS_HOST=$INGRESS_HOST -e INGRESS_PORT=$INGRESS_PORT -it --rm busybox sh -c "(date; sleep 1) | nc $INGRESS_HOST $INGRESS_PORT"; \
    done
    one Mon Nov 12 23:38:45 UTC 2018
    two Mon Nov 12 23:38:47 UTC 2018
    one Mon Nov 12 23:38:50 UTC 2018
    one Mon Nov 12 23:38:52 UTC 2018
    one Mon Nov 12 23:38:55 UTC 2018
    two Mon Nov 12 23:38:57 UTC 2018
    one Mon Nov 12 23:39:00 UTC 2018
    one Mon Nov 12 23:39:02 UTC 2018
    one Mon Nov 12 23:39:05 UTC 2018
    one Mon Nov 12 23:39:07 UTC 2018
    ```

    根据以上请求的分发情况，可以看到20%的流量指向了tcp-echo-v2。

    **说明：** 您的测试结果可能不一定总是10次中有2次打到tcp-echo-v2，但从较长时间范围的总体比例来看，一定是接近20% 的。


