# Worker节点的资源升配

随着Kubernetes集群负载的增加，用户常常面临资源不足的情况，此时您可以对集群的基础资源做一些扩展。例如，当集群规模达到10台以上后，您可以优先考虑升级Worker节点的配置，这样既可以提高资源的利用率，也可以降低集群运维的复杂度。本文为您介绍如何升级Worker节点的配置。

您已经成功创建Kubernetes集群，参见[创建Kubernetes托管版集群](/intl.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  根据需要选择购买新节点或添加已有节点，升配节点资源。

    -   如果您想通过购买新节点的方法升级配置，请参见[扩容集群](/intl.zh-CN/Kubernetes集群用户指南/集群/扩容集群.md)，并按照如下步骤操作。
        1.  在目标集群右侧单击**操作**列下**更多** \> **集群扩容**。
        2.  设置新增的Worker节点配置。

            ![集群扩容](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3528079951/p50574.png)

        3.  设置需要扩容的Worker节点的数量。

            ![集群扩容](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3528079951/p10905.png)

        4.  设置新增的Worker节点的登录方式。

            ![集群扩容](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7463659951/p9041.png)

        5.  设置RDS白名单。

            ![集群扩容](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7463659951/p21270.png)

        6.  为新增节点绑定标签。

            ![集群扩容](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7463659951/p50326.png)

        7.  配置完成后，单击**确定**。
    -   如果您想通过添加已有节点的方法升级配置，请按照如下步骤操作。
        1.  在目标集群右侧单击**操作**列**更多** \> **添加已有节点**。

            进入添加节点页面，您可以选择**自动添加**或**手动添加**的方式，添加现有云服务器实例。请参见[添加已有节点](/intl.zh-CN/Kubernetes集群用户指南/节点与节点池/节点/添加已有节点.md)。本例以自动添加为例进行升配。

        2.  添加方式选择**自动添加**，在已有云服务器的列表中，选择所需的ECS云服务器，然后单击**下一步**。

            ![自动添加](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7463659951/p10826.png)

        3.  填写实例信息，设置CPU Policy、登录密码和标签，然后单击**下一步**。

            ![实例信息](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7463659951/p10827.png)

        4.  在弹出的对话框中，单击**确定**，选择的ECS云服务器会自动添加到该集群中。

            ![ ECS 云服务器](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7463659951/p10828.png)

4.  在集群管理页面，单击**节点管理**，查看新增节点的状态。

    待新增的节点的状态为**运行中**时，此时Worker节点升配完成。


如果您想要移除节点，您可以在节点列表，单击目标节点右侧**操作**列的**更多** \> **移除**，在弹出的对话框中单击**确定**。

**说明：**

-   为了确保升级过程中应用的稳定，建议您先加入新节点，再移除多余节点。
-   移除的过程中，为了让应用更稳定，可以配置[pdb](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/)。

