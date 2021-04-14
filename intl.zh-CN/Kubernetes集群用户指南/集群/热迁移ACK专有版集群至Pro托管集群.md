---
keyword: [ACK专有版集群, Kubernetes集群迁移, Pro集群]
---

# 热迁移ACK专有版集群至Pro托管集群

为了能够让存量的ACK专有版管集群用户享受到ACK Pro托管集群的功能和特性，容器服务平台推出了专有版到Pro版的集群热迁移功能，使您可以动态地迁移ACK专有版集群至Pro托管集群。本文介绍如何迁移ACK专有版集群至Pro托管集群中。

迁移ACK专有版集群至Pro托管版集群前，您需要创建一个OSS的存储空间（Bucket）。关于如何创建OSS Bucket的具体步骤，请参见[创建存储空间](/intl.zh-CN/控制台用户指南/存储空间管理/创建存储空间.md)。

## 注意事项

-   请确保您的ACK专有版集群的Kubernetes版本在1.16及以上。如不满足该条件，请升级您的集群版本。关于如何升级集群的Kubernetes版本的具体步骤，请参见[升级集群](/intl.zh-CN/Kubernetes集群用户指南/集群/升级集群/升级集群.md)。
-   请确保迁移之前，您已将管控面组件（API Server、Kube Controller Manager、Cloud Controller Manager、Scheduler）和其他kube-system中的Daemonset之外的Pod驱逐到Worker节点上。管控面组件在迁移之后会被托管的组件所替代。
-   迁移完成后，原有专有版集群的Master节点将脱离集群（状态变为**未知**），终止使用。
-   迁移完成后，不支持将Pro托管版集群回退至专有版集群。
-   迁移完成后，原有专有版集群的Master节点的ECS实例不会被自动删除，请注意及时删除不需要的ECS实例。

## 热迁移ACK专有版集群至Pro托管集群

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面，找到需要迁移的专有版集群，然后在**操作**列下单击**迁移至Pro版**。

4.  在**迁移至Pro版**对话框中完成专有版集群迁移至Pro托管版集群的授权操作，然后单击**确定**。

    迁移专有版集群至Pro托管版集群，您需要添加以下迁移所需的权限。

    1.  在**迁移至Pro版**对话框中，单击以**KubernetesMasterRole**开头的链接。

    2.  在Master RAM角色的详情页，单击目标授权策略名称。

    3.  在目标授权策略的详情页的**策略内容**页签，单击**修改策略内容**。

    4.  在**修改策略内容**面板中的**Statement**字段中补充以下策略内容，然后单击**确定**。

        ```
        ,
                {
                    "Action": [
                        "oss:PutObject",
                        "oss:GetObject"
                    ],
                    "Effect": "Allow",
                    "Resource": [
                        "acs:oss:*:*:<YOUR_BUCKET_NAME>/*"  # 需要将策略中的<YOUR_BUCKET_NAME>替换为您在迁移对话框中所指定的Bucket名称。
                    ]
                }
        ```

        添加策略后的示例如下图。

        ![策略](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4932938161/p263342.png)

    完成迁移之后，原有的Master节点状态会变成**未知**。查看节点状态步骤如下：

    1.  在集群列表页面，单击目标集群右侧**操作**列下的**详情**。
    2.  在集群管理左侧导航栏中，选择**节点管理** \> **节点**。
    3.  在节点列表的**角色/状态**列下，查看Master节点的状态。

## 删除专有版集群热迁移后的Master节点

在完成迁移专有版集群至Pro托管版集群后，您可以自行将Master节点从集群中删除。目前ACK暂时不支持从控制台直接删除Master节点，您可以通过kubectl命令方式删除Master节点。

在执行命令前，请确保您可以使用kubectl命令连接集群。关于如何使用kubectl命令连接集群的具体操作，请参见[通过kubectl连接Kubernetes集群](/intl.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl连接Kubernetes集群.md)。

1.  执行以下命令查看需要删除的Master节点名称。

    ```
    kubectl get node | grep master
    ```

2.  执行以下命令删除目标Master节点。

    ```
    kubectl delete node <MASTER_NAME>
    ```

    **说明：** 将<MASTER\_NAME\>替换为上步中查询到的Master节点名称。


## 相关文档

您还可以迁移ACK标准托管版集群至Pro托管版集群。具体操作，请参见[\#task\_2010778](#task_2010778)。

