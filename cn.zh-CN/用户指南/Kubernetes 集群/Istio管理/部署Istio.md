# 部署Istio {#concept_mpq_kkh_1fb .concept}

为解决微服务的分布式应用架构在运维、调试、和安全管理等维度存在的问题，可通过部署Istio创建微服务网络，并提供负载均衡、服务间认证以及监控等能力，同时Istio不需要修改服务即可实现以上功能。

## 前提条件 {#section_ymx_4kh_1fb .section}

-   您已经成功创建一个 Kubernetes 集群，参见[创建Kubernetes集群](intl.zh-CN/用户指南/Kubernetes 集群/集群管理/创建Kubernetes集群.md#)。
-   请以主账号登录，或赋予子账号足够的权限，如自定义角色中的cluster-admin，参考[子账号Kubernetes应用权限配置指导](intl.zh-CN/用户指南/Kubernetes 集群/授权管理/子账号Kubernetes应用权限配置指导.md#)。

## 背景信息 {#section_f1c_rkh_1fb .section}

-   阿里云容器服务Kubernetes 1.10.4及之后版本支持部署Istio，如果是1.10.4之前的版本，请先升级到1.10.4或之后版本。

-   一个集群中，worker节点数量需要大于等于3个，保证资源充足可用。


## 操作步骤 {#section_spk_skh_1fb .section}

**通过集群界面部署Istio**

1.  登录 [容器服务管理控制台](https://cs.console.aliyun.com)。
2.  单击左侧导航栏中的**集群**，进入集群列表页面。
3.  选择所需的集群并单击操作列**更多** \> **部署Istio**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20172/154174941211255_zh-CN.png)

4.  根据如下信息，部署Istio：

    |配置|说明|
    |--|--|
    |集群|部署Istio的目标集群。|
    |命名空间|部署Istio的命名空间。|
    |发布名称|发布的Istio名称。|
    |启用 Prometheus 度量日志收集|是否启用Prometheus 收集度量日志。默认情况下启用。|
    |启用 Grafana 度量展示|是否启用Grafana 展示指标的度量数据。默认情况下启用。|
    |启用 Sidecar 自动注入|是否启用 Sidecar 进行容器的自动注入。默认情况下启用。|
    |启用 Kiali 可视化服务网格|是否启用 Kiali 可视化服务网格。默认情况下不启用。    -   用户名：指定用户名称。默认情况下是admin。
    -   密码：指定密码。默认情况下是admin。
|
    |启用阿里云日志服务 SLS 及 Jaeger| 是否启用阿里云的日志服务SLS 及 Jaeger。默认情况下不启用。

 服务入口地址（Endpoint）：根据配置的日志服务所在region选择对应的地址。具体请参见[服务入口](../../../../intl.zh-CN/API 参考/服务入口.md#section_nv4_h2r_12b)

 项目名称（Project）：采集日志所在的项目名称。

 日志库名称（Logstore）：采集日志所在的日志库名称。

 AccessKeyID：访问日志服务时，所使用的访问秘钥ID。

**说明：** 需要选择有权限访问日志服务的AccessKeyID。

 AccessKeySecret：访问日志服务时，所使用的访问秘钥Secret。

 |
    |Pilot设置|跟踪采样百分比（0-100）：默认取值为1。|
    |控制Egress流量|     -   直接放行对外访问的地址范围：Istio 服务网格内的服务可以直接对外访问的地址范围。默认情况下为空，使用英文半角逗号分隔。
    -   拦截对外访问的地址范围：拦截直接对外访问的地址范围。默认情况下，已包含集群 Pod CIDR 与 Service CIDR，使用英文半角逗号分隔。
 **说明：** 

直接放行对外访问的地址范围的优先级高于拦截对外访问的地址范围。

例如您将同一个IP地址同时配置在直接放行对外访问的地址与拦截对外访问的地址时，您仍可以直接访问此地址，即直接放行对外访问的地址范围生效。

 |

5.  单击**部署 Istio**，启动部署。

    在部署页面下方，可实时查看部署进展及状态。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20172/154174941211256_zh-CN.png)


**预期结果**

可通过以下方法查看部署是否成功：

-   在部署 Istio页面下方，**部署 Istio**变为**已部署**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20172/154174941211257_zh-CN.png)

-   -   单击左侧导航栏**应用** \> **容器组**，进入容器组页面。
-   选择部署Istio的集群及命名空间，可查看到已经部署Istio的相关容器组。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20172/154174941211258_zh-CN.png)

-   -   单击左侧导航栏**应用** \> **服务**，进入服务列表页面。
-   选择部署Istio的集群及命名空间，可查看到已经部署Istio相关服务所提供的访问地址。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20172/154174941211259_zh-CN.png)


**通过应用目录部署Istio**

1.  登录 [容器服务管理控制台](https://cs.console.aliyun.com)。
2.  单击左侧导航栏中的**市场** \> **应用目录**，进入应用目录页面。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20172/154174941211260_zh-CN.png)

3.  单击**ack-istio**，进入应用目录 - ack-istio页面。
4.  单击参数页签，进行参数配置。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20172/154174941212560_zh-CN.png)

    **说明：** 

    -   通用参数的含义、取值及默认情况，可参考说明页签**Configuration**字段。
    -   也可以针对特定参数进行定制化配置，例如：是否启用grafana、prometheus、tracing、weave-scope以及kiali等，可参考：

        ```
        #
        # addons configuration
        #
        grafana:
        enabled: true
        replicaCount: 1
        image: istio-grafana
        service:
        name: http
        type: ClusterIP
        externalPort: 3000
        internalPort: 3000
        ....
        prometheus:
        enabled: true
        replicaCount: 1
        image:
        repository: registry.cn-hangzhou.aliyuncs.com/aliacs-app-catalog/istio-prometheus
        tag: latest
        ....
        tracing:
        enabled: true
        jaeger:
        enabled: true
        ....
        weave-scope:
        enabled: true
        global:
        # global.image: the image that will be used for this release
        image:
        repository: weaveworks/scope
        tag: "1.9.0"
        # global.image.pullPolicy: must be Always, IfNotPresent, or Never
        pullPolicy: "IfNotPresent"
        ....
        kiali:
        enabled: true
        replicaCount: 1
        image:
        repository: registry.cn-hangzhou.aliyuncs.com/aliacs-app-catalog/istio-kiali
        tag: dev
        
        
        ```

5.  在右侧创建区域，填写以下基本信息：

    |配置|说明|
    |--|--|
    |集群|部署Istio的目标集群。|
    |命名空间|部署Istio的命名空间。默认情况下为 default。|
    |发布名称|发布的Istio名称。|

6.  单击**创建**，启动部署。

**预期结果**

-   -   单击左侧导航栏**应用** \> **容器组**，进入容器组页面。
-   选择部署Istio的集群及命名空间，可查看到已经部署Istio的相关容器组。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20172/154174941211258_zh-CN.png)

-   -   单击左侧导航栏**应用** \> **服务**，进入服务列表页面。
-   选择部署Istio的集群及命名空间，可查看到已经部署Istio相关服务所提供的访问地址。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20172/154174941211259_zh-CN.png)


