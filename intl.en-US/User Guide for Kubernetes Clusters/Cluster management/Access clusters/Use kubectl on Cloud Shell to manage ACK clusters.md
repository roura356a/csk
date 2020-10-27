---
keyword: [Cloud Shell, ACK clusters]
---

# Use kubectl on Cloud Shell to manage ACK clusters

This topic describes how to use kubectl on Cloud Shell to manage clusters of Container Service for Kubernetes \(ACK\) in the ACK console.

[Create a cluster of ACK Managed Edition](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a cluster of ACK Managed Edition.md).

To manage ACK clusters by using kubectl, you must install and set up the kubectl client. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to a cluster.md). You can also use kubectl on Cloud Shell in the ACK console to manage ACK clusters.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and choose **More** \> **Open Cloud Shell** in the **Actions** column.

    **Note:**

    After Cloud Shell is opened, perform the following steps:

    -   On the Authorization page, click **OK**. You will obtain an AccessKey pair for a temporary session.
    -   Click the ![cloudshell.png](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/9074183061/p141958.png) icon and select Mount File Storage. You can **create and mount** a Network Attached Storage \(NAS\) file system to the cluster or **skip** the process.
4.  Use kubectl on Cloud Shell in the ACK console to manage ACK clusters.

    **Note:** When you open Cloud Shell, Cloud Shell automatically reads the kubeconfig file of the cluster. Then, you can use kubectl to manage the cluster.

    ![Manage the cluster2](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/3435359951/p34727.png)


