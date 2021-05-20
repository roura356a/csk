---
keyword: [resource quotas and limits, namespaces]
---

# Set resource quotas and limits

You can set resource quotas and limits for a namespace in the Container Service for Kubernetes \(ACK\) console.

-   An ACK cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   A namespace is created. For more information, see [t18871.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Namespace and Quotas/Create a namespace.md).
-   You are connected to a master node of the cluster. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl to connect to an ACK cluster.md).

By default, pods that are in the running state can consume the CPU and memory resources of nodes without limit. In this case, pods in a namespace may exhaust the resources of the cluster.

Namespaces can be used as virtual clusters to serve multiple purposes. We recommend that you set resource quotas for namespaces.

You can set multiple resource quotas for a namespace, including CPU, memory, and pod resource quotas. For more information, see [Resource quotas](https://kubernetes.io/docs/concepts/policy/resource-quotas/).

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, click **Namespaces and Quotas**.

5.  Find the namespace that you want to manage and click **Resource Quotas and Limits** in the **Actions** column.

6.  In the Resource Quotas and Limits dialog box, set resource quotas and default resource limits.

    **Note:** Assume that you have set CPU or memory resource quotas for a namespace. When you create a pod, you must specify the CPU or memory limit for the pod or configure default limit ranges for the namespace. For more information, see [Resource quotas](https://kubernetes.io/docs/concepts/policy/resource-quotas/?spm=5176.2020520152.0.0.1b5c16ddobysw3).

    1.  You can set resource quotas for the namespace.

        ![Set resource quotas](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4555359951/p10707.png)

    2.  You can set resource limits and resource requests for containers in the namespace. This enables you to control the amount of resources consumed by the containers. For more information, see [Configure default memory requests and limits for a namespace](https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/memory-default-namespace/).

        ![Set resource requests](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4555359951/p11268.png)

7.  Connect to a master node of the cluster and view the resources in the namespace.

    -   Run the following command to query the resource quotas and limits:

        ```
        kubectl get limitrange,ResourceQuota -n test                
        ```

        Expected output:

        ```
        NAME AGE
        limitrange/limits 8m
        
        NAME AGE
        resourcequota/quota 8m
        ```

    -   Run the following command to query the resource quotas and limits:

        ```
        kubectl describe limitrange/limits resourcequota/quota -n test
        ```

        Expected output:

        ```
        Name: limits
        Namespace: test
        Type Resource Min Max Default Request Default Limit Max Limit/Request Ratio
        ---- -------- --- --- --------------- ------------- -----------------------
        Container cpu - - 100m 500m -
        Container memory - - 256Mi 512Mi -
        
        Name: quota
        Namespace: test
        Resource Used Hard
        -------- ---- ----
        configmaps 0 100
        limits.cpu 0 2
        limits.memory 0 4Gi
        persistentvolumeclaims 0 50
        pods 0 50
        requests.storage 0 1Ti
        secrets 1 10
        services 0 20
        services.loadbalancers 0 5
        ```


