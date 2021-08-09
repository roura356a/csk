---
keyword: [bind an EIP, EIP is not bound during cluster creation, access the Kubernetes API server over the Internet]
---

# Access the Kubernetes API server over the Internet

You can use an elastic IP address \(EIP\) to expose the API server for a cluster of Container Service for Kubernetes \(ACK\). After you perform this operation, you can access the API server of the ACK cluster over the Internet. You can bind an EIP to the Kubernetes API server during or after the cluster creation. This topic describes how to bind an EIP to an API server in the preceding ways.

## Bind an EIP to the Kubernetes API server when you create an ACK cluster

When you create an ACK cluster, select **Expose API Server with EIP**. For information about how to create an ACK cluster, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).

![EIP](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/8435359951/p103507.png)

## Bind an EIP to the Kubernetes API server after an ACK cluster is created

If you do not select Expose API Server with EIP when you create an ACK cluster, you can perform the following steps to bind an EIP to the Kubernetes API server of the cluster:

**Note:** Only ACK Standard clusters allow you to bind an EIP to the Kubernetes API server after the cluster is created.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  In the left-side navigation pane of the ACK console, choose **Clusters** \> **Clusters**.

4.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

5.  On the Clusters page, find the cluster to which you want to bind an EIP and click **Manage** in the **Actions** column.

6.  On the details page, click the **Basic Information** tab. In the **Cluster Information** section, click **Bind EIP**.

    ![Bind an EIP](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/8435359951/p103524.png)


After an EIP is bound to the Kubernetes API server, you can change or unbind the EIP.

![Change an EIP](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/8435359951/p103535.png)

**Note:** You can **change** or **unbind** the EIP for only ACK Standard clusters

