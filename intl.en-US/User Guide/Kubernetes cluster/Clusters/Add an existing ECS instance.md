# Add an existing ECS instance {#task_rtr_h3g_vdb .task}

You can add existing Elastic Compute Service \(ECS\) instances to a created Kubernetes cluster. Currently, Kubernetes clusters only support adding worker nodes.

-   If you have not created a cluster before, create a cluster first. For how to create a cluster, see [Create a cluster](intl.en-US/User Guide/Kubernetes cluster/Clusters/Create a cluster.md#).
-   Add the ECS instance to the security group of the Kubernetes cluster first.

**Instructions**

-   By default, each cluster can contain up to 40 nodes. To add more nodes, open a ticket.
-   The ECS instance to be added must be in the same Virtual Private Cloud \(VPC\) region as the cluster.
-   When adding an existing instance, make sure that your instance has an Elastic IP \(EIP\) for the VPC network type, or the corresponding VPC is already configured with the NAT gateway.  In short, make sure the corresponding node can access public network normally. Otherwise, the ECS instance fails to be added.
-   The ECS instance to be added must be under the same account as the cluster.
-   Only nodes with a CentOS operating system are supported.

1.  Log on to the [Container Service console](https://cs.console.aliyun.com). 
2.  Under Kubernetes, click **Clusters** in the left-side navigation pane. 
3.  Select the target cluster and click **More** \> **Add Existing Instance**. 

    On the Add Existing ECS Instance page and you can automatically or manually add an existing instance.

    If Automatically Add is selected, select the ECS instances to add them to the cluster automatically. If Manually Add is selected, you must obtain the command and then log on to the corresponding ECS instance to add the ECS instance to this cluster. You can only add one ECS instance at a time.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6884/15329323954319_en-US.png)

4.  Select Automatically Add to add multiple ECS instances at a time. 
    1.  In the list of existing cloud servers, select the target ECS instance, and then click **Next Step**. 

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6884/15329323954320_en-US.png)

    2.  Enter the instance information, set the logon password, and then click **Next Step**. 

        ![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/64983/cn_zh/1524193684199/Image%2010.png)

    3.  In the displayed dialog box, click **OK**, the selected ECS instance is automatically added to the cluster. 

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6884/15329323964322_en-US.png)

5.  You can also select Manually Add to manually add an existing ECS instance to the cluster. 
    1.  Select the ECS instance to be added and then click **Next Step**.  You can only add one ECS instance at a time. 

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6884/15329323964323_en-US.png)

    2.  Confirm the information and then click **Next Step**. 

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6884/15329323964324_en-US.png)

    3.  Go to the Add Existing ECS Instance page and copy the command. 

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6884/15329323964325_en-US.png)

    4.  Log on to the [ECS console](https://ecs.console.aliyun.com/). Select the region in which the cluster resides. 
    5.  Click **Connect** at the right of the ECS instance to be added.  The Enter VNC Password dialog box appears. Enter the VNC password and then click **OK**. Enter the copied command and then click **OK** to run the script. 

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6884/15329323964326_en-US.png)

    6.  After the script is successfully run, the ECS instance is added to the cluster.  You can click the cluster ID on the Cluster List page to view the node list of the cluster and  check if the ECS instance is successfully added to the cluster. 

