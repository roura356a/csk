# Node monitoring {#task_ofj_l4l_vdb .task}

Kubernetes clusters integrate with the Alibaba Cloud monitoring service seamlessly. You can view the monitoring information of Kubernetes nodes and get to know the node monitoring metrics of the Elastic Compute Service \(ECS\) instances under Kubernetes clusters.

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs). 
2.  Under Kubernetes, click **Clusters** \> **Nodes** to enter the Node List page. 
3.  Select the target cluster and node under the cluster. 
4.  Click **Monitor** at the right of the node to view the monitoring information of this node. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6892/15395833574360_en-US.png)

5.  You are redirected to the CloudMonitor console. View the basic monitoring information of the corresponding ECS instance, including the CPU usage, network inbound bandwidth, network outbound bandwidth, disk BPS, and disk IOPS.  

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6892/15395833574361_en-US.png)


To view the monitoring metrics at the operating system level, install the CloudMonitor component. For more information,  see [Introduction to Host monitoring](../../../../reseller.en-US/User Guide/Host monitoring/Introduction to Host monitoring.md#).

Kubernetes clusters can now monitor resources by using application groups. For more information, see [EN-US\_TP\_6947.md\#](reseller.en-US/User Guide/Kubernetes cluster/Monitoring/Monitor resources and send alarm notifications by using resource groups.md#).

