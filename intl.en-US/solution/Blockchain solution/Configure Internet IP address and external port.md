# Configure Internet IP address and external port {#task_ukb_fxv_vdb .task}

The following prerequisites must be met before making the blockchain network available to administration and monitoring tools and applications outside the container cluster.

-   Possess the externally accessible Internet address. You can either bind an EIP to a worker node or create a Server Load Balancer instance \(add a worker node on the backend server\) and use its Internet address. [Environment preparations](intl.en-US/solution/Blockchain solution/Environment preparations.md#) introduces how to bind an EIP to a worker node, see [Bind an EIP to a worker node](intl.en-US/solution/Blockchain solution/Environment preparations.md#section_ph2_355_vdb).
-   Allows the inbound access from the NodePort in the external port list by configuring the ECS security group rules.

1.   Log on to the [ECS console](https://ecs.console.aliyun.com/). 
2.   Click **Networks & Security ** \> **Security Groups**in the left-side navigation pane. 
3.   Select the region in which your Kubernetes cluster resides. 
4.   The security group name of the Kubernetes cluster contains k8s\_sg. Click **Configure Rules** at the right of the security group. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17795/15362009189869_en-US.png)

5.   If no security group rules under the **Inbound** tab meets the requirements, click **Add Security Group Rules**. The Add Security Group Rules dialog box appears. Complete the configurations. Complete the configurations. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17795/15362009189870_en-US.png)

    -   Select **Inbound** from the **Rule Direction** drop-down list.
    -   Enter the suitable **Port Range** according to the external port NodePort range of the blockchain network.
    -   Enter the suitable address range in the **Authorization Object** field based on the actual access requirements.
6.   Click **OK**. The security group rule is successfully added to the list and takes effect immediately. 

