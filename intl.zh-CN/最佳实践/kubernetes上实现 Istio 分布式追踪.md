# kubernetes上实现 Istio 分布式追踪 {#concept_q3c_4hd_5db .concept}

本文通过一个示例演示了如何在启用了Istio的应用中使用分布式追踪系统Jaeger。

在由单体架构迁移至微服务时，传统的监视工具往往无法提供跨越不同服务的可见性。因此就有必要引入分布式跟踪的工具。

为了解决不同的分布式追踪系统 API 不兼容的问题，诞生了 OpenTracing 规范。OpenTracing 是一个轻量级的标准化层，它位于应用程序/类库和追踪或日志分析程序之间。OpenTracing 已进入 CNCF，正在为全球的分布式追踪，提供统一的概念和数据标准。它通过提供平台无关、厂商无关的 API，使得开发人员能够方便的添加（或更换）追踪系统的实现。

Jaeger 是[CNCF](https://www.cncf.io/)下的一款开源分布式追踪系统，兼容 OpenTracing API。

## 前提条件 {#section_llz_ljq_kfb .section}

-   您已成功部署一个Kubernetes集群，参见[创建Kubernetes集群](../../../../intl.zh-CN/用户指南/Kubernetes 集群/集群管理/创建Kubernetes集群.md#)。
-   您需要拥有一个本地Linux环境，并已配置好Kubectl工具连接到集群，参见[通过 kubectl 连接 Kubernetes 集群](../../../../intl.zh-CN/用户指南/Kubernetes 集群/集群管理/通过 kubectl 连接 Kubernetes 集群.md#)。
-   您已下载对应Istio版本的项目代码，并在Istio文件目录下执行相关命令。参见[https://github.com/istio/istio/releases](https://github.com/istio/istio/releases)。

## 步骤1 一键部署 Jaeger 追踪系统 {#section_njx_szf_mfb .section}

1.  登录 [容器服务管理控制台](https://cs.console.aliyun.com)。
2.  单击左侧导航栏的**集群**，选择所需集群，单击**更多** \> **部署Istio**。

    **说明：** 本例中Isito的版本是1.0，更多关于部署Istio的信息，请参见[部署Istio](../../../../intl.zh-CN/用户指南/Kubernetes 集群/Istio管理/部署Istio.md#)。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15820/153966919110115_zh-CN.png)

3.  在部署 Istio 页面中，选择**启用阿里云日志服务 SLS 及 Jaeger**。阿里云容器服务支持通过Web界面一键部署Jaeger追踪系统。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21299/153966919113746_zh-CN.png)

4.  部署完成后，在左侧菜单栏中单击**应用** \> **服务**，选择所需集群和命名空间，找到tracing-on-sls-query服务。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15820/153966919110117_zh-CN.png)

5.  单击tracing-on-sls-query服务的外部地址，即可进入Jaeger UI页面。

    您可开始利用Jaeger观察服务间调用链，诊断性能问题、分析系统故障。


## 步骤2 部署分布式服务追踪示例Bookinfo {#section_w1c_phd_5db .section}

1.  首先为`default` 命名空间打上标签 `istio-injection=enabled`。

    **说明：** 阿里云容器服务Kubernetes集群支持一键部署Istio，并支持自动Sidecar注入。更多信息，参见[BookInfo指南](https://istio.io/docs/examples/bookinfo/)。

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

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15820/153966919110116_zh-CN.png)

6.  访问BookInfo首页，地址是`http://{EXTERNAL-IP}/productpage`。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15820/153966919113776_zh-CN.png)

    多次刷新浏览器，将在 productpage 中看到评论的不同的版本，它们会按照 round robin（红星、黑星、没有星星）的方式展现，因为目前为止还没有使用 Istio 来控制版本的路由。


## 步骤3 查看Jaeger追踪结果 {#section_ign_cbg_mfb .section}

Jaeger UI显示了分布式服务追踪信息的结果。

1.  单击tracing-on-sls-query服务的外部地址，进入Jaeger UI 页面。
2.  在左侧选择**服务**，然后选择各项配置，最后单击**Find Traces**，右上角显示的时刻和持续时间散点图用可视化方式呈现了结果，并提供了向下挖掘能力。

    **说明：** 本例中以BookInfo示例应用为例，查看productpage服务的调用情况。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15820/153966919110119_zh-CN.png)

3.  用户可以选择用多种不同视图对追踪结果进行可视化，例如追踪时段内的直方图，或服务在追踪过程中的累积时间。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15820/153966919210120_zh-CN.png)


## Istio 分布式追踪实现原理 {#section_kbc_phd_5db .section}

尽管Istio代理能够自动发送spans，但他们需要一些标识来将整个调用链关系联系起来。应用程序需要传入合适的HTTP header信息，便于代理发送span信息到Jaeger时，span可以准确地把每次调用关联起来。

为此，应用程序需要从传入的请求中收集如下的header信息并将其传入到每个传出请求：

```
x-request-id
x-b3-traceid
x-b3-spanid
x-b3-parentspanid
x-b3-sampled
x-b3-flags
x-ot-span-context
```

示例服务中的productpage应用（Python应用）从HTTP请求中提取所需的header信息：

```
defgetForwardHeaders(request):
    headers = {}

    user_cookie = request.cookies.get("user")
    if user_cookie:
        headers['Cookie'] = 'user=' + user_cookie

    incoming_headers = [ 'x-request-id',
                         'x-b3-traceid',
                         'x-b3-spanid',
                         'x-b3-parentspanid',
                         'x-b3-sampled',
                         'x-b3-flags',
                         'x-ot-span-context'
    ]

    for ihdr in incoming_headers:
        val = request.headers.get(ihdr)
        if val isnotNone:
            headers[ihdr] = val
            #print "incoming: "+ihdr+":"+valreturn headers
```

在应用程序中调用其他服务时，这些header信息会被传播下去形成一个调用链。

具体代码请参见 Istio 文档[https://istio.io/docs/tasks/telemetry/distributed-tracing.html](https://istio.io/docs/tasks/telemetry/distributed-tracing.html)。

## 总结 {#section_qbc_phd_5db .section}

我们可以利用阿里云Kubernetes容器服务，快速搭建一套用于连接、管理以及安全化微服务的开放平台Istio，为应用引入和配置多个相关服务。本文通过一个示例演示了如何在启用了Istio的应用中使用分布式追踪系统Jaeger。 欢迎大家使用阿里云上的容器服务，快速搭建微服务的开放治理平台Istio，比较简单地集成到自己项目的微服务开发中。

