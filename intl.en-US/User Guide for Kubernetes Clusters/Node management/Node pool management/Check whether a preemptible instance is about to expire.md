---
keyword: [check whether an instance is about to expire, check whether a preemptible instance is about to expire]
---

# Check whether a preemptible instance is about to expire

To avoid the unexpected expiration and release of preemptible instances, Container Service for Kubernetes \(ACK\) provides the ack-node-problem-detector component to send you notifications when preemptible instances are about to be released. This topic describes how to check whether a preemptible instance is about to expire.

A Kubernetes cluster is created and installed with the latest version of ack-node-problem-detector. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).

-   If you choose to create a new cluster, make sure that **Install node-problem-detector and Create Event Center** is selected.
-   If you choose to use an existing cluster, make sure that the latest version of ack-node-problem-detector is installed. For more information, see [Manage system components](/intl.en-US/User Guide for Kubernetes Clusters/Component/Manage system components.md).

In ACK clusters, Elastic Compute Service \(ECS\) instances are used as nodes to host services in the clusters. If you specify preemptible instance or subscription as the billing method when you create an ECS instance in a cluster, the instance will be automatically released at the specified time of expiration. When the instance is automatically released, if pod eviction, node draining, or node replacement is not completed in advance, services that run on the instance may be interrupted. If the instance of a master node is released, cluster-level exceptions may occur. To prevent such issues caused by the automatic release of preemptible instances, ACK uses ack-node-problem-detector to send you notifications when preemptible instances are about to be released.

## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Nodes** \> **Nodes**.

5.  On the Nodes page, find the node that you want to check and click the node name or choose **More** \> **Details** in the **Actions** column.

6.  On the node details page, check the status of the InstanceExpired condition.

    In the **Status** section, check the status of the InstanceExpired condition.

    ![Whether the instance is about to expire](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/4578840361/p302105.png)

    The following table describes the states of InstanceExpired condition.

    |State of InstanceExpired|Description|
    |------------------------|-----------|
    |True|If the **InstanceExpired** condition is in the True state and the **content** is **InstanceToBeTerminated**, it indicates that the preemptible instance is about to expire and be released.|
    |False|If the **InstanceExpired** condition is in the False state and the **content** is **InstanceNotToBeTerminated**, it indicates that the preemptible instance is not about to expire.|
    |Unknown|This state indicates that an error occurred on ack-node-problem-detector. [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).|

    If the InstanceExpired condition is in the **True** state, an event is generated in the **Events** section, as shown in the following figure.

    ![Instance expiry event](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/4578840361/p302240.png)


If the InstanceExpired condition is in the True state, it indicates that the preemptible instance is about to expire and be released. To prevent applications that run on this node from being interrupted, schedule the applications to other nodes. For more information, see [Schedule pods to specific nodes](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Application Scheduling Deployment/Schedule pods to specific nodes.md).

