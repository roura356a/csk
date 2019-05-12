# 更新Istio {#task_lhb_ktq_gfb .task}

您可以通过**更新**操作，编辑已部署的Istio。

-   您已成功创建一个 Kubernetes 集群，参见[创建Kubernetes集群](intl.zh-CN/用户指南/Kubernetes集群/集群管理/创建Kubernetes集群.md#)。
-   您已成功创建一个**Istio**，参见[部署Istio](intl.zh-CN/用户指南/Kubernetes集群/Istio管理/部署Istio.md#)。

1.  登录 [容器服务管理控制台](https://cs.console.aliyun.com/)。
2.  在 Kubernetes 菜单下，单击左侧导航栏的**应用** \> **发布**，进入发布页面。
3.  单击**Helm**，选择所需的集群，选择待更新的Istio，单击操作列的**更新**。 

    **说明：** 

    -   通过集群界面部署的Istio，**发布名称**为istio，更新的内容与部署时的选项一致。
    -   通过应用目录部署的Istio，**发布名称**为用户创建时指定的名称，更新的内容与部署时的参数一致。
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21819/155763379712755_zh-CN.png)

4.  在弹出的对话框中，对Istio的参数进行编辑后，单击**更新**。 

    此处以更新通过集群页面部署的Istio为例：

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21819/155763379712756_zh-CN.png)


可通过以下两种方式，查看更新后的内容：

-   更新完成后，页面自动跳转到发布页面，在资源页签，可以查看更新的内容。
-   在 Kubernetes 菜单下，单击左侧导航栏的**应用** \> **容器组**，进入容器组（Pod）页面，选择目标集群和目标空间进行查看。

