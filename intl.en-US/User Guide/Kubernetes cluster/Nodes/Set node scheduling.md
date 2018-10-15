# Set node scheduling {#task_mpc_1mb_r2b .task}

You can set node scheduling through the web interface so that you can allocate loads to each node properly.

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs). 
2.  Under Kubernetes, click **Clusters** \> **Nodes** to enter the Node List page. 
3.  Select a cluster, select a node under the cluster, and click **Schedule Settings** on the right. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17041/15395833998681_en-US.png)

4.  Set node scheduling in the displayed dialog box. In this example, click **Change to Unschedulable** to set the node to unschedulable. 

    **Note:** The scheduling status of the current node is displayed in the Scheduling Settings dialog box, which is schedulable by default. You can change the status.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17041/15395833998683_en-US.png)

    After the status is set, the scheduling status of the node changes in the dialog box.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17041/15395833998685_en-US.png)


When you deploy your application later, you can find that pods are not scheduled to the node.

