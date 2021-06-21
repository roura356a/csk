---
keyword: [cloud-native AI, monitoring, GPU resources]
---

# AI Dashboard

You can use the monitoring component provided by cloud-native AI to monitor the GPU resource usage in the cluster and the resource quota usage in different namespaces. This topic describes how to install and use the monitoring component provided by cloud-native AI.

-   The Kubernetes version of the ACK cluster is 1.18.8 or later.
-   The Arena component of V0.7.0 or later is installed. For more information, see [Install the latest version of Arena](/intl.en-US/Solutions/Deep learning solution/Preparations/Install the latest version of Arena.md).
-   The ARMS Prometheus component is installed. For more information, see [arms-prometheus](https://cs.console.aliyun.com/#/k8s/catalog/detail/incubator_ack-arms-prometheus).
-   The AI Dashboard component is installed. For more information, see [Deploy the cloud-native AI component set](/intl.en-US/Cloud-native AI user guide/Environment preparation/Deploy the cloud-native AI component set.md).

## Install the cloud-native AI component set

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, choose **Applications** \> **AI Project Acceleration \(Public Preview\)**.

5.  Install the monitoring component.

    -   If the cloud-native AI component set is installed:

        On the Cloud-native AI Component Set page, find the monitoring component **ack-arena-exporter** and click **Deploy** in the **Actions** column.

    -   If the cloud-native AI component set is not installed:

        On the Cloud-native AI Component Set page, click **Deploy**. In the **Monitoring** section, select **Monitoring Component** and click **Deploy Cloud-native AI Component Set**.


## View the GPU resource usage and resource quotas in different namespaces

**Note:** Before you use the monitoring component, you must install **ack-ai-dashboard** and configure access to the AI Dashboard page. For more information about how to install and access AI Dashboard, see [Deploy the cloud-native AI component set](/intl.en-US/Cloud-native AI user guide/Environment preparation/Deploy the cloud-native AI component set.md).

You can perform the following operations on the AI Dashboard page:

-   You can view information about resources in the cluster on the AI Dashboard page. You can view resource information such as the number of nodes, the number of running Jobs, and the GPU usage of the cluster.
-   In the upper-right corner of the AI Dashboard page, click **Nodes** to view information about the GPU resources on nodes.

    ![nodes](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2951579161/p240389.png)

-   In the upper-right corner of the AI Dashboard page, click **TrainingJobs** to view monitoring information about training jobs.
-   In the upper-right corner of the AI Dashboard page, click **Quota** to view the resource quota usage in each namespace of the cluster.

