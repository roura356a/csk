---
keyword: [Knative system components, Serving, automatic scaling]
---

# Upgrade Knative Serving

You can use Knative Serving to manage serverless applications. This enables automatic scaling for pods where serverless applications are deployed based on Knative events and user requests. If no workload is processed, the number of pods is scaled to zero. This topic describes how to upgrade Knative Serving.

-   Knative Serving is deployed. For more information, see [Deploy components](/intl.en-US/User Guide for Kubernetes Clusters/Knative management/Manage Knative components/Deploy a Knative component.md).
-   The Kubernetes version is 1.15.0 or later.
-   Only Knative Serving 0.11.0 is supported for upgrades.

**Note:** We recommend that you upgrade Knative Serving during off-peak hours.

You can upgrade to Knative Serving 0.14.0.

Knative Serving 0.14.0 provides the following new features:

-   By default, a minimum of 20 latest Knative service revisions are retained. The default retention period is 48 hours.
-   Supports highly reliable subcomponents, such as controller and hpaautoscaler.

## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

3.  In the left-side navigation pane, choose **Knative** \> **Components**.

4.  Find the **Serving** component and click **Upgrade** in the Actions column.

    ![Knative Serving](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9955359951/p127321.png)

    After Knative Serving is upgraded, the result is displayed.

    ![Result](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9955359951/p127322.png)


