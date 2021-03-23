---
keyword: [pod, pods, smallest deployable computing unit, pod logs]
---

# View pods in ACK clusters

Pods are the smallest computing units that you can create, deploy, and manage in Kubernetes. A pod is a runtime instance where an independent application runs. Each pod contains one or more containers that are tightly coupled. You can view and manage pods in a Container Service for Kubernetes \(ACK\) cluster in the ACK console.

## View details of pods

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

3.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

4.  View details of pods

    -   Click the **Pods** tab. On the Pods tab, find the pod that you want to view, and click **View Details** on the right side.
    -   Click the tab of the workload type that you want to manage. On the tab of the selected workload type, find and click the application that you want to manage or click **Details** on the right side.
    You can also view pods in the corresponding Services. In the left-side navigation pane, click **Services**. On the Services page, find and click the Service that you want to manage. In the workload section, click the application that you want to manage. On the application page, click the Pods tab to view the pods.

    **Note:** You can modify or delete pods on the Pods tab. We recommend that you use Deployments to manage pods if they are created by Deployments.


## View pod logs

You can view pod logs by using the following methods:

-   Navigate to the **Pods** tab, find the pod that you want to manage, and click **Logs** on the right side to view the pod logs.
-   Navigate to the tab of the corresponding workload type and find the application that you want to manage. In the **Actions** column, choose **More** \> **Logs**.

