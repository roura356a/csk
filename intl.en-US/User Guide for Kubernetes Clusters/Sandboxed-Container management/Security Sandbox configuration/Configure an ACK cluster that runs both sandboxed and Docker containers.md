# Configure an ACK cluster that runs both sandboxed and Docker containers

This topic describes how to create a node pool of sandboxed containers and a node pool of Docker containers for a cluster of Container Service for Kubernetes \(ACK\). ACK allows you to create node pools of different container runtime types for a cluster. However, all nodes in a node pool must use the same type of container runtime.

An ACK cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).

**Notice:** The ACK cluster must meet the following requirements:

-   The ACK version must be 1.14.6 aliyun.1 or later.
-   The network plug-in must be Flannel or Terway. Terway must run in One ENI for Multi-Pod mode.
-   The volume plug-in must be CSI 1.14.8.39-0d749258-aliyun or later. You cannot use Flexvolume.
-   The Logtail version must be 0.16.34.2-f6647154-aliyun or later.

## Notes

-   By default, you can deploy up to 100 nodes in an ACK cluster. To increase the quota of nodes, [submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).
-   Before you add an existing Elastic Compute Service \(ECS\) instance deployed in a virtual private cloud \(VPC\), make sure that an elastic IP address \(EIP\) is attached to the ECS instance, or a Network Address Translation \(NAT\) gateway is created in the same VPC. In addition, you must make sure that the related node can access the public network. Otherwise, you fail to add the ECS instance.

## Create a node pool that runs Docker containers

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster in which you want to create a node pool and click **Node Pools** in the **Actions** column.

4.  On the **Node Pools** page, click **Create Node Pool** and set the parameters.

    For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed Kubernetes cluster.md). The following table lists the parameters.

    |Parameter|Description|
    |---------|-----------|
    |**Container Runtime**|Select **Docker**. This specifies that all containers in the node pool are Docker containers.|
    |**Quantity**|Specify the initial number of nodes in the node pool. If you do not need to create nodes, select 0.|
    |**Operating System**|Select an operating system for the nodes. Valid values: CentOS, AliyunLinux, and Windows.|

5.  Click **OK**.


## Create a node pool that runs sandboxed containers

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster in which you want to create a node pool and click **Node Pools** in the **Actions** column.

4.  On the Node Pools page, click **Create Node Pool** and set the parameters.

    For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed Kubernetes cluster.md). The following table lists the parameters.

    |Parameter|Description|
    |---------|-----------|
    |**Container Runtime**|Select **Sandboxed-Container**. This specifies that all containers in the node pool are sandboxed containers.|
    |**Quantity**|Specify the initial number of nodes in the node pool. If you do not need to create nodes, select 0.|
    |**Billing Method**|Select Subscription. Sandboxed containers run only on ECS Bare Metal instances and ECS Bare Metal instances are billed on a subscription basis.|
    |**Mount Data Disk**|You must mount a data disk of at least 200 GiB.|
    |**Operating System**|Select AliyunLinux. Sandboxed containers support only the AliyunLinux operating system.|

5.  Click **OK**.


-   Check the status of the created node pool on the **Node Pools** page. If the status of the created node pool displays **Activated**, the node pool is created.
-   To view detailed information about the nodes in the node pool, connect to the ACK cluster where the node pool is deployed.
    1.  On the **Node Pools** page, click the name of the newly created node pool. In the **Node Pool Information** section, find and record the **ID of the node pool**.

        ![Node ID](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0632404061/p127968.png)

    2.  Connect to the ACK cluster where the node pool is deployed. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md).
    3.  Run the following command to query the name of about a specified node:

        ```
        kubectl get node --show-labels | grep -E "${node pool ID}|${node pool ID}"
        ```

    4.  Run the following command to query detailed information about a specified node:

        ```
        kubectl get node -o wide | grep -E "${node name} {node name}"
        ```


