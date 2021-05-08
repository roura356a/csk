---
keyword: [Cloud Shell, ASK clusters, ]
---

# Use kubectl on Cloud Shell to manage ASK clusters

This topic describes how to use kubectl on Cloud Shell to manage clusters of Container Service for Kubernetes \(ASK\) in the ACK console.

[Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md).

To manage ASK clusters by using kubectl, you must install and set up the kubectl client. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl to connect to an ACK cluster.md). You can also use kubectl on Cloud Shell in the ACK console to manage ASK clusters.

## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and choose **More** \> **Open Cloud Shell** in the **Actions** column.

    **Note:**

    After Cloud Shell is opened, perform the following steps:

    -   On the Authorization page, click **OK**. You will obtain an AccessKey pair for a temporary session.
    -   Click the ![cloudshell.png](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9074183061/p141958.png) icon and select Mount File Storage. You can **create and mount** a Network Attached Storage \(NAS\) file system to the cluster or **skip** the process.
4.  Use kubectl on Cloud Shell in the ACK console to manage ASK clusters.

    **Note:** When you open Cloud Shell, Cloud Shell automatically reads the kubeconfig file of the cluster. Then, you can use kubectl to manage the cluster.

    ![Manage the cluster2](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3435359951/p34727.png)


