# 部署高可靠 Ingress Controller {#concept_i55_mtc_5db .concept}

在 Kubernetes 集群中，Ingress 是授权入站连接到达集群服务的规则集合，为您提供七层负载均衡能力，您可以通过 Ingress 配置提供外部可访问的 URL、负载均衡、SSL、基于名称的虚拟主机等。作为集群流量接入层，Ingress 的高可靠性显得尤为重要，本文探讨如何部署一套高性能高可靠的 Ingress 接入层。

## 前提条件 {#section_etk_rtc_5db .section}

-   您已成功创建一个 Kubernetes 集群，参见 [创建 Kubernetes 集群](../../../../intl.zh-CN/用户指南/Kubernetes 集群/集群管理/创建Kubernetes集群.md#)。
-   SSH 连接到 Master 节点，参见 [SSH 访问 Kubernetes 集群](../../../../intl.zh-CN/用户指南/Kubernetes 集群/集群管理/SSH访问Kubernetes集群.md#)。

## 高可靠部署架构 {#section_qvt_qtc_5db .section}

高可靠性首先要解决的就是单点故障问题，一般常用的是采用多副本部署的方式，在 Kubernetes 集群中部署高可靠 Ingress 接入层同样采用多节点部署架构，同时由于 Ingress 作为集群流量接入口，建议采用独占 Ingress 节点的方式，以避免业务应用与 Ingress 服务发生资源争抢。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7250/15330293721101_zh-CN.png)

如上述部署架构图，由多个独占 Ingress 实例组成统一接入层承载集群入口流量，同时可依据后端业务流量水平扩缩容 Ingress 节点。当然如果您前期的集群规模并不大，也可以采用将 Ingress 服务与业务应用混部的方式，但建议进行资源限制和隔离。

## 高可靠Ingress接入层部署说明 {#section_svt_qtc_5db .section}

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7250/15330293721102_zh-CN.png)

-   Ingress SLB：Ingress 接入层前端 SLB 实例
-   Ingress Node：部署 Ingress Pod 的集群节点
-   Ingress Pod：Ingress 服务实例

这三者之间依据标签`node-role.kubernetes.io/ingress=true`进行关联：

1.  Ingress SLB 后端只会挂载打标`node-role.kubernetes.io/ingress=true`的集群 Node。
2.  Ingress Pod 只会被部署到打标`node-role.kubernetes.io/ingress=true`的集群 Node。

## 步骤1 给 Ingress Node 添加标签 {#section_wvt_qtc_5db .section}

1.  登录 [容器服务控制台](https://cs.console.aliyun.com/)。
2.  在 Kubernetes 菜单下，单击左侧导航栏中的 **集群** \> **节点**，进入节点列表页面。
3.  选择所需的集群，查看其下的节点，查看 worker 节点的实例 ID，最后在页面右上角单击 **标签管理**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7250/15330293721103_zh-CN.png)

4.  进入标签管理页面，勾选 worker 节点，单击 **添加标签**，为 worker 节点添加 `node-role.kubernetes.io/ingress：true` 标签，最后单击 **确定**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7250/15330293721105_zh-CN.png)

    在标签管理页面，您可看到 worker 节点已经成功添加该标签。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7250/15330293721106_zh-CN.png)


您也可登录 Master 节点，通过执行 `kubectl label no nodeID node-role.kubernetes.io/ingress=true` 为 worker 节点快速添加标签。

## 步骤2 创建 Ingress 服务 {#section_z4j_hx1_b2b .section}

1.  登录 [容器服务控制台](https://cs.console.aliyun.com/)。
2.  在 Kubernetes 菜单下，单击左侧导航栏中的**应用** \> **部署**，进入部署列表页面。
3.  选择所需的集群和 kube-system 命名空间，找到 nginx-ingress-controller 组件，然后单击右侧的 **删除**，并在弹出框中进行确定。

    集群初始化时默认部署了一个Ingress Controller，请参考 [ingress-nginx](https://github.com/kubernetes/ingress-nginx)。需要先删除该 Ingress Controller 组件，再部署一套新的高可靠 Ingress Controller 接入层。

    **Note:** 默认部署的 Ingress Controller 关联 nginx-ingress-lb 服务，在删除该 deployment 时，别删除关联的服务，后面会采用更新 nginx-ingress-lb 服务的方式进行更新。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7250/15330293721110_zh-CN.png)

4.  然后单击右上角的**使用模板部署**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7250/15330293721111_zh-CN.png)

5.  选择所需的集群，以及 kube-system 命名空间，选择样例模板或自定义，然后单击**创建**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7250/15330293731112_zh-CN.png)

    本例中，通过 DaemonSet 方式将其重新部署到目标 Ingress Node 上，当然您也可以采用 Deployment 配合亲和性方式来部署。

    ```
    # nginx ingress pods
     apiVersion: extensions/v1beta1
     kind: DaemonSet
     metadata:
       name: nginx-ingress-controller
       labels:
         app: ingress-nginx
       namespace: kube-system
     spec:
       template:
         metadata:
           labels:
             app: ingress-nginx
         spec:
           nodeSelector:
             node-role.kubernetes.io/ingress: "true"                          ##通过标签选择器将 pod 部署到对应的节点上
           serviceAccount: admin
           containers:
             - name: nginx-ingress-controller
               image: registry.cn-hangzhou.aliyuncs.com/acs/aliyun-ingress-controller:aliyun-nginx-0.9.0-beta.19.2
               args:
                 - /nginx-ingress-controller
                 - --default-backend-service=$(POD_NAMESPACE)/default-http-backend
                 - --configmap=$(POD_NAMESPACE)/nginx-configuration
                 - --tcp-services-configmap=$(POD_NAMESPACE)/tcp-services
                 - --udp-services-configmap=$(POD_NAMESPACE)/udp-services
                 - --annotations-prefix=nginx.ingress.kubernetes.io
                 - --publish-service=$(POD_NAMESPACE)/nginx-ingress-lb
                 - --v=2
               env:
                 - name: POD_NAME
                   valueFrom:
                     fieldRef:
                       fieldPath: metadata.name
                 - name: POD_NAMESPACE
                   valueFrom:
                     fieldRef:
                       fieldPath: metadata.namespace
               ports:
               - name: http
                 containerPort: 80
               - name: https
                 containerPort: 443
               livenessProbe:
                 failureThreshold: 3
                 httpGet:
                   path: /healthz
                   port: 10254
                   scheme: HTTP
                 initialDelaySeconds: 10
                 periodSeconds: 10
                 successThreshold: 1
                 timeoutSeconds: 1
               readinessProbe:
                 failureThreshold: 3
                 httpGet:
                   path: /healthz
                   port: 10254
                   scheme: HTTP
                 periodSeconds: 10
                 successThreshold: 1
                 timeoutSeconds: 1
    ```

6.  部署成功后，单击 **Kubernetes 控制台**，进入 Kubernetes Dashboard。选择 Kube-system 命名空间，单击左侧导航栏中的**守护进程集**，查看 nginx-ingress-controller。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7250/15330293731113_zh-CN.png)

7.  单击左侧导航栏中的**容器组**，您可查看 nginx-ingress-controller 的两个容器组。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7250/15330293731114_zh-CN.png)


## 步骤3 更新 Ingress SLB 服务 {#section_bxt_qtc_5db .section}

1.  登录 [容器服务控制台](https://cs.console.aliyun.com/)。
2.  在 Kubernetes 菜单下，单击左侧导航栏中的**应用** \> **服务**，进入服务列表页面。
3.  选择所需的集群和 Kube-system 命名空间，找到 nginx-ingress-lb 服务，单击右侧的**更新**。

    集群初始化时默认部署了一个 Ingress LoadBalancer Service，具体部署说明请参考 [ingress-nginx](https://github.com/kubernetes/ingress-nginx)，您需要更新 Ingress LoadBalancer Service，以自动识别挂载打标的 Ingress Node。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7250/15330293731115_zh-CN.png)

4.  在弹出的对话框中，添加一条注释。`service.beta.kubernetes.io/alicloud-loadbalancer-backend-label: "node-role.kubernetes.io/ingress=true"`，然后单击**确定**，完成更新。

    您也可登录集群 Master 节点，执行 `kubectl apply -f https://acs-k8s-ingress.oss-cn-hangzhou.aliyuncs.com/nginx-ingress-slb-service.yml`命令更新 nginx-ingress-lb 服务。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7250/15330293731116_zh-CN.png)


至此，若干 Ingress 实例的高可靠接入层部署完成，可有效应对单点故障和业务流量的挑战，您还可通过打标的方式快速扩容 Ingress 接入层。

