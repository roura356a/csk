---
keyword: [部署Ingress, Ingress应用]
---

# 部署Ingress应用

本文主要介绍在虚拟节点上部署Ingress应用，使得集群无需创建新节点即可为该应用扩充无限容量，满足业务高峰低谷的弹性需求。

## 前提条件

-   您已经部署了一个虚拟节点。具体操作，请参见[通过部署ACK虚拟节点组件创建ECI Pod](/cn.zh-CN/Kubernetes集群用户指南/弹性容器实例ECI/通过部署ACK虚拟节点组件创建ECI Pod.md)。
-   您已经给命名空间vk打上virtual-node-affinity-injection: enabled标签。具体操作，请参见[通过配置namespace标签的方式创建Pod](/cn.zh-CN/Kubernetes集群用户指南/弹性容器实例ECI/通过部署ACK虚拟节点组件创建ECI Pod.md)。

## 操作步骤

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**工作负载** \> **无状态**。

5.  单击右上角的**使用模板创建**。

6.  选择样例模板或自定义，然后单击**创建**。

    ![样例模板](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6106659951/p47324.png)

    您可以使用如下YAML示例模板创建Ingress。

    ```
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


## 预期结果

-   在集群管理页左侧导航栏中，选择**工作负载** \> **无状态**，可以看到刚刚创建的coffee和tea。
-   在集群管理页左侧导航栏中，选择**工作负载** \> **容器组**，可以看到Pod都运行在Virtual-Kubelet节点上。
-   在集群管理页左侧导航栏中，选择**服务与路由** \> **路由**，可以看到刚刚创建的路由。
-   您可以执行如下命令，确保可以访问Ingress应用。

    ```
    kubectl  get ing
    ```

    预期输出：

    ```
    NAME           HOSTS              ADDRESS          PORTS   AGE
    cafe-ingress   cafe.example.com   114.55.252.185   80      6m18s
    ```

    执行以下命令，验证访问Ingress应用的`"Host:cafe.example.com" <EXTERNAL_IP>/tea`地址。

    ```
    curl -H "Host:cafe.example.com" <EXTERNAL_IP>/tea
    ```

    预期输出：

    ```
    Server address: 192.168.xx.xx:80
    Server name: tea-658d56f6cc-cxxxx
    Date: 25/Sep/2020:12:36:50 +0000
    URI: /tea
    Request ID: b01d5bab9ae07abb8bc385377193xxxx
    ```

    执行以下命令，验证访问Ingress应用的`"Host:cafe.example.com" <EXTERNAL_IP>/coffee`地址。

    ```
    curl -H "Host:cafe.example.com" <EXTERNAL_IP>/coffee
    ```

    预期输出：

    ```
    Server address: 192.168.xx.xx:80
    Server name: coffee-8c8ff9b4f-hxxxx
    Date: 25/Sep/2020:12:36:47 +0000
    URI: /coffee
    Request ID: 722fe41a65a7fb552613c56e0a9axxxx
    ```


