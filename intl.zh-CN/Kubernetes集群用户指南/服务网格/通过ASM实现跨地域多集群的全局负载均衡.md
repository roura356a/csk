# 通过ASM实现跨地域多集群的全局负载均衡

服务网格ASM通过Envoy代理为应用服务提供了全局负载均衡能力，其能够以最佳方式将流量路由至某个服务位于多个地域的应用实例。本文以Bookinfo应用为例，介绍ASM跨地域多集群的全局负载均衡的能力。

-   创建一个ASM实例（本例中mesh1），详情请参见[创建ASM实例]()。
-   在集群创建之前做好网络规划，保证两个集群所使用的交换机IPv4网段不能重叠、Pod网络CIDR与Service CIDR不能重叠。
-   在两个VPC 下分别创建两个ACK集群（本例中m1c1和m1c2），详情请参见[创建Kubernetes专有版集群](/intl.zh-CN/Kubernetes集群用户指南/集群管理/创建集群/创建Kubernetes专有版集群.md)。
-   开通[云企业网（CEN）](https://cen.console.aliyun.com/)。
-   两个VPC通过CEN实现互通，详情请参见[教程概览]()。
-   在云企业网控制台中发布每一个网段，以确保这些网段在CEN中的状态为已发布并且不存在网段冲突。详情请参见[发布路由到云企业网]()。

-   本文所使用的网络规划如下所示：

    |对象|集群m1c1|集群m1c2|
    |--|------|------|
    |VPC、交换机IPv4网段|192.168.0.0/24|192.168.1.0/24|
    |Pod网络CIDR|172.22.0.0/16|172.30.0.0/16|
    |Service CIDR|172.23.0.0/20|172.31.0.0/20|

-   在ACK集群中，Pod的位置是通过其部署节点上的region和zone标签确定的，详情请参见[Well-Known Labels, Annotations and Taints](https://kubernetes.io/docs/reference/kubernetes-api/labels-annotations-taints/#failure-domainbetakubernetesioregion)。ACK集群默认已经配置了这些信息。

## 步骤一：部署应用示例到跨地域的多个集群上

部署Bookinfo应用到跨地域的不同VPC的多个集群上。

## 步骤二：启用服务就近访问

为了确定服务对应的任一实例何时异常，Envoy代理需要在每个服务的目标规则中进行异常检测配置，详情请参见[DestinationRule](https://istio.io/docs/reference/config/networking/destination-rule/#OutlierDetection)。在此情况下，ASM会根据发送请求的Envoy代理位置，针对目标服务的工作负载实例，进行优先级排序。当所有应用实例都正常时，请求将保留在同一位置，即保持服务就近访问。

1.  登录[ASM控制台](https://servicemesh.console.aliyun.com)。

2.  在左侧导航栏，选择**服务网格** \> **网格管理**。

3.  在**网格管理**页面，找到待配置的实例，单击实例的名称或在**操作**列中单击**管理**。

4.  在**基本信息**页面单击右上角的**功能设置**。

5.  在**功能设置更新**页面中勾选**启用服务就近访问**。

6.  单击**确定**。

    在**基本信息**页面可以看到**服务就近访问**的状态变为**开启**。


此时没有在服务reviews对应的目标规则中定义异常检测配置，因此访问productpage页面，刷新页面时会轮流显示reviews的3个版本，即：

-   v1版本不会调用Ratings服务。
-   v2版本会调用Ratings服务，并使用1到5个黑色星形图标来显示评分信息。
-   v3版本会调用Ratings服务，并使用1到5个红色星形图标来显示评分信息。

## 步骤三：定义异常检测配置

启用了服务就近访问的负载均衡之后，需要定义目标规则中的异常检测配置，Envoy代理才能确定应用实例是否正常，进而实现服务就近访问。

1.  登录[ASM控制台](https://servicemesh.console.aliyun.com)。

2.  在左侧导航栏，选择**服务网格** \> **网格管理**。

3.  在**网格管理**页面，找到待配置的实例，单击实例的名称或在**操作**列中单击**管理**。

4.  在**控制平面**区域，选择**DestinationRule**页签，然后单击**新建**。

5.  在**新建**页面，输入DestinationRule的信息，单击**确定**。

    1.  在**命名空间**下拉列表中，选择待新建DestinationRule的命名空间。

    2.  在文本框中，输入DestinationRule的配置信息。

        对应的Yaml文件内容如下所示：

        ```
        apiVersion: networking.istio.io/v1alpha3
        kind: DestinationRule
        metadata:
          name: reviews
        spec:
          host: reviews
          trafficPolicy:
            outlierDetection:
              consecutiveErrors: 5
              interval: 2m
              baseEjectionTime: 5m
        ```

    在**DestinationRule**页签，可以看到新建的DestinationRule。


由于productpage服务与reivews服务的版本v1和v2位于同一个位置，而v3则是位于不同的地域，因此按照就近访问原则，在reviews服务版本v1和v2正常情况下，流量不会路由到v3。访问productpage 页面，刷新页面时会轮流显示reviews的2个版本，即：

-   v1版本不会调用Ratings服务。
-   v2版本会调用Ratings服务，并使用1到5个黑色星形图标来显示评分信息。

**说明：** 假设位于集群m1c1的reviews服务出现故障（可以通过缩容Pod数量为0进行模拟），再次访问productpage页面，则可以看到页面中的reviews服务调用了v3版本，即使用1到5个红色星形图标来显示评分信息。

