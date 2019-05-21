# 基于虚拟节点部署 Ingress 应用 {#concept_265366 .concept}

本文主要介绍在虚拟节点上部署Ingress应用，使得集群无需创建新节点即可为该应用扩充**无限**容量，满足业务高峰低谷的弹性需求。

## 前提条件 {#section_3h9_anv_kch .section}

您已经部署了一个虚拟节点。请参见[ZH-CN\_TP\_218258\_V1.md\#](intl.zh-CN/用户指南/Kubernetes集群/通过Virtual Node Addon部署虚拟节点.md#)。

您已经给namespace vk打上virtual-node-affinity-injection: enabled标签。请参见[通过配置namespace标签的方式创建Pod](intl.zh-CN/用户指南/Kubernetes集群/通过Virtual Node Addon部署虚拟节点.md#li_8tb_qv1_yws)。

## 操作步骤 {#section_qtq_7pj_ul2 .section}

1.  登录 [容器服务管理控制台](https://cs.console.aliyun.com)。
2.  在 Kubernetes 菜单下，单击左侧导航栏中的**应用** \> **无状态**，单击右上角的**使用模板创建**。
3.  选择所需的集群和命名空间vk，选择样例模板或自定义，然后单击**创建**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/218949/155840227347324_zh-CN.png)

    您可以使用如下 yaml 示例模板创建Ingress。

    ``` {#codeblock_33x_yof_xw5}
    apiVersion: extensions/v1beta1
    kind: Deployment
    metadata:
      name: coffee
    spec:
      replicas: 2
      selector:
        matchLabels:
          app: coffee
      template:
        metadata:
          labels:
            app: coffee
        spec:
          containers:
          - name: coffee
            image: nginxdemos/hello:plain-text
            ports:
            - containerPort: 80
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: coffee-svc
    spec:
      ports:
      - port: 80
        targetPort: 80
        protocol: TCP
      selector:
        app: coffee
      clusterIP: None
    ---
    apiVersion: extensions/v1beta1
    kind: Deployment
    metadata:
      name: tea
    spec:
      replicas: 3
      selector:
        matchLabels:
          app: tea
      template:
        metadata:
          labels:
            app: tea
        spec:
          containers:
          - name: tea
            image: nginxdemos/hello:plain-text
            ports:
            - containerPort: 80
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: tea-svc
      labels:
    spec:
      ports:
      - port: 80
        targetPort: 80
        protocol: TCP
      selector:
        app: tea
      clusterIP: None
    ---
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: cafe-ingress
    spec:
      rules:
      - host: cafe.example.com
        http:
          paths:
          - path: /tea
            backend:
              serviceName: tea-svc
              servicePort: 80
          - path: /coffee
            backend:
              serviceName: coffee-svc
              servicePort: 80
    ```


**预期结果**

-   在 Kubernetes 菜单下，单击左侧导航栏的**应用** \> **无状态**，选择目标集群和命名空间，可以看到刚刚创建的coffee和tea。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/218949/155840227347325_zh-CN.png)

-   在 Kubernetes 菜单下，单击左侧导航栏的**应用** \> **容器组**，选择目标集群和命名空间，可以看到 Pod 都运行在 Virtual-Kubelet 节点上。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/218949/155840227347329_zh-CN.png)

-   在 Kubernetes 菜单下，单击左侧导航栏的**路由与负载均衡** \> **路由**，选择目标集群和命名空间，可以看到刚刚创建的路由。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/218949/155840227347330_zh-CN.png)

-   您可以执行如下命令，确保可以访问 Ingress 应用。

    ``` {#codeblock_4x2_07g_nuw}
    kubectl -n vk get ing
    curl -H "Host:cafe.example.com" <EXTERNAL_IP>/tea
    curl -H "Host:cafe.example.com" <EXTERNAL_IP>/coffee
    ```

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/218949/155840227347334_zh-CN.png)


