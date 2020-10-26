# Add existing nodes

You can add existing ECS instances to clusters. Currently, you can only add worker nodes to clusters.

-   If you have not created a cluster before, you need to [Create a cluster of ACK Proprietary Edition](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a cluster of ACK Proprietary Edition.md).
-   Add the ECS instance to the security group of the Kubernetes cluster.

-   By default, a cluster can contain up to 40 nodes. To add more nodes, submit a ticket.
-   The ECS instance must be in the same region and VPC network as the cluster.
-   If the ECS instance is deployed in a VPC network, make sure that the instance has an Elastic IP address, or a NAT gateway is configured for the VPC network. You need to make sure that the ECS instance has Internet access. Otherwise, you cannot add it to a cluster.
-   The ECS instance must belong to the same account as the cluster.
-   The ECS instance must be running the CentOS operating system.

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, choose **Clusters** \> **Clusters** to go to the Clusters page.

3.  Select the target cluster and click **More** \> **Add Existing Instance** in the Actions column.

    ![Add existing nodes](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/6635359951/p10825.png)

    On the Add Existing Node page, you can choose to add existing ECS instances to the cluster either automatically or manually.

    When Auto is selected, all available ECS instances are listed on the current page. Select the instances and they will be automatically added to the cluster. To manually add an ECS instance, you need to obtain the installation command and log on to the ECS instance to run the command. You can only add one ECS instance at a time.

4.  Select Auto to add multiple ECS instances to the cluster.

    1.  Select one or multiple ECS instances, and click **Next Step**.

        ![Add ECS instances](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/6635359951/p10826.png)

    2.  Enter instance information, set the CPU Policy, logon password, and labels, and then click **Next Step**.

        ![Enter instance information](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/6635359951/p10827.png)

    3.  In the dialog box that appears, click **OK**. The selected ECS instances are then automatically added to the cluster.

        ![Add ECS instances to the cluster](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/6635359951/p10828.png)

5.  You can also manually add an ECS instance to the cluster.

    1.  Select the ECS instance that you want to add and click **Next Step**. You can add only one ECS instance at a time.

        ![Manually add ECS instance](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/6635359951/p10828.png)

    2.  Confirm the instance information and click **Next Step**.

        ![Instance configuration](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/6635359951/p10830.png)

    3.  Copy the command.

        ![Add Node](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/6635359951/p10831.png)

    4.  Click **Complete**.

    5.  Log on to the [ECS console](https://ecs.console.aliyun.com/) and click **Instances** in the left-side navigation pane. Select the region where the cluster is located and find the ECS instance that you want to add.

    6.  Click **Connect** in the Actions column. The Enter VNC Password dialog box appears. Enter the VNC password and then click **OK**. Enter the copied command and click **OK** to run the script.

        ![VNC](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/6635359951/p10832.png)

    7.  After the script is successfully executed, the ECS instance is added to the cluster. You can click the cluster ID on the Clusters page to view the nodes in the cluster and check whether the ECS instance has been added to the cluster.


