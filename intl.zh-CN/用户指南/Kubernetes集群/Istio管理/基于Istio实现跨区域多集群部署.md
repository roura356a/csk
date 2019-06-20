# 基于Istio实现跨区域多集群部署 {#concept_mpq_kkh_1fb .concept}

容器服务Kubernetes版（简称ACK）提供高性能可伸缩的容器应用管理能力，支持企业级 Kubernetes容器化应用的全生命周期管理。基于Istio，可以轻松地跨多个区域中的Kubernetes集 群实现流量统一管理，例如在阿里云容器服务中部署了多个ACK集群，每个集群位于不同的区域中。 如果最靠近用户的实例发生故障或过载，则流量将无缝地定向到另一个可用实例。

## 前提条件 {#section_ymx_4kh_1fb .section}

-   您已经成功创建一个 Kubernetes 集群，参见[创建Kubernetes集群](intl.zh-CN/用户指南/Kubernetes集群/集群管理/创建Kubernetes集群.md#)。
-   请以主账号登录，或赋予子账号足够的权限，如自定义角色中的cluster-admin，参考[子账号RBAC权限配置指导](intl.zh-CN/用户指南/Kubernetes集群/授权管理/子账号RBAC权限配置指导.md#)。
-   基于Flat网络或者VPN的多集群部署方式，多个Kubernetes集群应位于同一个VPC下网 络互通，或者通过云企业网、高速通道等打通跨VPC网络的访问；同时要求Pod/Service CIDR不能冲突。
-   以下文档使用基于Flat网络或者VPN的多集群Istio部署方式管理服务。

## 背景信息 {#section_f1c_rkh_1fb .section}

-   阿里云容器服务Kubernetes 1.10.4及之后版本支持部署Istio，如果是1.10.4之前的版本，请先升级到1.10.4或之后版本。
-   一个集群中，worker节点数量需要大于等于3个，保证资源充足可用。

## 操作步骤 {#section_spk_skh_1fb .section}

**配置多集群网络**

-   同一VPC下

    同一VPC下，无论集群是在同一交换机下还是多个交换机下，网络互通，多个安全组需要手工设置打通。不同安全组默认情况下不能互相访问，同一VPC下可以通过设置规则使其互通，请参见[添加安全组规则](../../../../intl.zh-CN/安全/安全组/添加安全组规则.md#)。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/395257/156102817248663_zh-CN.png)

-   不同VPC下

    不同VPC下，无论是否在相同地域、可用区，集群之间网络本身不能互通，需要设置[VPC互通](../../../../intl.zh-CN/用户指南/网络连接/VPC互连.md#)（云企业网、VPN网关、高速通道），云企业网的配置更加简单而且可以自动分发学习路由，因此，推荐您使用云企业网。

    **说明：** VPC网络不能冲突，默认建立集群时创建的VPC网络段都是192.168.0.0/16；所以需要手工创建VPC，并指定不同的网段。

    此外，多个安全组需要手工设置打通，可以参考[添加安全组规则](../../../../intl.zh-CN/安全/安全组/添加安全组规则.md#)。

    数据平面上的安全组添加对应的控制平面的网段：

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/395257/156102817248664_zh-CN.png)

    类似地，控制平面上的安全组也同时添加相应的网段。

    此外，网络规划需要提前设置好，保证2个集群内的service/pod CIDR、VPC CIDR都不能重叠。


**通过集群界面部署Istio** 

1.  登录 [容器服务管理控制台](https://cs.console.aliyun.com)。
2.  单击左侧导航栏中的**集群**，进入集群列表页面。
3.  选择所需的集群并单击操作列**更多** \> **部署Istio**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20172/156102817311255_zh-CN.png)

4.  针对多集群部署，根据如下信息进行配置：

    与部署Istio到单个集群的方式一样，可以使用阿里云提供的控制台一键部署，请参见[部署Istio](intl.zh-CN/用户指南/Kubernetes集群/Istio管理/部署Istio.md#)。

    |配置|说明|
    |--|--|
    |启用服务就近访问|在跨区域的多集群部署中是否启用服务就近访问。默认情况下不启用。|
    |多集群同一控制平面模式设置|     -   **不启用**：不启用多集群模式。
    -   **使用Flat网络或者VPN**：基于Flat网络或者VPN方式实现多集群中的网络，多集群间 的Pod可以互通。
    -   **不使用Flat网络或者VPN**：不使用Flat网络或者VPN，多集群之间仅通过网关进行交互。
 |

5.  单击**部署 Istio**，启动部署。

    在部署页面下方，可实时查看部署进展及状态。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/395257/156102817348665_zh-CN.png)

    **预期结果**

    可通过以下操作查看部署是否成功：

    1.  单击左侧导航栏**应用** \> **容器组**，进入容器组页面。
    2.  选择部署Istio的集群及命名空间，可查看到已经部署Istio的相关容器组。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20172/156102817311258_zh-CN.png)


**管理Istio Ingress Gateway。** 

1.  上述部署 Istio之后，包含一个入口网关Ingress Gateway，该网关使用公网IP的负载均衡。通过该网关将集群中应用服务暴露到集群之外。
2.  执行`kubectl get service -n istio-system -l istio=ingressgateway`，获取入口网关的公网IP地址，应用访问可以通过该Gateway进行。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/395257/156102817348745_zh-CN.png)


**将数据平面加入Istio管理** 

1.  在数据平面的Kubernetes集群上执行如下生成 kubeconfig 文件。

    ``` {#codeblock_lcb_m97_tak}
    kubectl create namespace istio-system
    kubectl apply -f http://istio.oss-cn-hangzhou.aliyuncs.com/istio-operator/rbac.yml
    wget http://istio.oss-cn-hangzhou.aliyuncs.com/istio-operator/generate-kubeconfig.sh
    chmod +x generate-kubeconfig.sh
    ./generate-kubeconfig.sh ${CLUSTER_NAME}
    ```

    **说明：** $\{CLUSTER\_NAME\}需要在整个服务网格内保证唯一。

2.  在Istio控制平面集群下执行如下命令。

    ``` {#codeblock_cya_lv8_fun}
    wget http://istio.oss-cn-hangzhou.aliyuncs.com/istio-operator/create-secret.sh
    chmod +x create-secret.sh
    ./create-secret.sh ${CLUSTER_NAME}
    ```

3.  在Istio控制平面集群上，创建并拷贝内容到$\{CLUSTER\_NAME\}.yaml文件中，并执行`kubectl apply -n istio-system -f $\{CLUSTER\_NAME\}.yaml`。

    ``` {#codeblock_11p_yas_qvk}
    apiVersion: istio.alibabacloud.com/v1beta1
    kind: RemoteIstio
    metadata:
      name: ${CLUSTER_NAME} 
      namespace: istio-system
    spec:
      autoInjectionNamespaces:
      - default
      dockerImage:
        region: cn-beijing
      gateways:
        k8singress: {}
      hub: registry.cn-beijing.aliyuncs.com/aliacs-app-catalog
      includeIPRanges: '*'
      proxy: {}
      security: {}
      sidecarInjector:
        enabled: true
        replicaCount: 1
    ```


**预期结果**：

可通过以下操作查看远程集群的Istio部署是否成功：

1.  切换到远程集群，单击左侧导航栏**应用** \> **容器组**，进入容器组页面。
2.  选择部署Istio的集群及命名空间，可查看到已经部署Istio的相关容器组。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/395257/156102817448666_zh-CN.png)


