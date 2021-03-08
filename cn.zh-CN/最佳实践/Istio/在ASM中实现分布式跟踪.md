# 在ASM中实现分布式跟踪

服务网格ASM集成了阿里云链路追踪服务Tracing Analysis，为分布式应用的开发者提供了完整的调用链路还原、调用请求量统计、链路拓扑、应用依赖分析等能力，可以帮助开发者快速分析和诊断分布式应用架构下的性能瓶颈，提升开发诊断效率。本文介绍如何在ASM中使用链路追踪服务。

-   已开通[链路追踪服务](https://tracing-analysis.console.aliyun.com)。
-   创建ASM实例时，启用了链路追踪，详情参见[创建ASM实例]()。

分布式跟踪是一种用于对应用程序进行概要分析和监视的方法，尤其是针对使用微服务架构构建的应用程序。虽然Istio代理能够自动发送Span信息，但是应用程序仍然需要传播适当的HTTP标头，以便在代理发送Span时，可以将Span正确地关联到单个跟踪中。为此，应用程序需要收集以下标头并将其从传入请求传播到任何传出请求：

-   `x-request-id`
-   `x-b3-traceid`
-   `x-b3-spanid`
-   `x-b3-parentspanid`
-   `x-b3-sampled`
-   `x-b3-flags`
-   `x-ot-span-context`

## 部署示例

在按照[部署应用到ASM实例]()进行应用部署之后，查看示例中以Python语言实现的productpage服务，则会发现该应用程序使用了[OpenTracing](https://opentracing.io/)库从HTTP请求中提取了所需的标头。

```
def getForwardHeaders(request):
    headers = {}

    # x-b3-*** headers can be populated using the opentracing span
    span = get_current_span()
    carrier = {}
    tracer.inject(
        span_context=span.context,
        format=Format.HTTP_HEADERS,
        carrier=carrier)

    headers.update(carrier)

    # ...

    incoming_headers = ['x-request-id']

    # ...

    for ihdr in incoming_headers:
        val = request.headers.get(ihdr)
        if val is not None:
            headers[ihdr] = val

    return headers
```

查看以Java语言实现的reviews服务是也可以看到相应的HTTP标头。

```
@GET
@Path("/reviews/{productId}")
public Response bookReviewsById(@PathParam("productId") int productId,
                            @HeaderParam("end-user") String user,
                            @HeaderParam("x-request-id") String xreq,
                            @HeaderParam("x-b3-traceid") String xtraceid,
                            @HeaderParam("x-b3-spanid") String xspanid,
                            @HeaderParam("x-b3-parentspanid") String xparentspanid,
                            @HeaderParam("x-b3-sampled") String xsampled,
                            @HeaderParam("x-b3-flags") String xflags,
                            @HeaderParam("x-ot-span-context") String xotspan) {

  if (ratings_enabled) {
    JsonObject ratingsResponse = getRatings(Integer.toString(productId), user, xreq, xtraceid, xspanid, xparentspanid, xsampled, xflags, xotspan);
```

## 访问示例

在浏览器地址栏输入http://\{入口网关服务的IP地址\}/productpage，可以看到Bookinfo应用的页面。

## 查看应用列表

应用列表页面展示了所有被监控应用的健康度得分、本日请求数、本日错误数等关键指标。您还可以为应用设置自定义标签，从而通过标签进行筛选。

1.  登录[链路追踪 Tracing Analysis 控制台](https://tracing-analysis.console.aliyun.com/#/overview)

2.  在左侧导航栏中选择**应用列表**，并在应用列表页面顶部选择目标地域。

    ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4005219951/p75865.png)


## 查看应用详情

应用详情页面可展示应用在所部属的每一台机器上的关键性能指标、调用拓扑图和调用链路。

1.  登录[链路追踪 Tracing Analysis 控制台](https://tracing-analysis.console.aliyun.com/#/overview)

2.  在左侧导航栏中选择**应用列表**，并在应用列表页面顶部选择目标地域，然后单击对应的应用名称。

3.  在左侧导航栏中选择**应用详情**，在左侧的机器列表中单击**全部**或一台以IP地址标识的机器。

    在**概览**页面上查看调用拓扑图和关键性能指标。在**调用链路**页面上查看该应用在所选机器上的调用链路列表，按耗时降序排列，最多可列出100个调用链路。

    ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5005219951/p75870.png)


## 查看调用链瀑布图

调用链路的瀑布图展示了调用链路的日志产生时间、状态、IP址/机器名称、服务名、时间轴等信息。

1.  在应用详情页面，选择**调用链路**页签，单击指定链路的Trace ID。

2.  在新弹出的**调用链路**页面上查看该调用链路的瀑布图。

    **说明：**

    -   **IP地址**字段显示的是IP地址还是机器名称，取决于**应用设置**页面上的显示配置。详情请参见[管理应用和标签](/cn.zh-CN/控制台操作/应用管理/管理应用和标签.md)。
    -   将鼠标悬浮于服务名上，还可以查看该服务的时长、开始时间、Tag和日志事件等信息。详情参见[查看应用详情](/cn.zh-CN/控制台操作/应用管理/查看应用详情.md)。
    ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5005219951/p75882.png)


