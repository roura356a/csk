# 在Kubernetes上基于Istio实现Service Mesh智能路由 {#concept_klm_wpl_cfb .concept}

阿里云容器服务支持一键部署Istio，并支持多种扩展功能，本例中介绍如何通过Istio实现智能路由。Istio官方文档请参考[intelligent-routing](https://istio.io/docs/examples/intelligent-routing/)。

## 前提条件 {#section_d5b_csp_kfb .section}

-   您已成功部署一个Kubernetes集群，参见[创建Kubernetes集群](../../../../cn.zh-CN/用户指南/Kubernetes 集群/集群管理/创建Kubernetes集群.md#)。
-   您已成功部署了Istio，参见[部署Istio](../../../../cn.zh-CN/用户指南/Kubernetes 集群/Istio管理/部署Istio.md#)。

    **说明：** 本例中Isito的版本是1.0.2。

-   您需要拥有一个本地Linux环境，并已配置好Kubectl工具连接到集群，参见[通过 kubectl 连接 Kubernetes 集群](../../../../cn.zh-CN/用户指南/Kubernetes 集群/集群管理/通过 kubectl 连接 Kubernetes 集群.md#)。
-   您已下载对应Istio版本的项目代码，并在Istio文件目录下执行相关命令。参见[https://github.com/istio/istio/releases](https://github.com/istio/istio/releases)。

## 安装官方示例 {#section_csj_jln_cfb .section}

首先快速安装Bookinfo官方示例，具体可以参见： [https://istio.io/docs/guides/bookinfo](https://istio.io/docs/examples/bookinfo/)。

**快速部署Bookinfo示例应用**

1.  首先为`default` 命名空间打上标签 `istio-injection=enabled`。

    **说明：** 阿里云容器服务Kubernetes集群支持一键部署Istio，并支持自动Sidecar注入。

    ```
    $ kubectl label namespace default istio-injection=enabled
    ```

2.  使用Kubectl命令部署Bookinfo示例应用。

    ```
    $ kubectl apply -f samples/bookinfo/platform/kube/bookinfo.yaml
    ```

    上面的命令会启动全部的四个服务，其中也包括了 `reviews` 服务的三个版本（v1、v2 以及 v3）。

3.  确认所有的服务和 Pod 都已经正确的定义和启动。

    ```
    $ kubectl get svc,pods
    ```

4.  你需要从外部访问应用程序，例如使用浏览器。您需要创建一个[Istio Gateway](https://istio.io/docs/concepts/traffic-management/#gateways) 。为应用程序定义入口网关。

    ```
    $ kubectl apply -f samples/bookinfo/networking/bookinfo-gateway.yaml
    ```

    确认网关创建完成：

    ```
    $ kubectl get gateway
    NAME               AGE
    bookinfo-gateway   32s
    ```

5.  然后确认`istio-ingressgateway`的访问地址。您可通过命令行快速查询。

    ```
    $ kubectl get svc istio-ingressgateway -n istio-system
    ```

    您也可通过容器服务管理控制台，在左侧导航栏单击**应用** \> **服务**，选择集群和Istio-system命名空间，查看`istio-ingressgateway`服务的IP地址。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21221/153932501513546_zh-CN.png)

6.  访问BookInfo首页，地址是`http://{EXTERNAL-IP}/productpage`。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21221/153932501513548_zh-CN.png)

    多次刷新浏览器，将在 productpage 中看到评论的不同的版本，它们会按照 round robin（红星、黑星、没有星星）的方式展现，因为目前为止还没有使用 Istio 来控制版本的路由。


## 请求路由 {#section_qwn_3ln_cfb .section}

BookInfo示例部署了三个版本的reviews服务，因此需要设置一个缺省路由。否则当多次访问该应用程序时，会发现有时输出会包含带星级的评价内容，有时又没有。出现该现象的原因是当没有为应用显式指定缺省路由时，Istio会将请求随机路由到该服务的所有可用版本上。

在使用 Istio 控制 Bookinfo 版本路由之前，你需要在目标规则中定义好可用的版本 。

运行以下命令为 Bookinfo 服务创建的默认的目标规则：

-   如果不需要启用双向TLS，请执行以下命令：

    ```
    $ kubectl apply -f samples/bookinfo/networking/destination-rule-all.yaml
    ```

-   如果需要启用双向 TLS，请执行以下命令：

    ```
    $ kubectl apply -f samples/bookinfo/networking/destination-rule-all-mtls.yaml
    ```

    等待几秒钟，等待目标规则生效。你可以使用以下命令查看目标规则：

    ```
    $ kubectl get destinationrules -o yaml
    ```


## 将所有微服务的缺省版本设置为v1 {#section_rwn_3ln_cfb .section}

通过运行如下命令，将所有微服务的缺省版本设置为v1：

```
$ kubectl apply -f samples/bookinfo/networking/virtual-service-all-v1.yaml
```

可以通过下面的命令来显示所有已创建的路由规则：

```
kubectl get virtualservices -o yaml
```

由于路由规则是通过异步方式分发到代理的，过一段时间后规则才会同步到所有pod上。因此需要等几秒钟后再尝试访问应用。

在浏览器中打开BookInfo应用程序的URL: `http://{EXTERNAL-IP}/productpage`。

可以看到BookInfo应用程序的productpage页面，显示的内容中不包含带星的评价信息，这是因为reviews:v1服务不会访问ratings服务。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21221/153932501512577_zh-CN.png)

## 将来自特定用户的请求路由到reviews:v2 {#section_twn_3ln_cfb .section}

通过运行如下命令，把来自测试用户"jason"的请求路由到reviews:v2，以启用ratings服务。

```
$ kubectl apply -f samples/bookinfo/networking/virtual-service-reviews-test-v2.yaml
```

可以通过如下命令确认规则是否创建：

```
$ kubectl get virtualservice reviews -o yaml
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: reviews
  ...
spec:
  hosts:
  - reviews
  http:
  - match:
    - headers:
        end-user:
          exact: jason
    route:
    - destination:
        host: reviews
        subset: v2
  - route:
    - destination:
        host: reviews
        subset: v1
```

确认规则已创建之后，在浏览器中打开BookInfo应用程序的URL: `http://{EXTERNAL-IP}/productpage`。

以"jason"用户登录productpage页面，您可以在每条评价后面看到星级信息。

**说明：** 账号名称和密码都是`jason`。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21221/153932501512578_zh-CN.png)

**说明：** 本例中，首先使用Istio将100%的请求流量都路由到了BookInfo服务的v1版本；然后再设置了一条路由规则，路由规则基于请求的header（例如一个用户cookie）选择性地将特定的流量路由到了reviews服务的v2版本。

## 故障注入 {#section_uwn_3ln_cfb .section}

为了测试BookInfo微服务应用的弹性，我们计划针对"jason"用户在`reviews:v2`和`ratings`服务之间 注入7秒的延迟 。由于 reviews:v2 服务针对调用ratings服务设置了10秒的超时，因此期望端到端的流程能无错持续。

## HTTP Delay {#section_vwn_3ln_cfb .section}

使用HTTP Delay创建一个故障注入规则，延迟来自用户jason的流量：

```
$ kubectl apply -f samples/bookinfo/networking/virtual-service-ratings-test-delay.yaml
```

确认规则已创建之后，在浏览器中打开BookInfo应用程序的URL: `http://{EXTERNAL-IP}/productpage`。

以"jason"用户登录productpage页面，您可看到如下画面：

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21221/153932501512579_zh-CN.png)

**说明：** 整个review服务失败的原因：productpage和review服务之间的超时小于（3秒加上一次重试，总共6秒）review服务和rating服务之间的超时（10秒）。在由不同开发团队负责独立开发不同微服务的典型企业应用中，这类bug就会发生。Istio的故障注入规则有助于识别这些异常，而无需影响到最终用户。

## HTTP Abort {#section_wwn_3ln_cfb .section}

类似地，使用HTTP Abort创建一个故障注入规则：

```
$ kubectl apply -f samples/bookinfo/networking/virtual-service-ratings-test-abort.yaml
```

确认规则已创建之后，在浏览器中打开BookInfo应用程序的URL: `http://{EXTERNAL-IP}/productpage`。

以"jason"用户登录productpage页面，应该可以看到如下画面：

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21221/153932501512580_zh-CN.png)

## 流量转移 {#section_xwn_3ln_cfb .section}

除了基于内容的路由，Istio还支持基于权重的路由规则。

首先，将所有微服务的缺省版本设置为v1：

```
$ kubectl replace -f samples/bookinfo/networking/virtual-service-all-v1.yaml
```

其次，使用下面的命令把50%的流量从reviews:v1转移到reviews:v3:

```
$ kubectl replace -f samples/bookinfo/networking/virtual-service-reviews-50-v3.yaml
```

在浏览器中多次刷新productpage页面，大约有50%的几率会看到页面中出现带红星的评价内容。

**说明：** 注意该方式和使用容器编排平台的部署特性来进行版本迁移是完全不同的。容器编排平台使用了实例scaling来对流量进行管理。而通过Istio，两个版本的reviews服务可以独立地进行扩容和缩容，并不会影响这两个版本服务之间的流量分发。

如果觉得 `reviews：v3` 微服务已经稳定，你可以通过以下命令， 将virtual service 100％的流量路由到 `reviews：v3`，从而实现一个灰度发布的功能。

```
$ kubectl replace -f samples/bookinfo/networking/virtual-service-reviews-v3.yaml
```

## 总结 {#section_ywn_3ln_cfb .section}

我们可以利用阿里云Kubernetes容器服务，快速搭建一套用于连接、管理以及安全化微服务的开放平台Istio，为应用引入和配置多个相关服务。本文通过一个官方示例来尝试了Istio 的流量路由、故障注入、流量转移等功能。欢迎大家使用阿里云上的容器服务，快速搭建微服务的开放治理平台Istio，比较简单地集成到自己项目的微服务开发中。

