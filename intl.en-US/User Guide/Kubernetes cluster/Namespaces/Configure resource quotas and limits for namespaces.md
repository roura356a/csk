# Configure resource quotas and limits for namespaces {#task_sxr_qvx_b2b .task}

You can configure resource quotas and limits for namespaces through the Container Services console.

-   You have created an Kubernetes cluster. For more information, see [Create a Kubernetes cluster](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#).
-   You have created a namespace **test**. For more information, see [Create a namespace](reseller.en-US/User Guide/Kubernetes cluster/Namespaces/Create a namespace.md#).
-   You have connected to the master node address of the cluster. For more information, see [Connect to a Kubernetes cluster by using kubectl](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Connect to a Kubernetes cluster by using kubectl.md#).

By default, a running pod can use the CPU and memory of nodes unlimitedly, which means any pod can use the computing resources of the cluster unlimitedly, and the pods of a namespace may use up the cluster resources.

One of the important functions of namespaces is to act as a virtual cluster for multiple purposes and meeting the requirements of multiple users. Therefore, configuring the resource quotas for a namespace is a kind of best practice.

You can configure the resource quotas for a namespace, including CPU, memory, and number of pods. For more information, see [Resource Quotas](https://kubernetes.io/docs/concepts/policy/resource-quotas/).

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs). 
2.  Under Kubernetes, click **Clusters** \> **Namespace**. Select a cluster and click **ResourceQuota and LimitRange** on the right of the namespace **test**. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/18855/153958369010706_en-US.png)

3.  On the ResourceQuota and LimitRange dialog box, you can quickly set resource quotas and default resource limits. 

    **Note:** After setting the CPU/memory quota \(ResourceQuota\) for the namespace, you must specify the CPU/memory resource limit when configuring Pods, or configure the default resource limit \(LimitRange\) for the namespace. For details, please see [Resource Quotas](https://kubernetes.io/docs/concepts/policy/resource-quotas/?spm=5176.2020520152.0.0.1b5c16ddobysw3).

    1.  You can configure resource quotas for the namespace. 

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/18855/153958369010707_en-US.png)

    2.  You can control the container overhead by setting resource limits and resource requests for containers under this namespace. For more information, see [https://kubernetes.io//memory-default-namespace/](https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/memory-default-namespace/). 

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/18855/153958369011268_en-US.png)

4.  You have configured resource quotas and limits for the namespace. Connect to the master node connection address and execute the following commands to view the resources of the namespace: 

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


