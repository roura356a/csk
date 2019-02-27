# 升级安装Kubernetes集群的metrics-server组件 {#concept_cfx_ryj_wgb .concept}

本文档介绍如何在不升级Kubernetes集群的情况下升级安装metrics-server组件。

## 前提条件 {#section_y3f_r2f_xgb .section}

-   您已成功创建一个Kubernetes集群。有关如何创建Kubernetes集群，参见[创建Kubernetes集群](cn.zh-CN/用户指南/Kubernetes集群/集群管理/创建Kubernetes集群.md#)。
-   确保Kubernetes集群是1.12以前的版本。

## 操作步骤 {#section_pzr_r2f_xgb .section}

Kubernetes集群组件的升级过程分为以下三个部分：切换数据采集组件、切换监控数据链路、变更组件兼容适配。

完成以下步骤将数据采集组件从Heapster切换到metrics-server。

-   切换数据采集组件。

    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
    2.  在 Kubernetes 菜单下，单击左侧导航栏中的**应用** \> **无状态**，进入无状态页面。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/129974/155126484739478_zh-CN.png)

    3.  单击右上角的**使用模板创建**，进入使用模板创建页面。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/129974/155126484739496_zh-CN.png)

    4.  选择需要升级组件的**集群**，并选择**kube-system**作为**命名空间**。
    5.  **示例模板**选择**自定义**，并将以下内容复制到**模板**中，单击**创建**。

        ```
        apiVersion: v1
        kind: ServiceAccount
        metadata:
          name: admin
          namespace: kube-system
        ---
        apiVersion: rbac.authorization.k8s.io/v1beta1
        kind: ClusterRoleBinding
        metadata:
          name: admin
        roleRef:
          apiGroup: rbac.authorization.k8s.io
          kind: ClusterRole
          name: cluster-admin
        subjects:
        - kind: ServiceAccount
          name: admin
          namespace: kube-system
        ---
        apiVersion: v1
        kind: Service
        metadata:
          labels:
            task: monitoring
            # For use as a Cluster add-on (https://github.com/kubernetes/kubernetes/tree/master/cluster/addons)
            # If you are NOT using this as an addon, you should comment out this line.
            kubernetes.io/cluster-service: 'true'
            kubernetes.io/name: metrics-server
          name: heapster
          namespace: kube-system
        spec:
          ports:
          - port: 80
            targetPort: 8082
          selector:
            k8s-app: metrics-server
        ---
        apiVersion: v1
        kind: Service
        metadata:
          name: metrics-server
          namespace: kube-system
          labels:
            kubernetes.io/name: metrics-server
        spec:
          selector:
            k8s-app: metrics-server
          ports:
          - port: 443
            protocol: TCP
            targetPort: 443
        ---
        apiVersion: apiregistration.k8s.io/v1beta1
        kind: APIService
        metadata:
          name: v1beta1.metrics.k8s.io
        spec:
          service:
            name: metrics-server
            namespace: kube-system
          group: metrics.k8s.io
          version: v1beta1
          insecureSkipTLSVerify: true
          groupPriorityMinimum: 100
          versionPriority: 100
        ---
        apiVersion: extensions/v1beta1
        kind: Deployment
        metadata:
          name: metrics-server
          namespace: kube-system
          labels:
            k8s-app: metrics-server
        spec:
          selector:
            matchLabels:
              k8s-app: metrics-server
          template:
            metadata:
              name: metrics-server
              labels:
                k8s-app: metrics-server
            spec:
              serviceAccountName: admin
              containers:
              - name: metrics-server
                image: registry.##REGION##.aliyuncs.com/acs/metrics-server:v0.2.1-9dd9511-aliyun
                imagePullPolicy: Always
                command:
                - /metrics-server
                - '--source=kubernetes:https://kubernetes.default'
                - '--sink=socket:tcp://monitor.csk.##REGION##.aliyuncs.com:8093?clusterId=##CLUSTER_ID##&public=true'
        ```

    **说明：** 您需要将`##REGION##`与`##CLUSTER_ID##`替换为所选集群的地域名称（例如，华东1：cn-hangzhou）与集群ID。

-   切换监控数据链路
    1.  单击左侧导航栏中的**集群** \> **节点**，进入节点列表页面。
    2.  选择目标**集群**。
    3.  找到集群的三个master节点。单击master节点的实例ID，进入实例详情页面（本文档以master-01为例）。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/129974/155126484739497_zh-CN.png)

    4.  单击**远程连接**。进入 ECS 实例远程连接界面，根据页面指导，输入远程连接密码并单击**确定**。登录成功后，输入以下命令：

        ```
        sed -i 's/--horizontal-pod-autoscaler-use-rest-clients=false/--horizontal-pod-autoscaler-use-rest-clients=true/' /etc/kubernetes/manifests/kube-controller-manager.yaml
        
        ```

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/129974/155126484739498_zh-CN.png)

    5.  重复步骤[\#](#)-[d](#)，在master-02和master-03节点上执行该命令。

        执行完毕后，kube-controller-manager组件会被kubelet自动拉起更新。

-   组件兼容适配变更
    1.  单击左侧导航栏中的**路由与负载均衡** \> **服务**，进入服务页面。 
    2.  选择目标**集群**和命名空间**kube-system**。单击服务**heapster**右侧的**查看YAML**。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/129974/155126484739499_zh-CN.png)

    3.  在弹出的对话框中，修改selector中k8s-app的值为metrics-server。单击**更新**，完成修改。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/129974/155126484739500_zh-CN.png)

    4.  单击左侧导航栏中的**应用** \> **无状态**，进入无状态页面。
    5.  选择目标**集群**和**命名空间**kube-system。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/129974/155126484739506_zh-CN.png)

    6.  选择Heapster相关组件（heapster和monitoring-influxdb），单击**更多** \> **删除**，在弹出的对话框中，单击**确定**，完成链路切换。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/129974/155126484739501_zh-CN.png)

        **说明：** 删除monitoring-influxdb 组件时，在弹出的删除tiller-deploy提示框中，勾选**移除关联的服务（Server）tiller-deploy**，单击**确定**。

    7.  验证链路切换状态。

        等待3分钟左右，数据链路会完成初始化。

         单击左侧导航栏中的**应用** \> **容器组**。在容器组页面。如果CPU（核）与内存（字节）显示正常，则表示链路切换成功。

        **说明：** 所有组件的CPU和内存值均为0则表示异常。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/129974/155126484739502_zh-CN.png)


