---
keyword: [ack-virtual-node, 变更记录, 介绍]
---

# ack-virtual-node组件介绍

ack-virtual-node组件是用于扩展Kubernetes集群弹性能力的组件，可部署在ACK或者线下Kubernetes集群中。本文介绍如何使用ack-virtual-node创建Pod。

ack-virtual-node将应用Pod以Serverless容器（ECI）方式运行，提供极致弹性、免容量规划、按需使用按需计费的能力。在Job类任务、CI/CD、Spark大数据计算、在线应用弹性等场景中可以显著提升应用部署的弹性效率，以及降低应用的计算成本。

关于ECI Pod功能的详细信息，请参见[ECI实例概述](/intl.zh-CN/Serverless Kubernetes集群用户指南/ECI Pod/ECI实例概述.md)。

![vk](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0265670061/p168908.png)

## 使用ack-virtual-node创建Pod。

1.  设置权限。

    在注册集群中安装组件前，您需要在接入集群中设置AK用来访问云服务的权限。设置AK前，您需要创建RAM用户并为其添加访问相关云资源的权限。

    1.  创建RAM用户。

        有关如何创建RAM用户的具体步骤，请参见[创建RAM用户](/intl.zh-CN/用户管理/创建RAM用户.md)。

    2.  创建权限策略。

        有关创建权限策略的具体操作步骤，请参见[创建自定义策略](/intl.zh-CN/权限策略管理/自定义策略/创建自定义策略.md)。

        ack-virtual-node组件权限策略信息如下。

        ```
        {
            "Version": "1",
            "Statement": [
                {
                    "Action": [
                        "eci:CreateContainerGroup",
                        "eci:DeleteContainerGroup",
                        "eci:DescribeContainerGroups",
                        "eci:DescribeContainerLog",
                        "eci:UpdateContainerGroup",
                        "eci:UpdateContainerGroupByTemplate",
                        "eci:CreateContainerGroupFromTemplate",
                        "eci:RestartContainerGroup",
                        "eci:ExportContainerGroupTemplate",
                        "eci:DescribeContainerGroupMetric",
                        "eci:DescribeMultiContainerGroupMetric",
                        "eci:ExecContainerCommand",
                        "eci:CreateImageCache",
                        "eci:DescribeImageCaches",
                        "eci:DeleteImageCache"
                    ],
                    "Resource": [
                        "*"
                    ],
                    "Effect": "Allow"
                }
            ]
        }
        ```

    3.  为RAM用户添加权限。

        有关如何为RAM用户授权的具体步骤，请参见[为RAM用户授权](/intl.zh-CN/用户管理/为RAM用户授权.md)。

    4.  为RAM用户创建AK。

        有关如何为子账户创建AK，请参见[获取AccessKey]()。

        创建ack-virtual-node组件使用的Secret的代码如下。

        ```
        kubectl create nskubectl -n kube-system create secret generic alibaba-addon-secret --from-literal='access-key-id=<your access key id>' --from-literal='access-key-secret=<your access key secret>'
        ```

        您需要将上述代码中`<your access key id>`和`<your access key secret>`替换为您获取的AK信息。

2.  安装ack-virtual-node组件。

    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

    2.  在集群列表页面，选择目标集群，并在目标集群右侧**操作**列下，选择**更多** \> **系统组件管理**。

    3.  找到**ack-virtual-node**组件，然后单击右侧的**安装**。

3.  创建ECI Pod。

    ack-virtual-node支持两种创建ECI Pod的方法：

    -   配置Pod的标签：给Pod添加eci=true的标签。

        ```
        kubectl run nginx --image nginx -l eci=true
        ```

    -   配置Namespace的标签：给Namespace添加eci=true的标签。

        ```
        kubectl create ns vk
        
        kubectl label namespace vk eci=true
        
        kubectl -n vk run nginx --image nginx
        ```


**相关文档**  


[ack-virtual-node变更记录](/intl.zh-CN/产品发布记录/组件介绍与变更记录/其他/vk-scaler/变更记录.md)

