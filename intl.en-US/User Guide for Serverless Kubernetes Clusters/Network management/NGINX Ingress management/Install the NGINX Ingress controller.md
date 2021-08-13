---
keyword: Install the NGINX Ingress controller
---

# Install the NGINX Ingress controller

Serverless Kubernetes \(ASK\) clusters provide the NGINX Ingress controller, which is optimized based on the open source NGINX Ingress controller. This topic describes how to install the NGINX Ingress controller in an ASK cluster.

## Procedure

**Method 1: Install the NGINX Ingress controller when you create a cluster**

When you create an ASK cluster, select **Install Ingress Controllers** in the **Ingress** section. For more information, see [Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md).

**Note:** To install the NGINX Ingress controller, make sure that the following two conditions are met:

-   Two elastic container instances that have 2 CPU cores and 4 GiB of memory each are used to deploy services.
-   A Server Load Balancer \(SLB\) instance is created. You can set the network type of the SLB instance to Public Network or Internal Network and select the SLB instance specifications based on your business requirements.

![S8](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/3712548161/p245306.png)

**Method 2: Install the NGINX Ingress controller on the Add-ons page**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Operations** \> **Add-ons**.

5.  Click the **Networking** tab. Find the **Nginx Ingress Controller** section and click **Install**.

6.  In the **Note** message, click **OK**.

    If **Installed** appears in the upper-right corner of the **Nginx Ingress Controller** section, it indicates that the NGINX Ingress controller is installed.

    ![1](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/3504378261/p295089.png)


