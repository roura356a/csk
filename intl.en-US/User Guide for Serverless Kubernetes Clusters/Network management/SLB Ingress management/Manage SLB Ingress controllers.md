---
keyword: Install an SLB Ingress controller
---

# Manage SLB Ingress controllers

Alibaba Cloud serverless Kubernetes \(ASK\) clusters provide managed Server Load Balancer \(SLB\) Ingress controllers that implement Layer 7 forwarding rules based on SLB. This topic describes how to install an SLB Ingress controller in an ASK cluster and uninstall it from the cluster.

## Install an SLB Ingress controller

**Method 1: Install the SLB Ingress controller when you create a cluster**

When you create an ASK cluster, select **SLB Ingress** in the **Ingress** section. For more information, see [Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md).

![13](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4119918161/p245467.png)

**Method 2: Install the SLB Ingress controller on the Add-ons page**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Operations** \> **Add-ons**.

5.  Click the **Others** tab, find **SLB Ingress Controller**, and then click **Install**.


## Uninstall the SLB Ingress controller

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Operations** \> **Add-ons**.

5.  Click the **Others** tab, find SLB Ingress Controller, and then click **Uninstall**.


**Delete the Service associated with the SLB instance**

1.  In the left-side navigation pane of the details page, choose **Network** \> **Services**.

2.  On the **Services** page, select **kube-system** from the **Namespace** drop-down list.

3.  Find the **alb-ingress-lb** Service and click **Delete** in the Actions column.

4.  In the **Note** message, click **Confirm**.


