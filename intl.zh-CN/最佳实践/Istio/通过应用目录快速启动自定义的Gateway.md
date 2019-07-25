# 通过应用目录快速启动自定义的Gateway {#task_264105 .task}

本文讲述如何通过应用目录，快速启动自定义的Gateway。

-   请确保目标集群已[部署Istio](../../../../intl.zh-CN/用户指南/Kubernetes集群/Istio管理/部署Istio.md#)。
-   每个命名空间下，有且只能部署一个Gateway。
-   请确保自定义的 Gateway 的发布名称在同一个命名空间下唯一。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com/)。
2.  在 Kubernetes 菜单下，单击左侧导航栏中的**市场** \> **应用目录**，在右侧选中**ack-istio-customgateway**。
3.  在应用目录 - ack-istio-customgateway中，单击**参数**，可以通过修改参数配置进行定制化，如下所示。 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/217837/156404711847104_zh-CN.png)

    **说明：** 

    **参数**区域中有两个customgateway，您可以自定义两个customgateway，为了管理方便，建议customgateway和Istio的值customgateway保持一致。并且在同一个namespace下，名称要唯一性。

4.  在右侧的创建页面，在右侧选择对应的集群，同时可以看到命名空间已设定为default，发布名称已设定为ack-istio-customgateway-default，然后单击**创建**。
5.  几秒钟后，在弹出Helm 发布列表 - ack-istio-customgateway-default页面，Istio 应用Gateway 发布在集群中被创建出来。

