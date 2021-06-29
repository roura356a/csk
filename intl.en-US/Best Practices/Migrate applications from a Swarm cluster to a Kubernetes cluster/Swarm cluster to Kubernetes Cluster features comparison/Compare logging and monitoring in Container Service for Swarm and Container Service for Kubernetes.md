# Compare logging and monitoring in Container Service for Swarm and Container Service for Kubernetes

This topic compares the logging and monitoring features of a Container Service for Swarm cluster and those of a Container Service for Kubernetes \(ACK\) cluster.

## Logging

Container Service for Swarm cluster

A Container Service for Swarm cluster implements the logging feature based on **labels**.

ACK cluster

For an ACK cluster, the logging feature is configured and implemented in the following way:

-   Create an ACK cluster:

    On the Create Kubernetes Cluster page, select **Enable Log Service**. Then, the Log Service plug-in is automatically configured for the cluster. You can use an existing project or create a project.

    ![Create an ACK cluster](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0646858951/p36258.png)

    You can also manually install the Log Service plug-in after an ACK cluster is created. For more information, see [t17400.md\#section\_shf\_y5r\_gfb](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Log management/Collect log files from containers by using Log Service.md).

-   Configure Log Service when you create an application in the cluster. For more information, see [t17400.md\#section\_g3f\_y5r\_gfb](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Log management/Collect log files from containers by using Log Service.md).
-   Use Log Service after you create an application in the cluster. For more information, see [t13074.md\#](/intl.en-US/Data Collection/Logtail collection/Container log collection/Use the console to collect Kubernetes text logs in DaemonSet mode.md) and [t13075.md\#](/intl.en-US/Data Collection/Logtail collection/Container log collection/Use the console to collect Kubernetes stdout and stderr logs in DaemonSet mode.md).

## Monitoring

To enable monitoring for a Container Service for Swarm cluster or an ACK cluster, select **Install CloudMonitor Agent on ECS Instance** on the Create Kubernetes Cluster page. Then, you can view the monitoring data of the created Elastic Compute Service \(ECS\) instances in the Cloud Monitor console.

For more information about how ACK clusters integrate the Cloud Monitor service, see [Monitor basic resources](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Monitoring management/Monitor basic resources.md).

