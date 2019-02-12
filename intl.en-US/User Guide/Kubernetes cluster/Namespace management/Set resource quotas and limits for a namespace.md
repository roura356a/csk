# Set resource quotas and limits for a namespace {#task_sxr_qvx_b2b .task}

This topic describes how to set resource quotas and limits for a namespace through the Container Services console.

-   You have created a Kubernetes cluster. For more information, see [Create a Kubernetes cluster](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#).
-   You have created a namespace. In this topic, a namespace named **test** is used. For more information, see [Create a namespace](reseller.en-US/User Guide/Kubernetes cluster/Namespace management/Create a namespace.md#).
-   You have connected to the Master node of the cluster. For more information, see [Connect to a Kubernetes cluster by using kubectl](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Connect to a Kubernetes cluster by using kubectl.md#).

By default, a running pod uses the CPU and memory resources of nodes without limit. That is, any pod can use the computing resources of the cluster without restraint. Therefore, pods of a namespace may deplete the cluster resources.

Namespaces can be used as virtual clusters to serve multiple users. Therefore, setting resource quotas for a namespace is regarded as a best practice.

For a namespace, you can set the quotas of resources, such as CPU, memory, and number of pods. For more information, see [Resource quotas](https://kubernetes.io/docs/concepts/policy/resource-quotas/).

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs). 
2.  In the left-side navigation pane under Kubernetes, choose **Clusters** \> **Namespace**. Select the target cluster and click **ResourceQuota and LimitRange** on the right of the **test** namespace. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/18855/154996471810706_en-US.png)

3.  In the displayed dialog box, set resource quotas and default resource limits. 

    **Note:** After setting CPU/memory quotas for a namespace, you must specify CPU/memory resource limits or set the default resource limits for the namespace when creating a pod. For more information, see [Resource quotas](https://kubernetes.io/docs/concepts/policy/resource-quotas/?spm=5176.2020520152.0.0.1b5c16ddobysw3).

    1.  Set resource quotas for the namespace. 

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/18855/154996471810707_en-US.png)

    2.  To control the amount of resources consumed by containers, set resource limits and resource requests for containers in this namespace. For more information, see [https://kubernetes.io//memory-default-namespace/](https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/memory-default-namespace/). 

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/18855/154996471811268_en-US.png)

4.  Connect to the Master node and then run the following commands to view the resources of the **test** namespace: 

    ```
    
    #kubectl get limitrange,ResourceQuota -n test
    NAME AGE
    limitrange/limits 8m
    
    NAME AGE
    resourcequota/quota 8m
    
    
    # kubectl describe limitrange/limits resourcequota/quota -n test
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


