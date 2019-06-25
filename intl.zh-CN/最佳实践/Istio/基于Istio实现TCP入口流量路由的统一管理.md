# 基于Istio实现TCP入口流量路由的统一管理 {#concept_qzh_bb5_cfb .concept}

本文通过一个示例来讲述如何通过一个简单且标准的Istio规则来控制TCP入口流量的路由，从而实现TCP入口流量路由的统一管理。

## 背景信息 {#section_gvz_cb5_cfb .section}

使用Istio的流量管理模型，将流量与基础设施扩容进行解耦，让运维人员可以通过Pilot指定流量遵循的规则。通过将流量从基础设施扩容中解耦，就可以让 Istio 提供各种独立于应用程序代码之外的流量管理功能。 这些功能都是通过部署的Envoy sidecar代理来实现的。

在一个典型的网格中，通常有一个或多个用于终结外部 TLS 链接，将流量引入网格的负载均衡器（我们称之为 Gateway），然后流量通过sidecar Gateway流经内部服务。下图描绘了网关在网格中的使用情况：

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/150433/156142972348682_zh-CN.png)

Istio Gateway 为 HTTP/TCP 流量配置了一个负载均衡，多数情况下在网格边缘进行操作，用于启用一个服务的入口（ingress）流量。网格中可以存在任意数量的 Gateway，并且多个不同的 Gateway 实现可以共存。 对于入口流量管理，可能会问为什么不直接使用 Kubernetes Ingress API ？ 原因是 Ingress API 无法表达 Istio 的路由需求。 Ingress 试图在不同的 HTTP 代理之间取一个公共的交集，因此只能支持最基本的 HTTP 路由，最终导致需要将代理的其他高级功能放入到注解（annotation）中，而注解的方式在多个代理之间是不兼容的，无法移植。

此外，Kubernetes Ingress本身不支持TCP协议。因此，即使TCP不是NGINX的限制，也无法通过Ingress创建来配置NGINX Ingress Controller以进行TCP负载均衡。当然可以通过创建一个Kubernetes ConfigMap，来使用NGINX的TCP负载均衡功能，具体请参见[Support for TCP/UDP Load Balancing](https://github.com/nginxinc/kubernetes-ingress/tree/master/examples/tcp-udp) 。因此，这种配置在多个代理中无法兼容与移植。

与Kubernetes Ingress 不同，Istio Gateway 通过将 L4-L6 配置与L7配置分离的方式克服了 Ingress 的上述缺点。 Gateway 只用于配置 L4-L6 功能（例如，对外公开的端口、TLS 配置），所有主流的代理均以统一的方式实现了这些功能。 然后，通过在 Gateway 上绑定 VirtualService 的方式，可以使用标准的 Istio 规则来控制进入 Gateway 的 HTTP 和 TCP 流量。

## 前提条件 {#section_6sp_nj2_su3 .section}

-   您已成功创建一个 Kubernetes 集群，且集群版本为1.11.2及以上。参见[创建Kubernetes集群](../../../../intl.zh-CN/用户指南/Kubernetes集群/集群管理/创建Kubernetes集群.md#)。
-   您已连接到Kubernetes集群的Master节点，参见[通过 kubectl 连接 Kubernetes 集群](../../../../intl.zh-CN/用户指南/Kubernetes集群/集群管理/通过 kubectl 连接 Kubernetes 集群.md#)。
-   您已经安装 nc。如果 nc 未被安装，根据操作系统的不同， 使用yum 或 apt-get 等工具进行安装。

## 部署Istio {#section_arq_klx_fhb .section}

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
2.  在 Kubernetes 菜单下，选择**集群** \> **集群**，进入集群列表页面。
3.  选择所需的集群并单击操作列**更多** \> **部署Istio**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/150433/156142972348684_zh-CN.png)

4.  根据如下信息，部署Istio：

    |配置|说明|
    |--|--|
    |集群|部署Istio的目标集群。|
    |命名空间|部署Istio的命名空间。|
    |发布名称|发布的Istio名称。|
    |启用 Prometheus 度量日志收集|是否启用Prometheus 收集度量日志。默认情况下启用。|
    |启用 Grafana 度量展示|是否启用Grafana 展示指标的度量数据。默认情况下启用。|
    |启用 Sidecar 自动注入|是否启用 Sidecar 进行容器的自动注入。默认情况下启用。|
    |启用 Kiali 可视化服务网格|是否启用 Kiali 可视化服务网格。默认情况下不启用。     -   用户名：指定用户名称。默认情况下是admin。
    -   密码：指定密码。默认情况下是admin。
 |
    |链路追踪设置|     -   **启用分布式追踪 Jaeger**：启用该选项，则需要开通阿里云日志服务。在日志服务中将自动创建名称为istio-tracing-\{ClusterID\}的项目。追踪数据会存储到该项目中。
    -   **启用链路追踪**：启用该选项，则需要开通阿里云链路追踪服务。同时需要指定对应的接入点地址。例如，**接入点地址**为http://tracing-analysis-dc-hz.aliyuncs.com/.../api/v1/spans，表示启用该选项后，zipkin客户端根据v1版本的 API的公网（或者内网）接入点地址把采集数据传输到链路跟踪。

**说明：** 如果使用内网接入点，请确保Kubernetes集群与[链路追踪服务](https://tracing-analysis.console.aliyun.com/)在相同区域，保证网络互通。

 |
    |Pilot设置|跟踪采样百分比（0-100）：默认取值为1。|
    |控制Egress流量|     -   直接放行对外访问的地址范围：Istio 服务网格内的服务可以直接对外访问的地址范围。默认情况下为空，使用英文半角逗号分隔。
    -   拦截对外访问的地址范围：拦截直接对外访问的地址范围。默认情况下，已包含集群 Pod CIDR 与 Service CIDR，使用英文半角逗号分隔。
    -   勾选**全部**选项则会拦截所有对外访问的地址。
 **说明：** 

直接放行对外访问的地址范围的优先级高于拦截对外访问的地址范围。

例如，您将同一个IP地址同时配置在直接放行对外访问的地址与拦截对外访问的地址时，您仍可以直接访问此地址，即直接放行对外访问的地址范围生效。

 |

5.  单击**部署Istio**，启动部署。

    在部署页面下方，可实时查看部署进展及状态。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/150433/156142972348685_zh-CN.png)


## TCP Server镜像 {#section_qsv_339_b8b .section}

你可以直接使用已经构建好的镜像文件：registry.cn-hangzhou.aliyuncs.com/wangxining/tcptest:0.1或者安装如下操作构建TCP Server镜像。

1.  从https://github.com/osswangxining/Istio-TCPRoute-Sample地址克隆代码库。
2.  切换到代码目录可以看到一个Dockerfile。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/150433/156142972348687_zh-CN.png)

3.  运行以下命令构建镜像。

    ``` {#codeblock_vtq_x6j_jt9}
    docker build -t *\{镜像仓库地址\}* .
    ```


## 部署应用 {#section_3ik_kdj_qqs .section}

1.  执行如下命令，部署服务。

    ``` {#codeblock_zun_otr_044}
    cd k8s
    kubectl apply -f deployment.yml
    kubectl apply -f service.yml
    ```

    **说明：** 上面的命令会创建1个Service（`tcp-echo`） 与2个Deployment（`tcp-echo-v1`和`tcp-echo-v2`），其中这2个Deployment都包含了标签app: tcp-echo，并且Service（`tcp-echo`）对应到上述2个Deployment：

    ``` {#codeblock_1ml_sol_je4}
    selector:
        app: "tcp-echo"
    ```

2.  执行`kubectl get pods --selector=app=tcp-echo`，确认TCP Server的Pod启动。

    ``` {#codeblock_sbd_o8v_5ao}
    NAME                           READY     STATUS    RESTARTS   AGE
    tcp-echo-v1-7c775f57c9-frprp   2/2       Running   0          1m
    tcp-echo-v2-6bcfd7dcf4-2sqhf   2/2       Running   0          1m
    ```

3.  执行`kubectl get service --selector=app=tcp-echo`，确认TCP Server的服务。

    ``` {#codeblock_m0j_f1s_jir}
    NAME       TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
    tcp-echo   ClusterIP   172.19.46.255   <none>        3333/TCP   17h
    ```


## 定义 gateway {#section_ssr_23v_scj .section}

创建并拷贝内容到gateway.yaml文件中，并执行`kubectl apply -f gateway.yaml`，创建2个Gateway，其中一个Gateway监听31400端口，另一个Gateway监听31401端口。

``` {#codeblock_cjj_s4q_ghw}
apiVersion: networking.istio.io/v1alpha3
kind: Gateway
metadata:
  name: tcp-echo-gateway
spec:
  selector:
    istio: ingressgateway # use istio default controller
  servers:
  - port:
      number: 31400
      name: tcp
      protocol: TCP
    hosts:
    - "*"
---
apiVersion: networking.istio.io/v1alpha3
kind: Gateway
metadata:
  name: tcp-echo-gateway-v2
spec:
  selector:
    istio: ingressgateway # use istio default controller
  servers:
  - port:
      number: 31401
      name: tcp
      protocol: TCP
    hosts:
    - "*"
```

这两个 Gateway共用一个 ingressgateway 服务，该 ingressgateway 使用了 Loadbalancer 方式暴露，可提供对外使用的IP地址。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/150433/156142972448688_zh-CN.png)

## 创建Istio规则 {#section_bgh_b35_dwe .section}

创建并拷贝如下内容到destination-rule-all.yaml和virtualservice.yaml文件中。并分别执行`kubectl apply -f destination-rule-all.yaml`和`kubectl apply -f virtualservice.yaml`命令。

``` {#codeblock_pim_9rg_d3g}
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: tcp-echo
spec:
  hosts:
  - "*"
  gateways:
  - tcp-echo-gateway
  - tcp-echo-gateway-v2
  tcp:
  - match:
    - port: 31400
      gateways:
        - tcp-echo-gateway
    route:
    - destination:
        host: tcp-echo.default.svc.cluster.local
        subset: v1
        port:
          number: 3333
  - match:
    - port: 31401
      gateways:
        - tcp-echo-gateway-v2
    route:
    - destination:
        host: tcp-echo.default.svc.cluster.local
        subset: v2
        port:
          number: 3333
```

## 体验TCP路由功能 {#section_x0s_01s_af1 .section}

1.  查看Ingress Gateway的地址。

    单击左侧导航栏中的服务，在右侧上方选择对应的集群和命名空间，在列表中找到istio-ingressgateway的外部端点地址。

2.  打开终端，运行以下命令。

    ``` {#codeblock_b5e_c1u_2f1}
    nc INGRESSGATEWAY_IP 31400
    ```

3.  输入文字进行如下交互，可以看到该端口的TCP流量转发到了版本v1对应的Pod。

    ``` {#codeblock_lr4_737_fad}
    Welcome, you are connected to node cn-beijing.i-2zeij4aznsu1dvd4mj5c.
    Running on Pod tcp-echo-v1-7c775f57c9-frprp.
    In namespace default.
    With IP address 172.16.2.90.
    Service default.
    hello, app1
    hello, app1
    continue..
    continue..
    ```

4.  查看版本v1的Pod的日志。

    ``` {#codeblock_tp5_dq3_dmr}
    kubectl logs -f tcp-echo-v1-7c775f57c9-frprp -c tcp-echo-container | grep Received
    2018/10/17 07:32:29 6c7f4971-40f1-4f72-54c4-e1462a846189 - Received Raw Data: [104 101 108 108 111 44 32 97 112 112 49 10]
    2018/10/17 07:32:29 6c7f4971-40f1-4f72-54c4-e1462a846189 - Received Data (converted to string): hello, app1
    2018/10/17 07:34:40 6c7f4971-40f1-4f72-54c4-e1462a846189 - Received Raw Data: [99 111 110 116 105 110 117 101 46 46 10]
    2018/10/17 07:34:40 6c7f4971-40f1-4f72-54c4-e1462a846189 - Received Data (converted to string): continue..
    ```

5.  打开另外一个终端，运行以下命令。

    ``` {#codeblock_ccd_zhy_q0i}
    nc INGRESSGATEWAY_IP 31401
    ```

6.  输入文字进行如下交互，可以看到该端口的TCP流量转发到了版本v2对应的Pod。

    ``` {#codeblock_ae9_7cw_bcg}
    Welcome, you are connected to node cn-beijing.i-2zeij4aznsu1dvd4mj5b.
    Running on Pod tcp-echo-v2-6bcfd7dcf4-2sqhf.
    In namespace default.
    With IP address 172.16.1.95.
    Service default.
    hello, app2
    hello, app2
    yes,this is app2
    yes,this is app2
    ```

7.  查看版本v2的POD的日志。

    ``` {#codeblock_3y5_wxc_apo}
    kubectl logs -f tcp-echo-v2-6bcfd7dcf4-2sqhf -c tcp-echo-container | grep Received
    2018/10/17 07:36:29 1a70b9d4-bbc7-471d-4686-89b9234c8f87 - Received Raw Data: [104 101 108 108 111 44 32 97 112 112 50 10]
    2018/10/17 07:36:29 1a70b9d4-bbc7-471d-4686-89b9234c8f87 - Received Data (converted to string): hello, app2
    2018/10/17 07:36:37 1a70b9d4-bbc7-471d-4686-89b9234c8f87 - Received Raw Data: [121 101 115 44 116 104 105 115 32 105 115 32 97 112 112 50 10]
    2018/10/17 07:36:37 1a70b9d4-bbc7-471d-4686-89b9234c8f87 - Received Data (converted to string): yes,this is app2
    ```


