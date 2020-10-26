# Set resource quotas and limits

You can set resource quotas and limits for a namespace in the Container Service for Kubernetes \(ACK\) console.

-   A cluster is created. For more information, see [Create a cluster of ACK Managed Edition](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a cluster of ACK Managed Edition.md).
-   A namespace is created. The following example is based on namespace test. For more information, see [Create a namespace](/intl.en-US/User Guide for Kubernetes Clusters/Namespace management/Create a namespace.md).
-   A master node of the cluster is connected. For more information, see [Use kubectl to connect to a cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to a cluster.md).

By default, a pod running on a cluster can use unbounded computing resources, including CPU and memory resources of nodes. In this case, pods in a namespace may monopolize all available resources of the cluster.

Namespaces act as virtual clusters for multiple purposes and requirements. Therefore, we recommend that you set resource quotas for namespaces.

You can set multiple resources quotas for a namespace, including CPU, memory, and pod resource quotas. For more information, see [Resource quotas](https://kubernetes.io/docs/concepts/policy/resource-quotas/).

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, click **Namespaces**.

5.  Find the namespace that you want to manage and click **Resource Quotas and Limits** in the Actions column.

    ![Resource quotas and limits (new)](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/8773662061/p146984.png)

6.  In the dialog box that appears, set resource quotas and default limit ranges.

    **Note:** Assume that you have set CPU or memory resource quotas for a namespace. When you create a pod, you must specify the CPU or memory limit for the pod or configure default limit ranges for the namespace. For more information, see [Resource quotas](https://kubernetes.io/docs/concepts/policy/resource-quotas/?spm=5176.2020520152.0.0.1b5c16ddobysw3).

    1.  Set resource quotas for the namespace.

        ![Set resource quotas](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/4555359951/p10707.png)

    2.  Set default limit ranges and requests for pods in the namespace. This limits resources consumed by pods. For more information, see [Configure default memory requests and limits for a namespace](https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/memory-default-namespace/).

        ![Set limit ranges](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/4555359951/p11268.png)

7.  Connect to a master node and query the resource quotas and limits of the namespace.

    -   Run the following command to connect to a master node:

        ```
        kubectl get limitrange,ResourceQuota -n test                
        ```

        The command output appears:

        ```
        NAME AGE
        limitrange/limits 8m
        
        NAME AGE
        resourcequota/quota 8m
        ```

    -   Run the following command to query resource quotas and limits:

        ```
        kubectl describe limitrange/limits resourcequota/quota -n test
        ```

        The command output appears:

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


