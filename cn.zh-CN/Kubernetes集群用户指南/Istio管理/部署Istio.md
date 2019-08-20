# 部署Istio {#task_1443670 .task}

为解决微服务的分布式应用架构在运维、调试、和安全管理等维度存在的问题，可通过部署Istio创建微服务网络，并提供负载均衡、服务间认证以及监控等能力，同时Istio不需要修改服务即可实现以上功能。

-   您已经成功创建一个 Kubernetes 集群，参见[ZH-CN\_TP\_16639.md\#](intl.zh-CN/Kubernetes集群用户指南/集群管理/创建集群/创建Kubernetes 集群.md#)。
-   请以主账号登录，或赋予子账号足够的权限，如自定义角色中的cluster-admin，参考[ZH-CN\_TP\_17456.md\#](intl.zh-CN/Kubernetes集群用户指南/授权管理/子账号RBAC权限配置指导.md#)。

-   阿里云容器服务Kubernetes 1.10.4及之后版本支持部署Istio，如果是1.10.4之前的版本，请先升级到1.10.4或之后版本。
-   一个集群中，worker节点数量需要大于等于3个，保证资源充足可用。

1.  部署istio 。 

    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
    2.  单击左侧导航栏中的**服务网格**，进入Istio管理页面。
    3.  选择所需的集群并设置相应的配置参数。
    4.  根据如下信息，部署Istio。 

        |配置|说明|
        |--|--|
        |集群|部署Istio的目标集群。|
        |命名空间|部署Istio的命名空间。|
        |发布名称|发布的Istio名称。|
        |启用 Prometheus 度量日志收集|是否启用Prometheus 收集度量日志。默认情况下启用。|
        |启用 Grafana 度量展示|是否启用Grafana 展示指标的度量数据。默认情况下启用。|
        |启用 Sidecar 自动注入|是否启用 Sidecar 进行容器的自动注入。默认情况下启用。|
        |启用 Kiali 可视化服务网格|是否启用 Kiali 可视化服务网格。默认情况下不启用。         -   用户名：指定用户名称。默认情况下是admin。
        -   密码：指定密码。默认情况下是admin。
 |
        |链路追踪设置|**启用链路追踪**：启用该选项，则需要开通阿里云链路追踪服务。同时需要指定对应的接入点地址。例如，**接入点地址**为http://tracing-analysis-dc-hz.aliyuncs.com/.../api/v1/spans，表示启用该选项后，zipkin客户端根据v1版本的 API的公网（或者内网）接入点地址把采集数据传输到链路跟踪。 **说明：** 如果使用内网接入点，请确保Kubernetes集群与[链路追踪服务](https://tracing-analysis.console.aliyun.com/)在相同区域，保证网络互通。

 |
        |Pilot设置|跟踪采样百分比（0-100）：默认取值为1。|
        |控制Egress流量|         -   直接放行对外访问的地址范围：Istio 服务网格内的服务可以直接对外访问的地址范围。默认情况下为空，使用英文半角逗号分隔。
        -   拦截对外访问的地址范围：拦截直接对外访问的地址范围。默认情况下，已包含集群 Pod CIDR 与 Service CIDR，使用英文半角逗号分隔。
        -   勾选**全部**选项则会拦截所有对外访问的地址。
 **说明：** 

直接放行对外访问的地址范围的优先级高于拦截对外访问的地址范围。

例如您将同一个IP地址同时配置在直接放行对外访问的地址与拦截对外访问的地址时，您仍可以直接访问此地址，即直接放行对外访问的地址范围生效。

 |

    5.  单击**部署 Istio**，启动部署。 在部署页面下方，可实时查看部署进展及状态。

        ![查看部署结果](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20172/156629542811256_zh-CN.png)

    可通过以下方法查看部署是否成功：

    -   在部署 Istio页面下方，**部署 Istio**变为**已部署**。

        ![部署完成](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20172/156629542811257_zh-CN.png)

    -   -   单击左侧导航栏**应用** \> **容器组**，进入容器组页面。
-   选择部署Istio的集群及命名空间，可查看到已经部署Istio的相关容器组。

    ![容器组查看](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20172/156629542911258_zh-CN.png)

    -   -   单击左侧导航栏**路由与负载均衡** \> **服务**，进入服务（Service）页面。
-   选择部署Istio的集群及命名空间，可查看到已经部署Istio相关服务所提供的访问地址。

    ![部署istio的访问地址](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20172/156629542911259_zh-CN.png)

2.  管理Istio Ingress Gateway。 上述部署 Istio之后，Istio 1.1.4之后默认会创建一个Ingress Gateway。对于已有旧版本，建议升级到当前最新版本。如果需要对Ingress Gateway的配置进行调整，可以按照如下步骤进行更新。
    1.  在左侧导航栏选择**应用** \> **发布**，单击**Helm**页签。
    2.  单击**Istio**右侧的更新，在弹出的更新发布页面中，可以看到如下示例参数定义。 

        ``` {#codeblock_dy3_ig5_3rs}
        gateways:
          enabled: true
          ingress:
            - enabled: true
              gatewayName: ingressgateway
              maxReplicas: 5
              minReplicas: 1
              ports:
                - name: status-port
                  port: 15020
                  targetPort: 15020
                - name: http2
                  nodePort: 31380
                  port: 80
                  targetPort: 80
                - name: https
                  nodePort: 31390
                  port: 443
                  targetPort: 0
                - name: tls
                  port: 15443
                  targetPort: 15443
              replicaCount: 1
              serviceType: LoadBalancer
          k8singress: {}
        ```

    3.  修改相应的参数后，单击**更新**。 

        **说明：** 

        -   replicaCount：指定副本数。
        -   ports：设置启用的端口。
        -   serviceType：指定服务类型，可以设置为LoadBalancer、ClusterIP或者NodePort。
        -   serviceAnnotations：当服务类型为设置LoadBalancer时，通过设置serviceAnnotations参数指定使用内网还是公网负载均衡、使用已有的SLB等；其他参数具体参见[通过负载均衡访问服务](../../../../intl.zh-CN/Serverless Kubernetes集群用户指南/负载均衡管理/通过负载均衡访问服务.md#)。

