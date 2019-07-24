# Worker节点的资源升配 {#task_895194 .task}

随着Kubernetes集群负载的增加，用户常常面临资源不足的情况，此时您可以对集群的基础资源做一些扩展。例如，在集群规模达到10台以上的规模后，您可以优先考虑升级Worker节点的配置，这样既可以提高资源的利用率，也可以降低集群运维的复杂度。本文为您介绍如何升级Worker节点的配置。

您已经成功创建Kubernetes集群，参见[创建Kubernetes集群](../../../../intl.zh-CN/用户指南/Kubernetes集群/集群管理/创建Kubernetes集群.md#)。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com/)。
2.  在 Kubernetes 菜单下，选择**集群** \> **集群**，进入集群列表页面。 
    -   如果您想通过购买新节点的方法升级配置，请参见[扩容集群](../../../../intl.zh-CN/用户指南/Kubernetes集群/集群管理/扩容集群.md#)，并按照如下步骤操作。
        1.  在目标集群右侧单击操作列**集群扩容**，进入集群扩容页面。

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16647/156395004910904_zh-CN.png)

        2.  设置需要扩容的Worker节点的数量。

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16647/156395004910905_zh-CN.png)

        3.  设置新增的Worker节点配置。

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16647/156395005050574_zh-CN.png)

        4.  设置新增的Worker节点的登录方式。

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15639500509041_zh-CN.png)

        5.  设置RDS白名单。

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/156395005021270_zh-CN.png)

        6.  为新增节点绑定标签。

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/156395005050326_zh-CN.png)

        7.  配置完成后，单击**提交**。
    -   如果您想通过添加已有节点的方法升级配置，请按照如下步骤操作。
        1.  在目标集群右侧单击操作列**更多** \> **添加已有节点**。

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16929/156395005010825_zh-CN.png)

            进入添加节点页面，您可以选择**自动添加**或**手动添加**的方式，添加现有云服务器实例。请参见[添加已有节点](../../../../intl.zh-CN/用户指南/Kubernetes集群/节点管理/添加已有节点.md#)。本例以自动添加为例进行升配。

        2.  添加方式选择**自动添加**，在已有云服务器的列表中，选择所需的ECS云服务器，然后单击**下一步**。

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16929/156395005110826_zh-CN.png)

        3.  填写实例信息，设置CUP Policy、登录密码和标签，然后单击**下一步**。

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16929/156395005110827_zh-CN.png)

        4.  在弹出的对话框中，单击**确定**，选择的 ECS 云服务器会自动添加到该集群中。

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16929/156395005110828_zh-CN.png)

3.  选择**集群** \> **节点**，待新增的节点的状态为**运行中**时，此时Worker节点升配完成。

如果您想要移除节点，您可以在节点列表，选择目标集群，在目标节点右侧操作列单击**更多** \> **移除**，在弹出的对话框中单击**确定**。

**说明：** 

-   为了确保升级过程中应用的稳定，建议您先加入新节点，再移除多余节点。
-   移除的过程中，为了让应用更稳定，可以配置[pdb](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/)。

