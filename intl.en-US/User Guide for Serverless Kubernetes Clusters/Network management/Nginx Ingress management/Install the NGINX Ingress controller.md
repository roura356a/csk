---
keyword: Install NGINX Ingress controller
---

# Install the NGINX Ingress controller

Alibaba Cloud serverless Kubernetes \(ASK\) clusters provide the NGINX Ingress controller, which is optimized based on the open source NGINX Ingress controller. This topic describes how to install the NGINX Ingress controller in an ASK cluster.

## Procedure

**Method 1: Install the NGINX Ingress controller when you create a cluster**

When you create an ASK cluster, select **Nginx Ingress** in the **Ingress** section. For more information, see [Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md).

**Note:** To install the NGINX Ingress controller, the following two conditions must be met:

-   Two elastic container instances that have at least 2 CPU cores and 4 GiB of memory each are used to deploy services.
-   One Server Load Balancer \(SLB\) instance is created. You can set the network type of the SLB instance to Public Network or Internal Network and select the SLB instance specifications based on your business requirements.

![S8](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3712548161/p245306.png)

**Method 2: Install the NGINX Ingress controller on the Add-ons page**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Operations** \> **Add-ons**.

5.  Click the **Networking** tab. Select **Nginx Ingress Controller** and click **Install**.


