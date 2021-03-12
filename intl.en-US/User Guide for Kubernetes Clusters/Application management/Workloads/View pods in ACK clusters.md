---
keyword: [pods, pods, smallest deployable computing unit, pod logs]
---

# View pods in ACK clusters

Pods are the smallest computing units that you can create, deploy, and manage in Kubernetes. A pod is a runtime instance where an independent application runs. Each pod contains one or more containers that are tightly coupled. You can view and mange pods in a cluster of Alibaba Cloud Container Service for Kubernetes \(ACK\) in the ACK console.

## View pod details

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

3.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

4.  View pod details

    -   Click the **Pods** tab. On the Pods tab, find the target pod, and click **View Details** on the right side of the page.
    -   Click the tab of the target workload type. On the target tab, find and click the target application or click **Details** on the right side of the page.
    You can also view pods in the corresponding services. In the left-side navigation pane, click **Services**. On the Services page, find and click the target service. In the target workload section, click the target application. On the target application page, click the Pods tab to view the pods.

    **Note:** You can update or delete pods on the Pods tab. We recommend that you use Deployments to manage pods if they are created by Deployments.


## View pod logs

You can view pod logs by using the following methods:

-   Navigate to the **Pods** tab, find the target pod, and click **Logs** on the right side of the page to view the pod logs.
-   Navigate to the tab of the target workload type, find the target application, and select **Logs** from the **More** drop-down list in the Actions column to view the pod logs.

