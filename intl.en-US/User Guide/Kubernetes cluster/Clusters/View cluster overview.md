# View cluster overview {#task_qfb_4c2_vdb .task}

You can view the application status, component status, and resource monitoring charts on the Overview page of Alibaba Cloud Container Service Kubernetes clusters, which allows you to quickly understand the health status of clusters.

1.  Log on to the [Container Service console](https://cs.console.aliyun.com). 
2.  Under Kubernetesu, click **Overview** in the left navigation bar to enter the Kubernetes cluster overview page. 
3.  Select the cluster and namespace from the Clusters and Namespace drop-down lists. You can view the application status, component status, and resource monitoring charts. 

    -   **Application status**: The status of deployments, pods, and replica sets that are currently running. Green indicates the normal status and orange indicates an exception.
    -   **Node status**: Displays the node status of the current cluster.
    -   **Component status**: The components of Kubernetes clusters are generally deployed under the kube-system namespace, including the core components such as scheduler, controller-manager, and etcd.
    -   **Resource monitor**: Provides the monitoring charts of CPU and memory.  CPU is measured in cores and is accurate to three decimal places. The minimum unit is millicores, that is, one thousandth of one core. Memory is measured in G and is accurate to three decimal places.  For more information, see [Meaning of CPU](https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/#meaning-of-cpu) and [Meaning of memory](https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/#meaning-of-memory).
    -   **Event**: Displays event information of the cluster, such as warnings and error events.
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6888/15329313061684_en-US.png)


