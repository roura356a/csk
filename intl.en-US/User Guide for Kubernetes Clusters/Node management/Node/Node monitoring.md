# Node monitoring

Integrated with CloudMonitor, Container Service supports node monitoring and provides multiple monitoring metrics about your ECS nodes.

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  Select the cluster and the node in the cluster.

3.  Click **Monitor** in the Actions column to view monitoring information about the node.

    ![Node monitoring information](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8635359951/p10908.png)

4.  You are redirected to the CloudMonitor console and you can find basic monitoring information about the ECS instance, including the CPU usage, inbound bandwidth, outbound bandwidth, disk throughput, and disk IOPS.

    ![Basic monitoring information](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8635359951/p10909.png)


To view monitoring metrics about the operating system, you need to install the CloudMonitor agent. For more information, see [Overview](/intl.en-US/Host monitoring/Overview.md).

Kubernetes clusters now support resource monitoring based on application groups. For more information, see [Monitor basic resources](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Monitoring management/Monitor basic resources.md).

