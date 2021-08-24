---
keyword: [associate an EIP, EIP is not associated during cluster creation, public access to API server]
---

# Control public access to the API server of a cluster

Container Service for Kubernetes \(ACK\) allows you to use an elastic IP address \(EIP\) to expose the API server of a cluster. After you use an EIP to expose the API server of a cluster, the API server can be accessed over the Internet. You can associate an EIP with the API server of a cluster when you create the cluster or after the cluster is created. This topic describes how to control public access to the API server of a cluster by associating an EIP with or disassociating an EIP from the API server.

## Associate an EIP with the API server when you create a cluster

When you create a cluster, select **Expose API Server with EIP** to enable public access to the API server of the cluster. For more information about how to create a cluster, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).

![EIP](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/8435359951/p103507.png)

## Associate an EIP with the API server after a cluster is created

If you do not select Expose API Server with EIP when you create a cluster, you can perform the following steps to associate an EIP with the API server after the cluster is created.

**Note:** You can associate an EIP with the API server after the cluster is created only if the cluster is a standard Kubernetes cluster, professional Kubernetes cluster, or serverless Kubernetes \(ASK\) cluster.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  On the details page of the cluster, click the **Basic Information** tab. In the **Cluster Information** section, click **EIP**.

    ![Associate an EIP](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/8435359951/p103524.png)

5.  In the dialog box that appears, select an EIP and click **OK**.

    After the EIP is associated with the API server, a public IP address appears on the right side of the API Server Public Endpoint field.


## Disassociate or change an EIP

You can disassociate or change the EIP that is associated with the API server of a cluster. After you disassociate the EIP from the API server of a cluster, the API server cannot be accessed over the Internet.

![Change an EIP](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/8435359951/p103535.png)

**Note:** You can **change** and **disassociate** an EIP that is associated with the API server of a cluster only if the cluster is a standard Kubernetes cluster, professional Kubernetes cluster, or ASK cluster.

