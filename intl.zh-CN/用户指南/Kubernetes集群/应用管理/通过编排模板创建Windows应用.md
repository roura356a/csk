# 通过编排模板创建Windows应用 {#task_vwv_xwl_vdb .task}

在容器服务 Kubernetes 模板编排中，您需要自己定义一个应用运行所需的资源对象，通过标签选择器等机制，将资源对象组合成一个完整的应用。

创建一个 Windows Kubernetes 集群，参见[创建Windows Kubernetes 集群](intl.zh-CN/用户指南/Kubernetes集群/集群管理/创建Windows Kubernetes 集群.md#)。

本例演示如何通过一个编排模板创建 aspnet 应用，包含一个 Deployment 和 Service，后端 Deployment会创建Pod 资源对象， Service 会绑定到后端 Pod 上，形成一个完整的 aspnet 应用。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。 
2.  在 Kubernetes 菜单下，单击左侧导航栏中的**应用** \> **无状态**，进入无状态（Deployment）页面。 
3.  单击页面右上角的**使用模板创建**。 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/150114/155558050941756_zh-CN.png)

4.  对模板进行相关配置，完成配置后单击**创建**。 

    -   **集群**：选择目标集群。资源对象将部署在该集群内。
    -   **命名空间**：选择资源对象所属的命名空间，默认是 default。除了节点、持久化存储卷等底层计算资源以外，大多数资源对象需要作用于命名空间。
    -   **示例模板**：阿里云容器服务提供了多种资源类型的 Kubernetes yaml 示例模板，让您快速部署资源对象。您可以根据 Kubernetes Yaml 编排的格式要求自主编写，来描述您想定义的资源类型。
    -   **添加部署**：您可通过此功能快速定义一个Yaml模板。
    -   **使用已有模板**：您可将已有编排模板导入到模板配置页面。
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/150114/155558051141759_zh-CN.png)

    下面是一个aspnet 应用的示例编排，基于容器服务内置的编排模板。通过该编排模板，即可快速创建一个属于 aspnet 应用的 deployment 。

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
    apiVersion: apps/v1beta2
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
            - image: 'microsoft/dotnet-samples:aspnetapp'
              name: aspnet
    ```

5.  单击**创建**后，会提示部署状态信息。成功后，单击**Kubernetes 控制台**前往Kubernetes Dashboard 查看部署进度。 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/150114/155558051141783_zh-CN.png)

6.  在 Kubernetes Dashboard 里，您可以看到 aspnet-svc服务已成功部署，并暴露了外部入口。单击**外部端点**的访问地址。 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/150114/155558051141786_zh-CN.png)

7.  您可以在浏览器中访问 aspnet 首页。 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/150114/155558051141787_zh-CN.png)


您也可返回容器服务首页，单击左侧导航栏中的**路由与负载均衡** \> **服务**，查看该aspnet的服务。

