# Kubernetes基于ARMS的应用监控 {#concept_w3d_nn1_kgb .concept}

本文介绍容器服务Kubernetes基于业务实时监控服务ARMS（Application Real-Time Monitoring Service）如何进行应用监控。

## 背景信息 {#section_zrv_441_kgb .section}

业务实时监控服务ARMS（Application Real-Time Monitoring Service）是一款阿里云应用性能管理（APM）类监控产品。

ARMS应用监控是一款针对 Java 应用的性能管理（Application Performance Management，简称 APM）软件。您无需修改任何代码，只需要在 Java 应用的启动脚本中挂载一个探针，该探针就能够对您的 Java 应用进行全方位监控，帮助您更快速地定位出错接口和慢接口、重现调用参数、检测内存泄漏、发现系统瓶颈，从而大幅提升线上问题诊断问题的效率。ARMS产品详情，可参考[业务实时监控服务 ARMS](https://www.alibabacloud.com/help/zh/product/34364.html)。

**主要功能**

-   自动发现应用拓扑

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/92331/155558235837130_zh-CN.png)

-   自动发现并监控接口
-   捕获异常事务和SQL分析、慢事务
-   Java异常报表
-   查询基于调用链的事务快照

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/92331/155558235837131_zh-CN.png)

-   即席多维排查（多维度调用链搜索，异常调用链搜索）
-   PaaS平台集成

## 前提条件 {#section_bqm_3wp_kgb .section}

-   您已成功创建一个Kubernetes集群，参见[创建Kubernetes集群](../../../../intl.zh-CN/用户指南/Kubernetes集群/集群管理/创建Kubernetes集群.md#)。
-   您已开通ARMS应用监控服务，参见[开通 ARMS 服务](https://www.alibabacloud.com/help/zh/doc-detail/65257.html)。

## 安装组件 {#section_f32_vwp_kgb .section}

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
2.  单击左侧导航栏**市场** \> **应用目录**，在应用目录页面选择**ack-arms-pilot**。
3.  在应用目录-ack-arms-pilot页面，单击右侧的**创建**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/92331/155558235837035_zh-CN.png)


单击左侧导航栏**应用** \> **无状态**，选择目标集群和命名空间，可以看到名称为ack-arms-pilot-default-ack-arms-pilot的应用已成功创建。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/92331/155558235837071_zh-CN.png)

## 授权 {#section_q5g_fgq_kgb .section}

1.  在容器服务管理控制台，单击左侧导航栏**集群**，进入集群列表页面。

    **说明：** 请以主账号登录容器服务管理控制台进行授权操作。

2.  单击目标集群的名称，查看集群的详细信息。
3.  单击集群资源区域的**Worker RAM角色**，进入RAM访问控制台的RAM角色管理页面。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/92331/155558235937044_zh-CN.png)

    **说明：** 

    本文以新版RAM访问控制台为例进行介绍。

    若您使用的是旧版的RAM访问控制台：

    **方法一**

    1.  单击左侧导航栏**角色管理**，在**角色名**中输入**Worker RAM角色**的名称进行搜索。单击**角色名称**。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/92331/155558235937085_zh-CN.png)

    2.  在**基本信息**区域，单击页面右上角的**编辑基本信息**。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/92331/155558235937088_zh-CN.png)

    **方法二**

    单击页面右下角**体验新版**，切换到新版RAM访问控制台。在容器服务管理控制台单击**Worker RAM 角色**重新登录RAM访问控制台。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/92331/155558235937079_zh-CN.png)

4.  在RAM角色管理页面，单击权限管理区域的权限策略名称，查看具体的权限策略。
5.  在权限策略管理页面，单击策略内容区域的**修改策略内容**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/92331/155558236137053_zh-CN.png)

6.  在策略内容区域增加以下字段后，单击**确定**。

    ```
    {
        "Action": "arms:*",
        "Resource": "*",
        "Effect": "Allow"
    }
    ```

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/92331/155558236237054_zh-CN.png)


## 部署ARMS应用监控 {#section_nmg_xlq_kgb .section}

在创建Deployment的yaml文件中，通过添加以下`annotations`部署ARMS应用监控。

```
annotations:
  armsPilotAutoEnable: "on"
  armsPilotCreateAppName: "<your-deployment-name>"
```

**说明：** 

-   `annotations`添加到yaml文件`spec`字段`template`的`metadata`下。
-   `armsPilotCreateAppName`的内容为ARMS中应用的名称。

1.  在容器服务管理控制台，单击左侧导航栏**应用** \> **无状态**。
2.  单击页面右上角**使用模板创建**。
3.  选择目标集群和命名空间，创建Deployment。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/92331/155558236338486_zh-CN.png)

    ```
    apiVersion: apps/v1beta1 # for versions before 1.8.0 use apps/v1beta1
    kind: Deployment
    metadata:
      name: arms-springboot-demo
      labels:
        app: arms-springboot-demo
    spec:
      replicas: 2
      selector:
        matchLabels:
          app: arms-springboot-demo
      template:
        metadata:
          annotations:
            armsPilotAutoEnable: "on"
            armsPilotCreateAppName: "arms-k8s-demo"
          labels:
            app: arms-springboot-demo
        spec:
          containers:
            - resources:
                limits:
                  cpu: 0.5
              image: registry.cn-hangzhou.aliyuncs.com/arms-docker-repo/arms-springboot-demo:v0.1
              imagePullPolicy: Always
              name: arms-springboot-demo
              env:
                - name: MYSQL_SERVICE_HOST
                  value: "arms-demo-mysql"
                - name: MYSQL_SERVICE_PORT
                  value: "3306"
    ---
    apiVersion: apps/v1beta1 # for versions before 1.8.0 use apps/v1beta1
    kind: Deployment
    metadata:
      name: arms-demo-mysql
      labels:
        app: mysql
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: mysql
      template:
        metadata:
          labels:
            app: mysql
        spec:
          containers:
            - resources:
                limits:
                  cpu: 0.5
              image: registry.cn-hangzhou.aliyuncs.com/arms-docker-repo/arms-demo-mysql:v0.1
              name: mysql
              ports:
                - containerPort: 3306
                  name: mysql
    ---
    apiVersion: v1
    kind: Service
    metadata:
      labels:
        name: mysql
      name: arms-demo-mysql
    spec:
      ports:
        # the port that this service should serve on
        - name: arms-mysql-svc
          port: 3306
          targetPort: 3306
      # label keys and values that must match in order to receive traffic for this service
      selector:
        app: mysql
    ---
    ```


**执行结果**

1.  单击左侧导航栏**应用** \> **无状态**，选择目标集群和命名空间，可看到刚刚部署的Deployment。
2.  单击目标Deployment**操作列**的**ARMS控制台**，登录ARMS管理控制台查看应用的详细信息，例如：**应用健康概览**、**接口调用**等。

    **说明：** 若**操作列**无**ARMS控制台**，请检查您是否授权容器服务调用ARMS，可参考[授权](#section_q5g_fgq_kgb)。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/92331/155558236337099_zh-CN.png)


