---
keyword: [ASP.NET应用, Windows应用]
---

# 创建Windows应用

本例演示如何通过一个编排模板创建ASP.NET应用，包含一个Deployment和Service，后端Deployment会创建Pod资源对象，Service会绑定到后端Pod上，形成一个完整的ASP.NET应用。

-   创建一个Windows节点。具体操作，请参见[创建Windows节点池](/intl.zh-CN/Kubernetes集群用户指南/Windows容器/创建Windows节点池.md)。
-   在容器服务Kubernetes模板编排中，您需要自己定义一个应用运行所需的资源对象，通过标签选择器等机制，将资源对象组合成一个完整的应用。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**工作负载** \> **无状态**。

5.  在无状态页面单击右上角的**使用模板创建**。

6.  对模板进行相关配置，完成配置后单击**创建**。

    -   **集群**：选择目标集群。资源对象将部署在该集群内。
    -   **命名空间**：选择资源对象所属的命名空间，默认是Default。除了节点、持久化存储卷等底层计算资源以外，大多数资源对象需要作用于命名空间。
    -   **示例模板**：阿里云容器服务提供了多种资源类型的Kubernetes YAML示例模板，让您快速部署资源对象。您可以根据Kubernetes YAML编排的格式要求自主编写，来描述您想定义的资源类型。
    -   **添加部署**：您可通过此功能快速定义一个YAML模板。
    -   **使用已有模板**：您可将已有编排模板导入到模板配置页面。
    ![配置模板信息](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1206659951/p41759.png)

    下面是一个ASP.NET应用的示例编排，基于容器服务内置的编排模板。通过该编排模板，即可快速创建一个属于ASP.NET应用的Deployment。

    **说明：** 容器服务支持Kubernetes Yaml编排，支持通过`---`符号将资源对象分隔，从而通过一个模板创建多个资源对象。

    ```
    apiVersion: v1
    kind: Service
    metadata:
      name: aspnet-svc
    spec:
      ports:
        - port: 80
          protocol: TCP
          targetPort: 80
      selector:
        app: aspnet
      type: LoadBalancer
    ---
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: aspnet
    spec:
      selector:
        matchLabels:
          app: aspnet
      template:
        metadata:
          labels:
            app: aspnet
        spec:
          containers:
            - image: 'mcr.microsoft.com/dotnet/samples:aspnetapp'
              name: aspnet
          nodeSelectors:
            kubernetes.io/os: "windows"
          tolerations:
          - key: "os"
            value: "windows"
    ```

    **说明：** 镜像`mcr.microsoft.com/dotnet/samples:aspnetapp`为微软官方提供的ASP.NET示例镜像。更多信息，请参见[.NET Samples](https://hub.docker.com/_/microsoft-dotnet-samples)。

7.  单击**创建**后，会提示部署状态信息。成功后，返回**无状态**页面可以查看新创建的ASP.NET应用。

8.  访问ASP.NET应用。

    1.  在控制台左侧导航栏中，单击**集群**。

    2.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

    3.  在集群管理页左侧导航栏中，选择**服务与路由** \> **服务**。

    4.  在**服务**页面您可以看到**aspnet-svc**服务已成功部署，并暴露了外部入口。

    5.  单击**aspnet-svc**服务的外部端点，可以访问到ASP.NET应用。


