---
keyword: Install the ALB Ingress controller
---

# Manage the ALB Ingress controller

Serverless Kubernetes \(ASK\) clusters provide the managed Application Load Balancer \(ALB\) Ingress controller that implements Layer 7 forwarding rules based on ALB. This topic describes how to install the ALB Ingress controller in an ASK cluster and uninstall it from the cluster.

## Install the ALB Ingress controller

**Method 1: Install the ALB Ingress controller when you create an ASK cluster**

When you create an ASK cluster, select **ALB Ingress** in the **Ingress** section. For more information, see [Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md).

![12](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/4662058261/p298251.png)

**Method 2: Install the ALB Ingress controller on the Add-ons page**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Operations** \> **Add-ons**.

5.  Click the **Others** tab, find **ALB Ingress Controller**, and then click **Install**.


## Uninstall the ALB Ingress controller

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Operations** \> **Add-ons**.

5.  Click the **Others** tab, find ALB Ingress Controller, and then click **Uninstall**.


