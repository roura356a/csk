---
keyword: [basic information, connection information, cluster logs, cluster time zone]
---

# View cluster information

Container Service for Kubernetes \(ACK\) provides the Overview tab that helps you track the cluster health status. The Overview tab displays cluster information such as the application status, component status, and resource monitoring charts. You can also view the following cluster information: basic information, connection information, cluster resources, cluster logs, and cluster time zone.

## View cluster overview

On the Overview tab, you can view the application status, node status, component status, and resource monitoring charts.

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Overview** to view the cluster overview.

3.  Select the cluster that you want to view and the namespace where the cluster resides. You can view the application status, component status, and resource monitoring charts.

    -   **Application Status**: displays the statuses of the deployments, pods, and replica sets that are running in the cluster. Green indicates a normal state and yellow indicates an exception.
    -   **Node Status**: displays the statuses of the nodes in the cluster.
    -   **Component Status**: Cluster components are deployed in the kube-system namespace. Core components include the scheduler, controller-manager, and etcd.
    -   **Monitoring**: displays the monitoring charts of CPU and memory resources. The CPU utilization is measured in cores and is accurate to three decimal places. The smallest unit is millicores. A millicore is a thousandth of a core. The memory usage is measured in GiB and is accurate to three decimal places. For more information, see [Meaning of CPU](https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/#meaning-of-cpu) and [Meaning of memory](https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/#meaning-of-memory).
    -   **Events**: displays events in the cluster, such as warnings and errors.
    ![Cluster overview](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4235359951/p10836.png)


## View basic information

On the **Basic Information** tab, you can view the cluster ID, region, API server endpoint, and network information.

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters** or **Serverless Clusters** based on the cluster type. On the page that appears, click the name of the cluster that you want to view.

3.  Click the **Basic Information** tab.


## View connection information

On the **Connection Information** tab, you can obtain a kubeconfig file that you can use to connect to the cluster over the Internet or an internal network. After you configure the kubeconfig file, you can use kubectl to connect to the cluster.

## View cluster resources

On the **Cluster Resources** tab, you can view the cloud resources that are used by the cluster. To go to the console that corresponds to a resource, click the resource ID.

**Note:** The cluster resources are managed by ACK. We recommend that you do not delete or modify these resources. Otherwise, exceptions may occur and applications running in the cluster may also be affected.

## View cluster logs

On the **Cluster Logs** tab, you can view the cluster logs.

## View the time zone of a cluster

You can view the system time zone and the time zone of worker nodes.

-   Use kubectl to connect to the cluster and run the following command to view the system time zone:

    ```
    kubectl get configmap -n kube-system ack-cluster-profile -o yaml | grep timezone
    ```

    Sample command output:

    ```
    timezone: Asia/Shanghai
    ```

-   Use SSH to log on to a worker node and run the following command to view the time zone of the worker node:

    ```
    ls -l /etc/localtime
    ```

    Sample command output:

    ```
    lrwxrwxrwx 1 root root 30 Sep 30 18:44 /etc/localtime -> /usr/share/zoneinfo/Asia/Shanghai
    ```


