---
keyword: [basic information, connection information, cluster logs, cluster time zone]
---

# View cluster information

Container Service for Kubernetes \(ACK\) provides an overview page for each cluster in the ACK console. You can view the statuses of applications and components, and check the monitoring information about the computing resources. This allows you to understand the health status of the cluster. On the overview page, you can also view the basic information, connection information, computing resources, logs, and time zone of the cluster.

## Check the cluster overview

On the overview page of an ACK cluster, you can view the statuses of applications, nodes, and components in the cluster. You can also view the monitoring information about the computing resources.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  Click the **Overview** tab to go to the overview page of the cluster.

5.  Select the namespace that you want to check. You can view the diagrams on the page to check the statuses of applications and components in the namespace. You can also view the monitoring charts of the computing resources in the namespace.

    -   **Application Status**: This section displays diagrams that show the statuses of the Deployments, pods, and StatefulSets that are running in the selected namespace. The green color indicates that applications are running as normal. The yellow color indicates that exceptions have occured.
    -   **Node Status**: This section displays a diagram that shows the statuses of the nodes in the cluster.
    -   **Component Status**: In most cases, cluster components are deployed in the kube-system namespace. Core components, such as kube-scheduler, Cloud Controller Manager \(CCM\), and etcd, are also deployed in this namespace.
    -   **Monitoring**: This section displays the monitoring charts of CPU and memory resources. The CPU utilization is measured in cores and is accurate to three decimal places. The smallest unit is millicores. A millicore is one-thousandth of a core. The memory utilization is measured in GiB and is accurate to three decimal places. For more information, see [Meaning of CPU](https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/#meaning-of-cpu) and [Meaning of memory](https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/#meaning-of-memory).
    -   **Events**: This section displays events in the cluster. For example, warnings and errors.

## View basic information

On the **Basic Information** tab, you can view the cluster ID, region, API server endpoint, and network information.

## View connection information

On the **Connection Information** tab, you can obtain the kubeconfig file that is used to connect to the cluster over the Internet or the internal network. You can also use kubectl to connect to the cluster based on the content in the kubeconfig file.

## View cluster resources

On the **Cluster Resources** tab, you can view the cloud resources that are used by the cluster. To check a resource in the corresponding console, click the resource ID.

**Note:** The cluster resources are managed by ACK. We recommend that you do not delete or modify these resources. Otherwise, exceptions may occur and applications that run in the cluster may also be affected.

## View cluster logs

You can view cluster logs by using the following methods:

-   Method 1: View cluster logs through the **Cluster Information** menu.

    In the left-side navigation pane of the cluster details page, click Cluster Information. Click the **Cluster Logs** tab to view the cluster logs.

-   Method 2: View cluster logs through the **Operations** menu.

    In the left-side navigation pane of the cluster details page, choose **Operations** \> **Log Center**. On the Log Center page, click the **Cluster Logs** tab to view the cluster logs.


## View the time zone of a cluster

You can view the time zone of a cluster and the time zone of a worker node.

-   To view the time zone of a cluster, connect to the cluster by using kubectl and run the following command:

    ```
    kubectl get configmap -n kube-system ack-cluster-profile -o yaml | grep timezone
    ```

    Sample output:

    ```
    timezone: Asia/Shanghai
    ```

-   To view the time zone of a worker node, connect to the worker node by using SSH and run the following command:

    ```
    ls -l /etc/localtime
    ```

    Sample output:

    ```
    lrwxrwxrwx 1 root root 30 Sep 30 18:44 /etc/localtime -> /usr/share/zoneinfo/Asia/Shanghai
    ```


