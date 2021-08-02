---
keyword: [node monitoring, Alibaba Cloud monitoring services]
---

# Monitor nodes

Container Service for Kubernetes \(ACK\) is integrated with monitoring services. You can view the monitoring information about Elastic Compute Service \(ECS\) instances in ACK clusters in the CloudMonitor console. This topic describes how to view the monitoring information about ECS instances in ACK clusters.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Nodes** \> **Nodes**.

5.  On the **Nodes** page, find the node that you want to monitor and click **Monitor** in the **Actions** column.

6.  You are then redirected to the CloudMonitor console. On the **Host Monitoring** page, click the **Basic Monitoring** tab to view basic monitoring information about the ECS instance, such as the CPU usage, inbound bandwidth, outbound bandwidth, average disk blocks per second \(BPS\), and average disk input/output operations per second \(IOPS\).


-   To view operating system metrics, you must first install the CloudMonitor agent. For more information, see [Overview](/intl.en-US/Host monitoring/Cloud Monitor agent/Overview.md).
-   To view process metrics, you must first install the CloudMonitor agent. For more information, see [Overview](/intl.en-US/Host monitoring/Cloud Monitor agent/Overview.md).
-   ACK clusters have added support for the monitoring of application groups. For more information, see [Monitor basic resources](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Monitoring management/Monitor basic resources.md).

