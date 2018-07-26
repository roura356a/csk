# kubernetes上实现 istio 分布式追踪 {#concept_q3c_4hd_5db .concept}

## 背景介绍 {#section_h1c_phd_5db .section}

微服务是如今的焦点，越来越多的 IT 企业开始拥抱微服务。微服务架构将复杂系统切分若干小服务，每个服务可以被独立地开发、部署和伸缩；微服务架构和容器（Docker/Kubernetes）是天作之合，可以进一步简化微服务交付，加强整体系统的弹性和健壮性。

在将单体式应用程序向微服务转型的过程中，由大量的微服务构成的分布式应用架构也会增加运维、调试、和安全管理的复杂性。随着微服务的规模和复杂性的增长，开发者需要面临如服务发现、负载均衡、故障恢复、指标收集和监控，以及A/B测试、限流、访问控制、端到端认证等复杂的挑战，这将是一个棘手的难题。

2017年5月，Google、IBM 和 Lyft 发布了开源服务网格框架 Istio，提供微服务的连接、管理、监控和安全保护。Istio提供了一个服务间通信的基础设施层，解耦了应用逻辑和服务访问中版本管理、安全防护、故障转移、监控遥测等切面的问题。istio 代码无关的特性会吸引企业向微服务转型，将会推动微服务生态的迅猛发展。

## istio 架构原理 {#section_i1c_phd_5db .section}

在 Kubernetes 中，Pod 是一组亲密耦合的容器集合，容器之间共享同一 Network Namespace。借助 Kubernetes 中 Initializer 的扩展机制，在不修改业务 Pod 部署描述前提下，可以为每个业务 Pod 自动创建和启动一个 Envoy 的容器。Envoy 接管同一 pod 内业务容器的进出流量，从而通过在 Envoy 上的控制操作来实现流量管理、微服务跟踪、安全认证、访问控制和策略实施等一系列微服务治理功能。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7252/15325973821151_zh-CN.png)

Istio 服务网格逻辑上分为数据面板和控制面板。

-   数据面板由一组智能代理（Envoy）组成，代理部署为 sidecar，调解和控制微服务之间所有的网络通信。
-   控制面板负责管理和配置代理来路由流量，以及在运行时执行策略。

主要由以下组件构成。

-   **Envoy**：用于调解服务网格中所有服务的所有入站和出站流量。支持 例如动态服务发现，负载均衡，故障注入、流量管理等功能。Envoy 以 sidecar 的方式部署在相关的服务的 Pod 中。
-   **Pilot**：Pilot 负责收集和验证配置并将其传播到各种 Istio组件。
-   **Mixer**：负责在服务网格上执行访问控制和使用策略，并从 Envoy 代理和其他服务收集遥测数据。
-   **Istio-Auth**：提供强大的服务间认证和终端用户认证。

更多关于 istio 的信息，请参见 [isito官方文档](https://istio.io/docs/concepts/what-is-istio/overview.html)。

## 安装 istio {#section_m1c_phd_5db .section}

本例中将以阿里云容器服务 Kubernetes 集群进行演示。

阿里云容器服务在 1.8+ 版本以上的 Kubernetes 集群已经内置开启了 Initializers 插件，无需额外的配置工作。

**说明：** 由于部署 Istio 之后会为每个 Pod 注入 sidecar，来接管服务通信，建议在独立的测试环境中进行验证。

**创建 Kubernetes 集群**

1.  登录 [容器服务管理控制台](https://cs.console.aliyun.com/)。
2.  在 Kubernetes 菜单下，在左侧导航栏中单击 **集群**，再单击右上角的 **创建 Kubernetes 集群**。
3.  开始创建集群，进行集群参数配置。具体如何创建 Kubernetes 集群，请参见 [创建 Kubernetes 集群](../../../../cn.zh-CN/用户指南/Kubernetes 集群.md#)。
4.  集群创建完毕后，等待目标集群的状态变为**运行中**。然后单击集群右侧的**管理**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7252/15325973821153_zh-CN.png)

5.  在集群管理页面，您可以根据集群管理页面信息，配置相应的连接信息。您可以使用 [通过 kubectl 连接 Kubernetes 集群](../../../../cn.zh-CN/用户指南/Kubernetes 集群/集群管理/通过 kubectl 连接 Kubernetes 集群.md#) 或 [SSH 访问 Kubernetes 集群](../../../../cn.zh-CN/用户指南/Kubernetes 集群/集群管理/SSH访问Kubernetes集群.md#) 这两种方式连接集群进行管理。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7252/15325973821154_zh-CN.png)


**部署 istio 发行版**

登录到 Master 节点，执行如下命令，获取最新的 istio 安装包。

```
curl -L https://git.io/getLatestIstio | sh -
```

执行如下命令。

```
cd istio-0.4.0                          ##切换工作目录到Istio
export PATH=$PWD/bin:$PATH              ##添加 istioctl client 到 PATH 环境变量
```

执行如下命令，部署 Istio。

```
kubectl apply -f install/kubernetes/istio.yaml             ## 部署 Istio 系统组件
kubectl apply -f install/kubernetes/istio-initializer.yaml     ## 部署 Istio initializer 插件  
```

部署完毕后，可以用如下命令来验证 Istio 组件是否成功部署。

```
$ kubectl get svc,pod -n istio-systemNAME TYPE CLUSTER-IP EXTERNAL-IP PORT(S) AGEsvc/istio-ingress LoadBalancer 172.21.10.18 101.37.113.231 80:30511/TCP,443:31945/TCP 1msvc/istio-mixer ClusterIP 172.21.14.221  9091/TCP,15004/TCP,9093/TCP,9094/TCP,9102/TCP,9125/UDP,42422/TCP 1msvc/istio-pilot ClusterIP 172.21.4.20  15003/TCP,443/TCP 1mNAME READY STATUS RESTARTS AGEpo/istio-ca-55b954ff7-crsjq 1/1 Running 0 1mpo/istio-ingress-948b746cb-4t24c 1/1 Running 0 1mpo/istio-initializer-6c84859cd-8mvfj 1/1 Running 0 1mpo/istio-mixer-59cc756b48-tkx6c 3/3 Running 0 1mpo/istio-pilot-55bb7f5d9d-wc5xh 2/2 Running 0 1m
```

等待所有的 Pod 进入运行状态，Istio 就已经部署完成了。

## Istio 分布式服务追踪案例 {#section_w1c_phd_5db .section}

**部署测试应用 BookInfo**

BookInfo 是一个类似网上书店的应用，由若干个不同语言编写的独立微服务组成，通过容器方式进行部署，与 Istio 没有任何依赖关系。所有的微服务都将与一个 Envoy sidecar 一起打包，Envoy sidecar 会拦截这些服务入站和出站的调用请求，用于演示 Istio 服务网格的分布式追踪功能。

关于该 BookInfo 应用的更多信息，请参见 [BookInfo指南](https://istio.io/docs/guides/bookinfo.html)。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7252/15325973821155_zh-CN.png)

执行如下命令，部署测试应用 Bookinfo 。

```
kubectl apply -f samples/bookinfo/kube/bookinfo.yaml
```

阿里云 Kubernetes 集群环境下，已经为每个集群配置了 SLB 和 Ingress。执行如下命令获取 ingress 的 IP 地址。

```
$ kubectl get ingress -o wide
NAME      HOSTS     ADDRESS          PORTS     AGE
gateway   *         101.37.xxx.xxx   80        2m
```

如果上面的命令无法获取外部 IP，可以通过如下的方法来获得相应地址。

```
export GATEWAY_URL=$(kubectl get ingress -o wide -o jsonpath={.items[0].status.loadBalancer.ingress[0].ip})
```

当如下命令返回 200 时，就表示应用已经成功部署。

```
curl -o /dev/null -s -w "%{http_code}\n" http://${GATEWAY_URL}/productpage
```

您可以通过浏览器打开`http://${GATEWAY_URL}/productpage` 来访问应用。GATEWAY\_URL 即是 Ingress 的 IP 地址。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7252/15325973821156_zh-CN.png)

**部署 Jaeger 追踪系统**

分布式追踪系统可以帮助观察服务间调用链，是诊断性能问题、分析系统故障的利器。

Istio 生态实现了对不同的分布式追踪系统的支持，包括 [Zipkin](https://github.com/jaegertracing/jaeger) 和 [Jaeger](https://github.com/jaegertracing/jaeger)。本例中使用 Jaeger 进行演示。

Istio v0.4 提供了对 Jaeger 的支持，测试方法如下。

```
kubectl apply -n istio-system -f https://raw.githubusercontent.com/jaegertracing/jaeger-kubernetes/master/all-in-one/jaeger-all-in-one-template.yml
```

部署完成之后，在利用 kubectl 连接集群的方式下，您可以执行如下命令，通过端口映射来访问 Jaeger 控制面板，然后通过浏览器打开 [http://localhost:16686](http://localhost:16686/) 。

```
kubectl port-forward -n istio-system $(kubectl get pod -n istio-system -l app=jaeger -o jsonpath='{.items[0].metadata.name}') 16686:16686 &
```

在 SSH 登录到阿里云 kubernetes 机器的方式下，您可以执行如下命令，查看 jaeger-query 服务的外部访问地址。

```
$ kubectl get svc -n istio-system
NAME               TYPE           CLUSTER-IP      EXTERNAL-IP      PORT(S)                                                            AGE
jaeger-agent       ClusterIP      None            <none>           5775/UDP,6831/UDP,6832/UDP                                         1h
jaeger-collector   ClusterIP      172.21.10.187   <none>           14267/TCP,14268/TCP,9411/TCP                                       1h
jaeger-query       LoadBalancer   172.21.10.197   114.55.82.11     80:31960/TCP     ##外部访问地址即是 114.55.82.11:80                                                   1h
zipkin             ClusterIP      None            <none>           9411/TCP
```

记录 jaeger-query 的外部访问 IP 和端口，通过浏览器打开。

通过多次访问 BookInfo 应用，生成调用链信息，我们可以清楚地看到服务的调用链信息。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7252/15325973821159_zh-CN.png)

单击某个具体的 Trace，可以查看详情。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7252/15325973821160_zh-CN.png)

您也可以查看 DAG 。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7252/15325973821163_zh-CN.png)

## Istio 分布式追踪实现原理 {#section_kbc_phd_5db .section}

Istio 服务网格的核心是 Envoy，是一个高性能的开源 L7 代理和通信总线。在 Istio 中，每个微服务都被注入了 Envoy Sidecar，该实例负责处理所有传入和传出的网络流量。因此，每个 Envoy Sidecar 都可以监控所有的服务间 API 调用，并记录每次服务调用所需的时间以及是否成功完成。

每当微服务发起外部调用时，客户端 Envoy 会创建一个新的 span。一个 span 代表一组微服务之间的完整交互过程，从请求者（客户端）发出请求开始到接收到服务方的响应为止。

在服务交互过程中，客户端会记录请求的发起时间和响应的接收时间，服务器端 Envoy 会记录请求的接收时间和响应的返回时间。

每个 Envoy 都会将自己的 span 视图信息发布到分布式追踪系统。当一个微服务处理请求时，可能需要调用其他微服务，从而导致因果关联的 span 的创建，形成完整的 trace。这就需要由应用来从请求消息中收集和转发下列 Header。

-   `x-request-id`
-   `x-b3-traceid`
-   `x-b3-spanid`
-   `x-b3-parentspanid`
-   `x-b3-sampled`
-   `x-b3-flags`
-   `x-ot-span-context`

在通信链路中的 Envoy，可以截取、处理、转发相应的 Header。

```
Client Tracer                                              Server Tracer
┌──────────────────┐                                       ┌──────────────────┐
│                  │                                       │                  │
│   TraceContext   │           Http Request Headers        │   TraceContext   │
│ ┌──────────────┐ │          ┌───────────────────┐        │ ┌──────────────┐ │
│ │ TraceId      │ │          │ X─B3─TraceId      │        │ │ TraceId      │ │
│ │              │ │          │                   │        │ │              │ │
│ │ ParentSpanId │ │ Extract  │ X─B3─ParentSpanId │ Inject │ │ ParentSpanId │ │
│ │              ├─┼─────────>│                   ├────────┼>│              │ │
│ │ SpanId       │ │          │ X─B3─SpanId       │        │ │ SpanId       │ │
│ │              │ │          │                   │        │ │              │ │
│ │ Sampled      │ │          │ X─B3─Sampled      │        │ │ Sampled      │ │
│ └──────────────┘ │          └───────────────────┘        │ └──────────────┘ │
│                  │                                       │                  │
└──────────────────┘                                       └──────────────────┘
```

具体代码请参见 Istio 文档 [https://istio.io/docs/tasks/telemetry/distributed-tracing.html](https://istio.io/docs/tasks/telemetry/distributed-tracing.html)

## 总结 {#section_qbc_phd_5db .section}

Istio 借助良好的扩展机制和强大的生态正在加速 Service Mesh 的应用和普及。除上文之外还有 Weave Scope， Istio Dashboard 和 Istio-Analytics 项目提供丰富的调用链路可视化和分析能力。

