# 通过编排模板创建Linux应用

在容器服务kubernetes模板编排中，您需要自己定义一个应用运行所需的资源对象，通过标签选择器等机制，将资源对象组合成一个完整的应用。

[创建Kubernetes专有版集群](/intl.zh-CN/Kubernetes集群用户指南/集群管理/创建集群/创建Kubernetes专有版集群.md)。

本例演示如何通过一个编排模板创建Nginx应用，包含一个Deployment和Service，后端Deployment会创建Pod资源对象，Service会绑定到后端Pod上，形成一个完整的Nginx应用。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**工作负载** \> **无状态**。

5.  在无状态（Deployment）页面，单击右上角的**使用模板创建**。

6.  对模板进行相关配置，完成配置后单击**创建**。

    -   **集群**：选择目标集群。资源对象将部署在该集群内。
    -   **命名空间**：选择资源对象所属的命名空间，默认是Default。除了节点、持久化存储卷等底层计算资源以外，大多数资源对象需要作用于命名空间。
    -   **示例模板**：阿里云容器服务提供了多种资源类型的Kubernetes YAML示例模板，让您快速部署资源对象。您可以根据Kubernetes YAML编排的格式要求自主编写，来描述您想定义的资源类型。
    -   **添加部署**：您可通过此功能快速定义一个YAML模板。
    -   **使用已有模板**：您可将已有编排模板导入到模板配置页面。
    下面是一个Nginx应用的示例编排，基于容器服务内置的编排模板。通过该编排模板，即可快速创建一个属于Nginx应用的Deployment。

    **说明：** 容器服务支持Kubernetes YAML编排，支持通过`---`符号将资源对象分隔，从而通过一个模板创建多个资源对象。

    ```
    apiVersion: apps/v1 
    kind: Deployment
    metadata:
        name: nginx-deployment-basic
        labels:
          app: nginx
    spec:
        replicas: 2
        selector:
          matchLabels:
            app: nginx
        template:
          metadata:
            labels:
              app: nginx
          spec:
            containers:
            - name: nginx
              image: nginx:1.7.9 # replace it with your exactly <image_name:tags>
              ports:
              - containerPort: 80
    
    ---
    
    apiVersion: v1     
    kind: Service
    metadata:
       name: my-service1        #TODO: to specify your service name
       labels:
         app: nginx
    spec:
       selector:
         app: nginx             #TODO: change label selector to match your backend pod
       ports:
       - protocol: TCP
         name: http
         port: 30080            #TODO: choose an unique port on each node to avoid port conflict
         targetPort: 80
       type: LoadBalancer        ##本例中将type从Nodeport修改为LoadBalancer
    ```

7.  单击**创建**后。会提示部署状态信息。成功后，可以在Kubernetes Dashboard查看部署进度，详情请参见[通过Kubernetes Dashboard创建应用]()。

    ![查看部署进度](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9975659951/p11075.png)

8.  在Kubernetes Dashboard里，您可以看到my-service1服务已成功部署，并暴露了外部入口。单击**外部入口**的访问地址。

    ![外部入口](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9975659951/p11084.png)

9.  您可以在浏览器中访问Nginx服务欢迎页面。

    ![访问欢迎页](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9975659951/p11086.png)


