---
keyword: [ack-virtual-node, release notes, introduction]
---

# Introduction

The ack-virtual-node component is used to ensure optimal auto scaling capability of Container Service for Kubernetes \(ACK\) clusters. You can deploy the component in an ACK cluster or in an on-premises Kubernetes cluster. This topic describes how to create a pod by using ack-virtual-node.

The ack-virtual-node component can be used to run pods as serverless container groups in Elastic Container Instance. The pay-as-you-go billing method is supported. This ensures optimal auto scaling capability and avoids unnecessary resource usage. You can use ack-virtual-node to improve the efficiency of application deployment and auto scaling, and minimize the computing cost in multiple scenarios. These scenarios include Job tasks, continuous integration and delivery \(CI/CD\), Apache Spark big data computing, and online application auto scaling.

For more information about Elastic Container Instance-based pods, see [ECI overview](/intl.en-US/User Guide for Serverless Kubernetes Clusters/ECI Pod/ECI overview.md).

![vk](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7166194061/p168908.png)

## Use ack-virtual-node to create a pod

1.  Set permissions.

    Before you can install the component in a registered cluster, you must specify the AccessKey information to authenticate requests that are sent to cloud resources. Before you set the AccessKey information, create a Resource Access Management \(RAM\) user and grant the RAM user the permissions to access Alibaba Cloud resources.

    1.  Create a RAM user.

        For more information, see [Create a RAM user](/intl.en-US/RAM User Management/Create a RAM user.md).

    2.  Create a permission policy.

        For more information about how to create a permission policy, see [Create a custom policy](/intl.en-US/Policy Management/Custom policies/Create a custom policy.md).

        The following example shows the permission policies of ack-virtual-node.

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

    3.  Grant permissions to the RAM user

        For more information about how to grant permissions to the RAM user, see [Grant permissions to a RAM user](/intl.en-US/RAM User Management/Grant permissions to a RAM user.md).

    4.  Set the AccessKey information for the RAM user.

        For more information about how to set the AccessKey information for the RAM user, see [Obtain an AccessKey pair]().

        Run the following command to create the Secret for ack-virtual-node:

        ```
        kubectl create nskubectl -n kube-system create secret generic alibaba-addon-secret --from-literal='access-key-id=<your access key id>' --from-literal='access-key-secret=<your access key secret>'
        ```

        Replace `<your access key id>` and `<your access key secret>` in the command with your AccessKey information.

2.  Install the ack-virtual-node component.

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane, click Clusters. On the Clusters page, find the cluster that you want to manage, and choose **More** \> **Manage System Components** in the **Actions** column of the cluster.

    3.  You are redirected to the Add-ons page. Find the **ack-virtual-node** component and click **Install** for the component.

3.  Create an Elastic Container Instance-based pod.

    ack-virtual-node allows you to create an Elastic Container Instance-based pod by using one of the following methods:

    -   Add the label eci=true to the pod.

        ```
        kubectl run nginx --image nginx -l eci=true
        ```

    -   Add the label eci=true to the namespace.

        ```
        kubectl create ns vk
        
        kubectl label namespace vk eci=true
        
        kubectl -n vk run nginx --image nginx
        ```


**Related topics**  


[Release notes](/intl.en-US/Release notes/System Component change Records/Other SQL statements/vk-scaler/Release notes.md)

