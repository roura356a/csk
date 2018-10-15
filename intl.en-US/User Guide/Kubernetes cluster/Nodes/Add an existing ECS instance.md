# Add an existing ECS instance {#task_rtr_h3g_vdb .task}

You can add existing Elastic Compute Service \(ECS\) instances to a created Kubernetes cluster. Currently, Kubernetes clusters only support adding worker nodes.

-   If you have not created a cluster before, create a cluster first. For how to create a cluster, see [Create a Kubernetes cluster](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#).
-   Add the ECS instance to the security group of the Kubernetes cluster first.

-   By default, each cluster can contain up to 40 nodes. To add more nodes, open a ticket.
-   The ECS instance to be added must be in the same Virtual Private Cloud \(VPC\) region as the cluster.
-   When adding an existing instance, make sure that your instance has an Elastic IP \(EIP\) for the VPC network type, or the corresponding VPC is already configured with the NAT gateway.  In short, make sure the corresponding node can access public network normally. Otherwise, the ECS instance fails to be added.
-   The ECS instance to be added must be under the same account as the cluster.
-   Only the ECS instance whose operating system is CentOS can be added.

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs). 
2.  Under Kubernetes, click **Clusters** in the left-side navigation pane. 
3.  Select the target cluster and click **More** \> **Add Existing Instance**. 

    The Add Existing ECS Instance page appears. All the available ECS instances under the current account are displayed on this page. Select to add existing ECS instances automatically or manually.

    If Automatically Add is selected, select the ECS instances to add them to the cluster automatically. If Manually Add is selected, you must obtain the command and then log on to the corresponding ECS instance to add the ECS instance to this cluster. You can only add one ECS instance at a time.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16929/153958329410825_en-US.png)

4.  Select Automatically Add to add multiple ECS instances at a time. 
    1.  In the list of existing cloud servers, select the target ECS instance, and then click **Next Step**. 

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16929/153958329510826_en-US.png)

    2.  Enter the instance information, set the logon password, and then click **Next Step**. 

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16929/153958329510827_en-US.png)

    3.  Click **Confirm** in the displayed dialog box. The selected ECS instances are automatically added to this cluster. 

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16929/153958329510827_en-US.png)

5.  You can also select Manually Add to manually add an existing ECS instance to the cluster. 
    1.  Select the ECS instance to be added and then click **Next Step**.  You can add only one ECS instance at a time. 

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16929/153958329510828_en-US.png)

    2.  Confirm the information and then click **Next Step**. 

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16929/153958329510830_en-US.png)

    3.  Copy the command. 

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16929/153958329510831_en-US.png)

    4.  Click **Done**. 
    5.  Log on to the [ECS console](https://partners-intl.console.aliyun.com/#/ecs) and click **Instances** in the left-side navigation pane. Select the region in which the cluster resides and the ECS instance to be added. 
    6.  Click **Connect** at the right of the ECS instance to be added. The Enter VNC Password dialog box appears. Enter the VNC password and then click **OK**. Enter the copied command and then click **OK** to run the script. 

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16929/153958329510832_en-US.png)

    7.  After the script is successfully run, the ECS instance is added to the cluster. You can click the cluster ID on the Cluster List page to view the node list of the cluster and check if the ECS instance is successfully added to the cluster. 

