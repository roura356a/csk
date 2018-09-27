# 删除Istio {#task_qmp_ltq_gfb .task}

您可以通过**删除**操作，删除已部署的Istio。

-   您已成功创建一个 Kubernetes 集群，参见[创建Kubernetes集群](intl.zh-CN/用户指南/Kubernetes 集群/集群管理/创建Kubernetes集群.md#)。
-   您已成功创建一个**Istio**，参见[部署Istio](intl.zh-CN/用户指南/Kubernetes 集群/Istio管理/部署Istio.md#)。

1.  登录 [容器服务管理控制台](https://cs.console.aliyun.com/)。 
2.  在 Kubernetes 菜单下，单击左侧导航栏的**应用** \> **Helm**，进入发布列表页面。 
3.  选择所需的集群及待删除的Istio，单击操作列的**删除**。 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21820/153801472512751_zh-CN.png)

4.  在弹出的对话框中，单击**确定**。 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21820/153801472512752_zh-CN.png)

    **说明：** 

    -   不勾选**清除发布记录**：
        -   发布记录不会被删除：

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21820/153801472512783_zh-CN.png)

        -   此Istio的名称不可被再次使用。

            通过集群界面重新部署Istio时，显示已完成。

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21820/153801472512784_zh-CN.png)

            通过应用目录部署Istio时，提示：已存在同名的部署或资源，请修改名称。

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21820/153801472612785_zh-CN.png)

    -   勾选**清除发布记录**，会同时删除所有发布记录，且此Istio的名称可被再次使用。
    建议保持默认状态，勾选**清除发布记录**。


返回发布列表页面，可以看到该Istio已被删除。

