# 删除Istio {#task_qmp_ltq_gfb .task}

您可以通过**删除**操作，删除已部署的Istio。

-   您已成功创建一个 Kubernetes 集群，参见[ZH-CN\_TP\_16639.md\#](intl.zh-CN/Kubernetes集群用户指南/集群管理/创建集群/创建Kubernetes 集群.md#)。
-   您已成功创建一个Istio，参见[部署Istio](intl.zh-CN/Kubernetes集群用户指南/Istio管理/部署Istio.md#)。
-   在安装Istio的集群中，名称为istio-init的Helm发布正常运行中。在执行如下删除操作前不能删除istio-init。
-   您已连接到Kubernetes集群的Master节点，参见[通过 kubectl 连接 Kubernetes 集群](intl.zh-CN/Kubernetes集群用户指南/集群管理/管理与访问集群/通过 kubectl 连接 Kubernetes 集群.md#)。

1.  登录 [容器服务管理控制台](https://cs.console.aliyun.com/)。
2.  单击左侧导航栏的**服务网格** \> **Istio管理**，进入Istio管理页面。
3.  在页面的右上方，单击**删除**按钮。
4.  在弹出的对话框中，单击**确定**。
5.  单击左侧导航栏的**应用** \> **容器组**，选择对应的集群和命名空间**istio-sytem**，确认Istio相关容器删除。 

    **说明：** 如果出现命名空间istio-system的istio-config资源出现无法删除的情况，请参考[删除后custom resource对象 残留](../../../../intl.zh-CN/Kubernetes集群用户指南/Istio管理/Istio 常见问题.md#section_tfq_ubs_npg)进行操作。


单击左侧导航栏的**应用** \> **容器组**，选择对应的集群和命名空间**istio-sytem**，确认Istio相关容器删除。

