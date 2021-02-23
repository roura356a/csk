---
keyword: [Knative system components, Serving, automatic scaling]
---

# Upgrade a Knative component

Container Service for Kubernetes \(ACK\) allows you to upgrade Knative components. You can use Knative Serving to manage serverless applications. This enables automatic scaling for pods where serverless applications are deployed based on Knative events and user requests. If no workload is processed, the number of pods is scaled to zero. This topic describes how to upgrade Knative Serving.

-   Knative Serving is deployed. For more information, see [Deploy a Knative component](/intl.en-US/User Guide for Kubernetes Clusters/Knative management/Manage Knative components/Deploy a Knative component.md).
-   The Kubernetes version is 1.15.0 or later.
-   Only Knative Serving 0.11.0 supports component upgrades.

**Note:** We recommend that you upgrade Knative Serving during off-peak hours.

You can upgrade Knative Serving to version 0.14.0.

Knative Serving 0.14.0 provides the following new features:

-   By default, a minimum of 20 latest Knative Service revisions are retained. The default retention period is 48 hours.
-   Supports highly reliable subcomponents, such as controllers and Horizontal Pod Autoscaler \(HPA\).

## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster, or click **Details** in the **Actions** column. The details page of the cluster appears.

3.  In the left-side navigation pane of the details page, choose **Applications** \> **Knative**.

4.  On the Components tab, find the **Core Components** section, select the **Serving** component, and then click **Upgrade**.

    ![Knative Serving](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9955359951/p127321.png)

    After Knative Serving is upgraded, the result is displayed, as shown in the following figure.

    ![Result](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9955359951/p127322.png)


