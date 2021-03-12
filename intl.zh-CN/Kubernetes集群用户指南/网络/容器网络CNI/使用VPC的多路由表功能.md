---
keyword: [CCM, vpc多路由表, 专有版集群, k8s]
---

# 使用VPC的多路由表功能

ACK通过CCM（Cloud Controller Manager）组件在VPC路由表中添加合适的路由来打通集群中Pod之间的网络连接。您可以通过更新cloud-config配置文件让ACK专有版集群使用VPC多路由表功能。本文介绍如何让ACK专有版集群使用VPC支持的多路由表。

-   创建一个ACK专有版集群。具体步骤，请参见[创建Kubernetes专有版集群](/intl.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes专有版集群.md)。
-   ACK专有版集群的网络为Flannel模式。
-   CCM版本需大于v1.9.3.86-g4454991-aliyun。关于如何查看CCM版本的步骤，请参见[管理组件](/intl.zh-CN/Kubernetes集群用户指南/集群/升级集群/管理组件.md)。

VPC对多个路由表的支持能力，让您可以自行选择ECS关联路由表。由于CCM早期版本中仅提供了VPC单路由表的支持，因此您需要在容器服务控制台升级CCM至最新版本。关于如何升级CCM的版本，请参见[管理组件](/intl.zh-CN/Kubernetes集群用户指南/集群/升级集群/管理组件.md)。

**说明：** 目前仅ACK专有版集群可通过自行配置使用VPC多路由表功能。如果需ACK托管版集群支持使用VPC的多路由表功能，请[提交工单](https://workorder-intl.console.aliyun.com/console.htm)。

## 通过kubectl命令行方式配置支持VPC多路由表

使用kubectl命令方式配置支持VPC多路由表前，确保可以使用kubectl命令链接ACK专有版集群。具体操作，请参见[通过kubectl连接Kubernetes集群](/intl.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl连接Kubernetes集群.md)。

1.  更新配置项cloud-config文件。

    使用命令`kubectl edit cm -n kube-system cloud-config`修改cloud-config配置项文件，将配置项文件中的`${ROUTE_TABLES_IDS}`替换为您的VPC路由表ID（必须包含系统路由表），多个路由表使用英文逗号（,）分隔（例如，vtb-t4n788888\*\*\*\*,vtb-t4n7k6u3m0n840799\*\*\*\*）。

    cloud-config配置项文件部分内容如下。

    ```
    apiVersion: v1
    kind: ConfigMap
    metadata:
      name: cloud-config
      namespace: kube-system
    data:
      cloud-config.conf: |-
        {
            "Global": {
                "routeTableIDs": "${ROUTE_TABLES_IDS}"
            }
        }
    ```

2.  执行以下命令重启CCM Pod。

    ```
    kubectl -n kube-system delete po -lapp=cloud-controller-manager
    ```

    重启完毕后，即可在路由表中看到对应的集群节点条目。


## 通过控制台方式配置支持VPC多路由表

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**配置管理** \> **配置项**。

5.  在kube-system命名空间下，单击配置项cloud-config右侧的**YAML编辑**。

6.  在**查看YAML**面板的routeTableIDs参数的值中，填写您的VPC路由表ID（必须包含系统路由表），多个路由表使用英文逗号（,）分隔（例如，vtb-t4n788888\*\*\*\*,vtb-t4n7k6u3m0n840799\*\*\*\*），然后单击**确定**。

    ![cloud-config](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9555355161/p248985.png)

7.  在集群管理页左侧导航栏中，选择**工作负载** \> **无状态**。

8.  在kube-system命名空间下，找到应用alicloud-application-controller并在其右侧，选择**更多** \> **重新部署**。

    重新部署后，即可在路由表中看到对应的集群节点条目。


