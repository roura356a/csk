# 如何给 Pod 挂载弹性公网IP {#concept_266137 .concept}

本文主要为您介绍如何在 serverless kubernetes 或虚拟节点中给Pod挂载 EIP。

## 背景信息 {#section_33u_zx6_h2x .section}

阿里云 Serverless Kubernetes 服务/虚拟节点 推出 Pod 挂载弹性公网IP功能，此功能使某些 Serverless容器应用的部署和服务访问变得更加简单和便利。

-   无需创建 VPC NAT网关即可让单个 Pod 访问公网
-   无需创建 Service 也可让单个 Pod 暴露公网服务
-   可以更加灵活而且动态的绑定 Pod 和 EIP

## 前提条件 {#section_l86_vis_89h .section}

-   您已经创建一个[Serverless Kubernetes集群](intl.zh-CN/用户指南/Serverless Kubernetes 集群/集群管理/创建 Serverless Kubernetes 集群.md#)或已在Kubernetes 集群创建一个[虚拟节点](../../../../intl.zh-CN/用户指南/Kubernetes集群/弹性伸缩/虚拟节点.md#)。
-   请确保该集群的安全组已开放相关端口（本示例中需要开放80端口）。

## 操作步骤 {#section_c65_cdu_1gi .section}

1.  登录 VPC 管理控制台，购买弹性公网 IP。请参考[申请EIP](../../../../intl.zh-CN/用户指南/申请EIP.md#)。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/220325/155840461247489_zh-CN.png)

    **说明：** 申请的 EIP 和集群必须在同一区域。

2.  登录 [容器服务管理控制台](https://cs.console.aliyun.com)。
3.  在 Kubernetes 菜单下，单击左侧导航栏中的**应用** \> **无状态**，进入无状态（Deployment）页面。
4.  选择所需的集群和命名空间，选择样例模板或自定义，然后单击**创建**。

    您可以使用如下 yaml 示例模板创建Pod（本例中，通过指定Annonation k8s.aliyun.com/eci-eip-instanceid为true，Serverless Kubernetes服务/虚拟节点会自动为此Pod分配一个EIP） 。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/220325/155840461247512_zh-CN.png)

    ``` {#codeblock_kma_1jw_bs5}
    apiVersion: v1
    kind: Pod
    metadata:
      name: nginx
      annotations:
        "k8s.aliyun.com/eci-eip-instanceid": "<youreipInstanceId>"
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

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/220325/155840461347513_zh-CN.png)

6.  在浏览器中输入http://ip地址，您可访问 nginx 欢迎页。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/220325/155840461347613_zh-CN.png)

    **说明：** 此处的http://ip地址为步骤1中申请的EIP的IP地址。


