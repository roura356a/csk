---
keyword: [resource quotas and limits, namespaces]
---

# Set resource quotas and limits

You can set resource quotas and limits for a namespace in the Container Service for Kubernetes \(ACK\) console.

-   An ACK cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   A namespace is created. For more information, see [Manage namespaces](/intl.en-US/User Guide for Kubernetes Clusters/Namespace and Quotas/Manage namespaces.md).
-   You are connected to the cluster by using kubectl. For more information, see [Connect to an ACK cluster by using kubectl](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to ACK clusters by using kubectl.md).

By default, pods that are in the running state can consume the CPU and memory resources of nodes without limit. In this case, pods in a namespace may exhaust the resources of the cluster.

Namespaces can be used as virtual clusters to serve multiple purposes. We recommend that you set resource quotas for namespaces.

You can set multiple resource quotas for a namespace, including CPU, memory, and pod quotas. For more information, see [Resource quotas](https://kubernetes.io/zh/docs/concepts/policy/resource-quotas/).

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, click **Namespaces and Quotas**.

5.  Find the namespace that you want to manage and click **Resource Quotas and Limits** in the **Actions** column.

6.  In the Resource Quotas and Limits dialog box, set resource quotas and default resource limits.

    **Note:** After you set CPU and memory quotas for a namespace, you must specify CPU and memory limits when you create a pod. You can also set the default resource limits for the namespace. For more information, see [Resource quotas](https://kubernetes.io/zh/docs/concepts/policy/resource-quotas/).

    1.  Set resource quotas for the namespace.

        ![Set resource quotas](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/4555359951/p10707.png)

    2.  Set resource limits and resource requests for containers in the namespace. This enables you to control the amount of resources consumed by the containers. For more information, see [Configure default memory requests and limits for a namespace](https://kubernetes.io/zh/docs/tasks/administer-cluster/manage-resources/memory-default-namespace/).

        ![Set resource requests](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/4555359951/p11268.png)


You can click **Resource Quotas and Limits** in the **Actions** column to view the resource limits that are set for the specified namespace.

