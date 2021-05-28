---
keyword: [virtual-kubelet-autoscaler, install virtual-kubelet-autoscaler]
---

# Install virtual-kubelet-autoscaler

Alibaba Cloud provides the virtual-kubelet-autoscaler plug-in for Container Service for Kubernetes \(ACK\) clusters. If a pod fails to be scheduled to Elastic Compute Service \(ECS\) nodes in a cluster \(for example, due to insufficient node resources\), virtual-kubelet-autoscaler reschedules the pod to a virtual node deployed on an elastic container instance.

-   A managed Kubernetes cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   A virtual node is deployed in the cluster. For more information, see [Deploy the virtual node controller and use it to create Elastic Container Instance-based pods](/intl.en-US/User Guide for Kubernetes Clusters/Virtual nodes and ECI/Deploy the virtual node controller and use it to create Elastic Container Instance-based pods.md).

## Install virtual-kubelet-autoscaler

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, choose **Marketplace** \> **App Catalog**.

3.  On the **App Catalog** page, click the **Alibaba Cloud Apps** tab, and find and click **ack-virtual-kubelet-autoscaler**.

    You can search for ack-virtual-kubelet-autoscaler on the **Alibaba Cloud Apps** tab. Enter **ack-virtual-kubelet-autoscaler** into the Name search box and click the search icon. You can also enter a keyword to perform a fuzzy match.

4.  On the **App Catalog - ack-virtual-kubelet-autoscaler** page, select the created cluster in the **Deploy** section and click **Create**.

    ![Create an application](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3420793061/p132495.png)


1.  On the Clusters page, find the cluster that you want to manage, and click the name of the cluster or click **Details** in the **Actions** column.
2.  In the left-side navigation pane of the cluster details page, choose **Applications** \> **Helm**. You can find the ack-virtual-kubelet-autoscaler application.

