---
keyword: [Knative system components, Serving, auto scaling]
---

# Upgrade a Knative component

Container Service for Kubernetes \(ACK\) allows you to upgrade Knative components. You can use Knative Serving to manage serverless applications. This enables auto scaling of pods based on Knative events and user requests. If no workload is processed, the number of pods is scaled to zero. This topic describes how to upgrade Knative Serving.

-   Knative Serving is deployed. For more information, see [Deploy a Knative component](/intl.en-US/User Guide for Kubernetes Clusters/Knative/Manage Knative components/Deploy a Knative component.md).
-   The Kubernetes version is later than 1.15.0.
-   Only Knative Serving 0.11.0 can be upgraded.

**Note:** We recommend that you upgrade Knative Serving during off-peak hours.

You can upgrade Knative Serving to V0.14.0.

Knative Serving 0.14.0 provides the following features:

-   By default, a minimum of 20 latest Knative Service revisions are retained. The default retention period is 48 hours.
-   Highly reliable subcomponents are provided, such as controller and hpaautoscaler.

## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Applications** \> **Knative**.

5.  On the Components tab, find **Serving** in the **Core Component** section and click **Upgrade**.

    ![Knative Serving](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9955359951/p127321.png)

    After Knative Serving is upgraded, the result is displayed, as shown in the following figure.

    ![Upgrade result](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9955359951/p127322.png)


