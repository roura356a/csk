# 在Knative 上实现 Tracing 分布式追踪 {#concept_645533 .concept}

链路追踪 Tracing Analysis 为分布式应用的开发者提供了完整的调用链路还原、调用请求量统计、链路拓扑、应用依赖分析等工具。本文介绍了如何在 Knative 上实现 Tracing 分布式追踪，以帮助开发者快速分析和诊断 Knative 中部署的应用服务。

## 前提条件 {#section_llt_cmh_amq .section}

-   您已经成功创建一个 Kubernetes 集群，参见[创建Kubernetes集群](intl.zh-CN/用户指南/Kubernetes集群/集群管理/创建Kubernetes集群.md#)。
-   您已连接到Kubernetes集群的Master节点，参见[通过 kubectl 连接 Kubernetes 集群](intl.zh-CN/用户指南/Kubernetes集群/集群管理/通过 kubectl 连接 Kubernetes 集群.md#)。
-   您已经成功部署 Knative，参见[部署 Knative](intl.zh-CN/用户指南/Kubernetes集群/Knative 管理/部署 Knative.md#)。
-   您已经成功部署 Serving 组件，参见[部署组件](intl.zh-CN/用户指南/Kubernetes集群/Knative 管理/部署组件.md#)。

## 操作步骤 {#section_hnp_6n3_wpf .section}

1.  部署Istio。参考[部署Istio](intl.zh-CN/用户指南/Kubernetes集群/Istio管理/部署Istio.md#)。

    部署时需要关注以下几点：

    -   Pilot 设置跟踪采样百分比，推荐设置大一些（例如，100），便于数据采样。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/519140/156102904749240_zh-CN.png)

    -   启用[阿里云链路追踪服务](https://tracing-analysis.console.aliyun.com/)，查看 token 选择为**on** , 客户端采集工具选择**zipkin** （目前仅支持使用 zipkin 的 /api/v1/spans 接口访问）， 选择集群所在 Region 的接入点（推荐使用内网接入地址），以华南1 Region 为例如下：

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/519140/156102904849241_zh-CN.png)

    -   选择**启用链路追踪**，设置链路追踪服务地址。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/519140/156102904849242_zh-CN.png)

2.  执行如下命令，选择命名空间设置如下标签启用 Sidecar 自动注入`istio-injection=enabled`。

    通过这种方式就注入了 Istio 的 envoy 代理（proxy）容器， Istio 的 envoy 代理拦截流量后会主动上报 trace 系统。以设置 default 命名空间为例：

    ``` {#codeblock_3w9_jnc_jk4}
    kubectl label namespace default istio-injection=enabled
    ```

3.  部署 Knative Service 服务。参考[部署 Serving Hello World 应用示例](intl.zh-CN/用户指南/Kubernetes集群/Knative 管理/部署 Serving Hello World 应用示例.md#)。

    完成后，您可以通过如下命令，访问Hello World 示例服务。

    ``` {#codeblock_r9n_z01_exu}
    $ curl -H "Host: helloworld-go.default.example.com" http://112.124.XX.XX
    Hello Knative!
    ```

4.  登录[链路追踪服务控制台](https://tracing-analysis.console.aliyun.com)，选择**应用列表**，可以查看对应应用的 tracing 信息。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/519140/156102904849243_zh-CN.png)

5.  选择应用，单击查看**应用详情**，可以看到服务调用的平均响应时间。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/519140/156102904849244_zh-CN.png)


## 总结 {#section_klm_51q_jro .section}

在 Knative 中，推荐开启阿里云链路追踪服务。通过 Tracing 不仅有助于复杂应用服务之间的问题排查及效率优化，同时也是 Knative 的最佳实践方式。

