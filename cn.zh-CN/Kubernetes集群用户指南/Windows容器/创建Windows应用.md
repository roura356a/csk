---
keyword: [Web应用, Windows应用]
---

# 创建Windows应用

本例演示如何通过一个编排模板创建Web应用，包含一个Deployment和Service，后端Deployment会创建Pod资源对象，Service会绑定到后端Pod上，形成一个完整的Web应用。

-   已创建Windows节点。具体操作，请参见[创建Windows节点池](/cn.zh-CN/Kubernetes集群用户指南/Windows容器/创建Windows节点池.md)。
-   在容器服务Kubernetes模板编排中，您需要自己定义一个应用运行所需的资源对象，通过标签选择器等机制，将资源对象组合成一个完整的应用。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**工作负载** \> **无状态**。

5.  在无状态页面单击右上角的**使用YAML创建资源**。

6.  对模板进行相关配置，完成配置后单击**创建**。

    -   **命名空间**：选择资源对象所属的命名空间，默认是Default。除了节点、持久化存储卷等底层计算资源以外，大多数资源对象需要作用于命名空间。
    -   **示例模板**：阿里云容器服务提供了多种资源类型的Kubernetes YAML示例模板，让您快速部署资源对象。您可以根据Kubernetes YAML编排的格式要求自主编写，来描述您想定义的资源类型。
    -   **添加工作负载**：您可通过此功能快速定义一个YAML模板。
    -   **使用已有模板**：您可将已有编排模板导入到模板配置页面。
    -   **保存模板**：您可以保存设置好的编排模板。
    ![配置模板信息](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1206659951/p41759.png)

    下面是一个Web应用的示例编排，基于容器服务内置的编排模板。通过该编排模板，即可快速创建一个属于Web应用的Deployment。

    **说明：** 容器服务支持Kubernetes YAML编排，支持通过`---`符号将资源对象分隔，从而通过一个模板创建多个资源对象。

    ```
    apiVersion: v1
    kind: Service
    metadata:
      labels:
        app: web-windows
      name: web-windows
    spec:
      type: LoadBalancer
      ports:
        - port: 80
          protocol: TCP
          targetPort: 80
      selector:
        app: web-windows
    ---
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      labels:
        app: web-windows
      name: web-windows
    spec:
      selector:
        matchLabels:
          app: web-windows
      template:
        metadata:
          labels:
            app: web-windows
        spec:
          restartPolicy: Always
          terminationGracePeriodSeconds: 30
          tolerations:
          - key: os
            value: windows
          affinity:
            nodeAffinity:
              requiredDuringSchedulingIgnoredDuringExecution:
                nodeSelectorTerms:
                - matchExpressions:
                  - key: kubernetes.io/os
                    operator: In
                    values:
                    - windows
          containers:
         # 根据不同集群的地域，您需要修改以下镜像地址中的地域<cn-hangzhou>信息。
            - image: registry-vpc.cn-hangzhou.aliyuncs.com/acs/sample-web-windows:v1.0.1
              name: windows
              ports:
              - containerPort: 80
                protocol: TCP
    ```

    **说明：**

    -   镜像`registry-vpc.{region}.aliyuncs.com/acs/sample-web-windows`是阿里云容器服务提供的Windows Web容器示例镜像（基于[mcr.microsoft.com/windows/nanoserver](https://hub.docker.com/_/microsoft-windows-nanoserver)）。
    -   也可使用微软提供的ASP.NET示例镜像：`mcr.microsoft.com/dotnet/samples:aspnetapp`。更多信息，请参见[.NET Samples](https://hub.docker.com/_/microsoft-dotnet-samples)。
7.  单击**创建**后，会提示部署状态信息。成功后，返回**无状态**页面可以查看新创建的Web应用。

8.  访问Web应用。

    1.  在控制台左侧导航栏中，单击**集群**。

    2.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

    3.  在集群管理页左侧导航栏中，选择**网络** \> **服务**。

    4.  在**服务**页面您可以看到**web-windows**服务已成功部署，并暴露了外部入口。

    5.  单击**web-windows**服务的外部端点，可以访问到Web应用。

    6.  修改访问URL为`name.html`，可以看到部署应用的容器的`hostname`。


