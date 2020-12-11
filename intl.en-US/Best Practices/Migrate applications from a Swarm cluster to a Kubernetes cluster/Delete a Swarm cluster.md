# Delete a Swarm cluster

This topic describes how to delete a cluster of Container Service for Swarm that is no longer required.

## Check whether network traffic is routed to the Swarm cluster

1.  Check whether network traffic is sent to the Server Load Balancer \(SLB\) instance that is attached to the Swarm cluster.

    Before you delete a Swarm cluster, make sure that network traffic is no longer sent to the SLB instance that is attached to the Swarm cluster. SLB provides charts for you to monitor inbound traffic by instance or listener. Perform the following steps to view the charts:

    1.  Log on to the [SLB console](https://slbnext.console.aliyun.com/) console. On the Instances page, click the ID of the SLB instance that is attached to the Swarm cluster.

    2.  On the **Monitoring** tab, you can set forwarding rules and view the chart.

2.  Check whether network traffic is no longer forwarded to applications that are deployed in the Swarm cluster.

    We recommend that you monitor applications that are deployed in the Swarm cluster for a period of time and make sure that no network traffic is received before you delete the cluster. The swarm cluster may receive network traffic due to client version upgrades or Domain Name Service \(DNS\) caching. In this case, if you delete the Swarm cluster, your business may be interrupted.


## Delete the Swarm cluster

After you make sure that the related Kubernetes cluster is handling external requests as expected and no network traffic is received by the Swarm cluster, you can delete the Swarm cluster. Perform the following steps to delete the Swarm cluster:

1.  Back up the configurations of the Swarm cluster.

    To ensure that you can quickly roll back to the Swarm cluster in case an unknown error occurs in the related Kubernetes cluster, we recommend that you back up at least the following configuration items of the Swarm cluster:

    -   Node labels, network settings, volume settings, ConfigMap settings, and SLB configurations.
    -   Orchestration templates of all applications and log files.
2.  Release resources related to the Swarm cluster.

    **Note:** When you delete a Swarm cluster, all resources that are attached to the cluster are automatically released or deleted. For example, Elastic Compute Service \(ECS\) instances, SLB instances, applications, and Services. Exercise caution when you delete the Swarm cluster.

    Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com). In the left-side navigation pane, choose **Clusters** \> **Clusters**. Select the cluster that you want to delete, click **More** in the Actions column, and then select **Delete**. For more information, see [Delete an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Delete an ACK cluster.md).

3.  Release cloud disks.

    A cloud disk can be attached to only one ECS instance. If a cloud disk is used as a volume and needs to be migrated to the related Kubernetes cluster, you must stop the ECS instance that uses the cloud disk and migrate the instance to the Kubernetes cluster. For more information, see [Migrate cluster configurations](/intl.en-US/Best Practices/Migrate applications from a Swarm cluster to a Kubernetes cluster/Migrate cluster configurations.md).

    If a cloud disk is used for data storage and you do not need to migrate its data to the related Kubernetes cluster, you can create a new cloud disk for the Kubernetes cluster. After the Swarm cluster is deleted, the disk mounted to the cluster is not automatically released. The cloud disk changes to the **Unattached** state.

    **Note:** To avoid data loss, we recommend that you back up disk data before you release cloud disks.

    You can go to the ECS console to release cloud disks that are mounted to the Swarm cluster.

    1.  Log on to the [ECS console](https://ecs.console.aliyun.com/). In the left-side navigation pane, choose **Storage & Snapshots** \> **Disks**.

    2.  On the Disks page, select the cloud disk that you want to release and choose **More** \> **Release** in the Actions column.


