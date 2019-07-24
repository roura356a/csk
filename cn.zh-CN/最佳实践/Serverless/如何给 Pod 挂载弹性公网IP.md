# 如何给 Pod 挂载弹性公网IP {#concept_266137 .concept}

本文主要为您介绍如何在 serverless kubernetes 或虚拟节点中给Pod挂载 EIP。

## 背景信息 {#section_33u_zx6_h2x .section}

阿里云 Serverless Kubernetes 服务/虚拟节点推出 Pod 挂载弹性公网IP功能，此功能使某些 Serverless容器应用的部署和服务访问变得更加简单和便利。

-   无需创建 VPC NAT网关即可让单个 Pod 访问公网
-   无需创建 Service 也可让单个 Pod 暴露公网服务
-   可以更加灵活而且动态的绑定 Pod 和 EIP

## 前提条件 {#section_l86_vis_89h .section}

-   您已经创建一个[Serverless Kubernetes集群](intl.zh-CN/用户指南/Serverless Kubernetes 集群/集群管理/创建 Serverless Kubernetes 集群.md#)或已在Kubernetes 集群创建一个[虚拟节点](../../../../intl.zh-CN/用户指南/Kubernetes集群/弹性伸缩/虚拟节点.md#)。
-   请确保该集群的安全组已开放相关端口（本示例中需要开放80端口）。

## 操作步骤 {#section_c65_cdu_1gi .section}

您可以通过以下两种方法，给Pod挂载弹性公网IP。

-   自动分配弹性公网 EIP
    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
    2.  在 Kubernetes 菜单下，单击左侧导航栏中的**应用** \> **无状态**，进入无状态（Deployment）页面。
    3.  选择所需的集群和命名空间，选择样例模板或自定义，然后单击**创建**。

        您可以使用如下 yaml 示例模板创建 Pod。本例中，通过指定k8s.aliyun.com/eci-with-eip为 true，Serverless Kubernetes服务/虚拟节点会自动为此Pod分配一个EIP，并且绑定到 Pod上。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/220325/156394994151458_zh-CN.png)

        ``` {#codeblock_y2n_h3l_5y5}
        apiVersion: v1
        kind: Pod
        metadata:
          name: nginx
          annotations:
            "**k8s.aliyun.com/eci-with-eip**": "**true**"
        spec:
          containers:
          - image: registry-vpc.cn-hangzhou.aliyuncs.com/jovi/nginx:alpine
            imagePullPolicy: Always
            name: nginx
            ports:
            - containerPort: 80
              name: http
              protocol: TCP
          restartPolicy: OnFailure
        ```

        **说明：** 

        -   您也可以通过Annotation k8s.aliyun.com/eip-bandwidth指定EIP的带宽，默认值为5，单位为M。
        -   如果您创建的是 Deployment，那么 Deployment 中的每一个 Pod 都将会被挂载不同的 EIP，请谨慎使用此操作。
    4.  单击左侧导航栏中的**应用** \> **容器组**，查看容器的状态。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/220325/156394994151460_zh-CN.png)

    5.  在目标容器组右侧单击**更多** \> **编辑**，弹出编辑 YAML文件。

        **说明：** YAML文件中`k8s.aliyun.com/allocated-eipAddress: 47.110.XX.XX`的IP地址即为EIP的公网访问地址。

    6.  在浏览器中输入http://ip地址，您可访问 nginx 欢迎页。

        此处的http://ip地址为YAML文件中`k8s.aliyun.com/allocated-eipAddress: 47.110.XX.XX`的IP地址。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/220325/156394994151465_zh-CN.png)

        **说明：** 此方式中 EIP 为动态分配，其生命周期与Pod相同，当删除Pod时，动态分配的EIP也会一并删除。

-   指定弹性公网 IP实例 ID
    1.  登录 VPC 管理控制台，购买弹性公网 IP。请参考[申请EIP](../../../../intl.zh-CN/用户指南/申请EIP/申请新EIP.md#)。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/220325/156394994147489_zh-CN.png)

        **说明：** 申请的 EIP 和集群必须在同一区域。

    2.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
    3.  在 Kubernetes 菜单下，单击左侧导航栏中的**应用** \> **无状态**，进入无状态（Deployment）页面。
    4.  选择所需的集群和命名空间，选择样例模板或自定义，然后单击**创建**。

        您可以使用如下 yaml 示例模板创建 Pod。本例中，通过指定Pod的Annonation k8s.aliyun.com/eci-eip-instanceid为EIP实例ID。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/220325/156394994247512_zh-CN.png)

        ``` {#codeblock_t8p_xmn_avd}
        apiVersion: v1
        kind: Pod
        metadata:
          name: nginx
          annotations:
            "**k8s.aliyun.com/eci-eip-instanceid**": "**<youreipInstanceId\>**"
        spec:
          containers:
          - image: registry-vpc.cn-hangzhou.aliyuncs.com/jovi/nginx:alpine
            imagePullPolicy: Always
            name: nginx
            ports:
            - containerPort: 80
              name: http
              protocol: TCP
          restartPolicy: OnFailure
        ```

        **说明：** `<youreipInstanceId>`需要替换成步骤1中获取的EIP实例ID。

    5.  单击左侧导航栏中的**应用** \> **容器组**，查看容器的状态。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/220325/156394994247513_zh-CN.png)

    6.  在浏览器中输入http://ip地址，您可访问 nginx 欢迎页。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/220325/156394994247613_zh-CN.png)

        **说明：** 此处的http://ip地址为步骤1中申请的EIP的IP地址。


