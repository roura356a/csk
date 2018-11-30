# Create a managed Kubernetes cluster {#task_skz_qwk_qfb .task}

You can create a managed Kubernetes cluster quickly and easily in the Container Service console.

You have activated the following services: Container Service, Resource Orchestration Service \(ROS\), Resource Access Management \(RAM\), and Auto Scaling service.

Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs), [ROS console](https://partners-intl.console.aliyun.com/#/ros), [RAM console](https://partners-intl.console.aliyun.com/#/ram), and [Auto Scaling console](https://partners-intl.console.aliyun.com/#/essnew) to activate the corresponding services.

**Note:** The deployment of Container Service managed Kubernetes clusters depends on the application deployment capabilities of Alibaba Cloud ROS. Therefore, you need to activate ROS before creating a managed Kubernetes cluster.

-   Your account must have a balance of 100 RMB and pass the real name authorization. Otherwise, you cannot create Pay-As-You-Go Elastic Compute Service \(ECS\) instances and Server Load Balancer \(SLB\) instances.
-   The SLB instances created with the cluster support only the Pay-As-You-Go billing method.
-   Kubernetes clusters support only the Virtual Private Cloud \(VPC\) network type.
-   By default, each account has a specified quota for the cloud resources it can create. If the number of cloud resources exceeds the quota, the account cannot create a cluster. Make sure you have enough quota before creating a cluster. To increase your quota, open a ticket.
    -   By default, each account can create up to 100 security groups.
    -   By default, each account can create up to 60 Pay-As-You-Go SLB instances.
    -   By default, each account can create up to 20 EIPs.

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs). 
2.  Under Kubernetes, click **Clusters** in the left-side navigation pane. The Cluster List page is displayed. Click **Create Kubernetes Cluster** in the upper-right corner. 
3.  On the Create Kubernetes Cluster page, click **Managed Kubernetes \(beta\)**. 
4.  Enter the cluster name. 

    The cluster name can be 1–63 characters long and contain numbers, Chinese characters, English letters, and hyphens \(-\).

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/154357213121257_en-US.png)

5.  Select the region and zone where the cluster is located. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/154357213221258_en-US.png)

6.  Set the cluster network type. 

    **Note:** Kubernetes clusters support only the VPC network type.

    **VPC**: You can select **Auto Create** to create a VPC together with the Kubernetes cluster, or select**Use Existing** to use an existing VPC. If you select **Use Existing**, you can select a VPC and VSwitch from the two displayed drop-down lists.

    -   **Auto Create**: The system automatically creates a NAT Gateway for your VPC when a cluster is created.
    -   **Use Existing**: If the selected VPC has a NAT Gateway, Container Service uses the NAT Gateway. Otherwise, the system automatically creates a NAT Gateway by default. If you do not want the system to automatically create a NAT Gateway, deselect the **Configure SNAT for VPC** check box.

        **Note:** If you deselect the check box, configure the NAT Gateway on your own to implement the VPC Internet environment with secure access, or manually configure the SNAT. Otherwise, instances in the VPC cannot access the Internet normally, which leads to cluster creation failure.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/154357213221259_en-US.png)

7.  Set the node type. 

    **Note:** **Pay-As-You-Go** and **Subscription** types are supported.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/154357213221260_en-US.png)

8.  Configure the instance. 

    **Note:** 

    -   Currently, only the CentOS operating system is supported.
    -   Each cluster contains at least two nodes.
    -   Each cluster contains up to 48 nodes. To create more nodes, open a ticket.
    -   System disks are attached to the instances by default. Available system disks are Ultra Disks and SSD Disks.
    -   You can attach a data disk to the instances. The data disk can be an Ultra Disk or an SSD Disk.
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/154357213221261_en-US.png)

9.  Set the logon mode. 

    -   Set the key pair.

        When creating a cluster, select the key pair logon mode and click **Create a new key pair**. In the ECS console, create a key pair. For details, see [Create an SSH key pair](../../../../reseller.en-US/User Guide/Key pairs/Create an SSH key pair.md#). After the key pair is created, set the key pair as the credentials for logging on to the cluster.

    -   Set the password.
        -   **Logon Password**: Set the node logon password.
        -   **Confirm Password**: Confirm your node logon password.
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/154357213221262_en-US.png)

10. Set the **Pod Network CIDR** and **Service CIDR** parameters. 

    **Note:** 

    -   These two parameters are available only when you select to **Use Existing** VPC.
    -   Both **Pod Network CIDR** and **Service CIDR** cannot overlap with the Classless Inter-Domain Routing \(CIDR\) block used by the VPC and the existing Kubernetes clusters in the VPC. The values cannot be modified after the cluster is created. In addition, service CIDR cannot overlap with pod network CIDR. For more information about how to plan Kubernetes CIDR blocks, see [Plan Kubernetes CIDR blocks under VPC](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Plan Kubernetes CIDR blocks under VPC.md#).
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/154357213221265_en-US.png)

11. Select whether to configure a SNAT Gateway for the VPC. 

    **Note:** 

    -   If you select **Auto Create**, you must configure a SNAT Gateway.
    -   If you select **Use Existing**, you can select whether to automatically configure a SNAT Gateway. If you select not to automatically configure a SNAT Gateway, you can configure a NAT Gateway for VPC instances to securely access the Internet, or you can configure a SNAT Gateway manually. Otherwise, the instances in the VPC cannot access the Internet, and the cluster fails to be created.
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/154357213221267_en-US.png)

12. Select whether to install a cloud monitoring plug-in on your ECS. You can install a cloud monitoring plug-in on the ECS node to view the monitoring information of the created ECS instances in the CloudMonitor console.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/154357213221268_en-US.png)

13. Select a network plug-in. Available network plug-ins are Flannel and Terway. For details, see [Do I select the Terway or Flannel plug-in for my Kubernetes cluster network?](../../../../reseller.en-US/FAQ/Do I select the Terway or Flannel plug-in for my Kubernetes cluster network?.md#). 

    -   Flannel: a simple and stable community Flannel plug-in. It provides only a few simple features. For example, it does not support the Kubernetes Network Policy.
    -   Terway: a network plug-in developed by Alibaba Cloud Container service. It can allocate Alibaba Cloud Elastic Network Interfaces \(ENIs\) to containers. It can also define the access policies between containers according to the Kubernetes Network Policy. In addition, it supports bandwidth limiting for individual containers.
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/154357213221269_en-US.png)

14. Set the RDS whitelist. 

    Add the IP addresses of the ECS instances to the RDS instance whitelist.

    **Note:** This option is available only when you select to **Use Existing** VPC.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/154357213221270_en-US.png)

15. Click **Create** in the upper-right corner. 

    **Note:** Normally, it takes five minutes to create a multi-node Kubernetes cluster.


After the cluster is successfully created, you can view the cluster on the Cluster List page of the Container Service console.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/154357213221425_en-US.png)

Click **View Logs** on the right of the cluster to view the cluster logs on the Cluster Logs page. To view more information, click **Stack Events**.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/154357213221426_en-US.png)

On the Cluster List page, find the created cluster and click **Manage** to view the basic information and connection information about this cluster.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/154357213221427_en-US.png)

**In the Cluster Info section:**

-   **API Server Internet endpoint**: The IP address and port through which the Kubernetes API server provides services for the Internet. With the API Server Internet endpoint, you can manage the cluster by using kubectl or other tools on your terminal.
-   **Service Access Domain**: Provides the services in the cluster with access domain name for testing. The service access domain name suffix is `<cluster_id>.<region_id>.alicontainer.com`.

You can see [Connect to a Kubernetes cluster by using kubectl](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Connect to a Kubernetes cluster by using kubectl.md#) and run `kubectl get node` to view the node information of the cluster.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/154357213221438_en-US.png)

