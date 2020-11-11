---
keyword: [vk-scaler, release notes, introduction]
---

# Introduction

The vk-scaler component is used to ensure optimal auto scaling capability of Container Service for Kubernetes \(ACK\) clusters. You can deploy the component in an ACK cluster or in an on-premises Kubernetes cluster. This topic describes how to use vk-scaler to create a pod.

The vk-scaler component can be used to run pods as serverless container groups in Elastic Container Instance \(ECI\). The pay-as-you-go billing method is supported. This ensures optimal auto scaling capability and avoids unnecessary resource usage. You do not need to manually configure the capacity for scaling. You can use vk-scaler to improve the efficiency of application deployment and auto scaling and minimize the computing cost in multiple scenarios. These scenarios include Job tasks, continuous integration and delivery \(CI/CD\), Apache Spark big data computing, and online application auto scaling.

For more information about ECI pods, see [ECI configurations](/intl.en-US/User Guide for Serverless Kubernetes Clusters/ECI Pod management/Overview.md).

![vk](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/7166194061/p168908.png)

## Use vk-scaler to create a pod

1.  Set permissions.

    When you register an external Kubernetes cluster and before you install the vk-scaler component, set the AccessKey information to grant the registered cluster permissions to access Alibaba Cloud resources. Before you set the AccessKey information, create a RAM user and grant the RAM user permissions to access Alibaba Cloud resources.

    1.  Create a RAM user.

        For more information, see [Create a RAM user](/intl.en-US/RAM User Management/Create a RAM user.md).

    2.  Create a policy.

        For more information, see [Create a custom policy](/intl.en-US/Policy Management/Custom policies/Create a custom policy.md).

        The following example shows the permission policies of vk-scaler.

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

    3.  Grant permissions to the RAM user.

        For more information, see [Grant permissions to a RAM user](/intl.en-US/RAM User Management/Grant permissions to a RAM user.md).

    4.  Set the AccessKey information for the RAM user.

        For more information, see [Obtain an AccessKey pair]().

        **Note:**

        Run the following command to create the secret for vk-scaler:

        ```
        kubectl create nskubectl -n kube-system create secret generic alibaba-addon-secret --from-literal='access-key-id=<your access key id>' --from-literal='access-key-secret=<your access key secret>'
        ```

        Replace `<your access key id>` and `<your access key secret>` in the command with your AccessKey information.

2.  Install the vk-scaler component.

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane, click Clusters. On the Clusters page, find the cluster that you want to manage, and choose **More** \> **Manage System Components** in the **Actions** column of the cluster.

    3.  You are redirected to the Add-ons page. Find the **vk-scaler** component and click **Install** for the component.

3.  Create ECI pods.

    vk-scaler allows you to create ECI pods by using one of the following methods:

    -   Add the label eci=true to the pods.

        ```
        kubectl run nginx --image nginx -l eci=true
        ```

    -   Add the label eci=true to the namespace.

        ```
        kubectl create ns vk
        
        kubectl label namespace vk eci=true
        
        kubectl -n vk run nginx --image nginx
        ```

    **Note:**

    The ack-virtual-node and ack-virtual-kubelet-autoscaler components are no longer updated and maintained. Before you uninstall both components, migrate the ECI pods to the nodes where vk-sclaler is installed.

    Compared with ack-virtual-node, vk-scaler is more compatible with pods. It also provides more efficient auto scaling.


**Related topics**  


[Release notes]()

