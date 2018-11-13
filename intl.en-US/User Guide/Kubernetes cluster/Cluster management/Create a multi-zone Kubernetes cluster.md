# Create a multi-zone Kubernetes cluster {#task_hyb_xwf_vdb .task}

You can create a multi-zone Kubernetes cluster to guarantee high availability.

-   Activate the following services: Container Service, Resource Orchestration Service \(ROS\), and Resource Access Management \(RAM\).

    Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs), [ROS console](https://partners-intl.console.aliyun.com/#/ros), and [RAM console](https://partners-intl.console.aliyun.com/#/ram) to activate the corresponding services.

    **Note:** The deployment of Container Service Kubernetes clusters depends on the application deployment capabilities of Alibaba Cloud ROS. Therefore, activate ROS before creating a Kubernetes cluster.

-   You must create a Virtual Private Cloud \(VPC\) and create at least three VSwitches in the VPC. To achieve high availability, we recommend that you create VSwitches in different availability zones.
-   You need to manually configure SNAT for each VSwitch in the VPC. Otherwise, instances in the VPC cannot access the Internet normally.

You can create Kubernetes clusters with ECS instances in different availability zones by using the Container Service console to achieve high availability.

**Context**

During cluster creation, Container Service performs the following operations:

-   Create Elastic Compute Service \(ECS\) instances and configure to log on to other nodes from management nodes with the SSH public key. Install and configure the Kubernetes cluster by using CloudInit.
-   Create a security group. This security group allows the VPC inbound access of all the ICMP ports.
-   Create a RAM user and an AccessKey. The RAM user has the permissions for querying, creating, and deleting ECS instances, the permissions for adding and deleting cloud disks, and all permissions for the operations on Server Load Balancer \(SLB\), CloudMonitor, VPC, Log Service, and Network Attached Storage \(NAS\). The Kubernetes cluster dynamically creates SLB instances, cloud disks, and VPC routing rules according to your configurations.
-   Create an intranet SLB instance and expose the port 6443.
-   Create an Internet SLB instance and expose the port 6443. \(If you enable the SSH logon for Internet when creating the cluster, port 22 is exposed. Otherwise, port 22 is not exposed.\)

**Limits**

-   The SLB instances created with the cluster support only the Pay-As-You-Go billing method.
-   Kubernetes clusters support only the Virtual Private Cloud \(VPC\) network type.
-   By default, each account has a specified quota for the cloud resources it can create. If the number of cloud resources exceeds the quota, the account cannot create a cluster. Make sure you have enough quota before creating a cluster. To increase your quota, open a ticket.
    -   By default, each account can create up to 5 clusters in all regions and add up to 40 nodes to each cluster. To create more clusters or nodes, open a ticket.
    -   By default, each account can create up to 100 security groups.
    -   By default, each account can create up to 60 Pay-As-You-Go SLB instances.
-   The limits for ECS instances are as follows:
    -   Only the CentOS operating system is supported.
    -   The Pay-As-You-Go and Subscription ECS instances can be created.

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs). 
2.  Under Kubernetes, click **Clusters** in the left-side navigation pane. Click **Create Kubernetes Cluster** in the upper-right corner. 
3.  On the Create Kubernetes Cluster page, click **Multi-AZ Kubernetes**. 
4.  Enter the cluster name. 

    The cluster name can be 1–63 characters long and contain numbers, Chinese characters, English letters, and hyphens \(-\).

5.  Select the region where the cluster is located. 
6.  Select a VPC. 

    Select a VPC from the existing VPC drop-down list and select three VSwitches under the VPC. To achieve high availability, we recommend that you select the VSwitches located in different zones.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16644/15420938068749_en-US.png)

7.  Configure the Master nodes and Worker nodes. 

    1.  Select a node payment type from Pay-As-You-Go and Subscription. 
    2.  Select instance types of the Master nodes and Worker nodes, and set the number of Worker nodes. 

        **Note:** 

        -   Currently, only the CentOS operating system is supported.
        -   Currently, only three Master nodes can be created.
        -   Each cluster can contain up to 37 Worker nodes. To create more nodes, open a ticket.
        -   System disks are attached to Master nodes and Worker nodes by default. Available system disks include SSD Cloud Disks and Ultra Cloud Disks.
        -   You can also manually attach a data disk to the Worker node. The data disk can be an Ultra Cloud Disk or an SSD Cloud Disk.
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16644/15420938068750_en-US.png)

8.  Configure the logon mode. 
    -   Set the key pair.

        When creating a cluster, select the key pair logon mode and click **Create a new key pair**. In the ECS console, create a key pair. For details, see [Create an SSH key pair](../../../../reseller.en-US/User Guide/Key pairs/Create an SSH key pair.md#). After the key pair is created, set the key pair as the credentials for logging on to the cluster.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16644/15420938068751_en-US.png)

    -   Set the password.
        -   **Logon Password**: Set the node logon password.
        -   **Confirm Password**: Confirm your node logon password.
9.  Specify the **Pod Network CIDR** and **Service CIDR** parameters. 

    Both of them cannot overlap with the Classless Inter-Domain Routing \(CIDR\) block used by VPC and the existing Kubernetes clusters in VPC. The values cannot be modified after the cluster is created. In addition, service CIDR cannot overlap with pod network CIDR. For more information about how to plan Kubernetes CIDR blocks, see [Plan Kubernetes CIDR blocks under VPC](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Plan Kubernetes CIDR blocks under VPC.md#).

10. Select whether to enable **Use Public SLB to Expose API Server**. API server provides add, delete, edit, check, watch, and other HTTP Rest interfaces for a variety of resource objects \(such as pods and services\).

    1.  If you select to enable this option, the Internet SLB is created and the port 6443 of the Master nodes is exposed. The port corresponds to the API server. Then you can use kubeconfig to connect to and operate the clusters through the Internet.
    2.  If you select not to enable this option, the Internet SLB is not created. You can only use kubeconfig to connect to and operate the clusters inside the VPC.
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16644/154209380612073_en-US.png)

11. Select whether to enable SSH logon for Internet. 

    **Note:** To enable SSH access for Internet, you must select **Use Public SLB to Expose API Server**.

    -   If you select to enable SSH access for Internet, you can use SSH to access a cluster.
    -   If you select not to enable SSH access for Internet, you cannot access a cluster by using SSH or connect to a cluster by using kubectl. To access a cluster instance by using SSH, manually bind an EIP to the ECS instance, configure security group rules, and open the SSH port \(22\). For details, see [Access Kubernetes clusters by using SSH](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Access Kubernetes clusters by using SSH.md#).
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16644/15420938068752_en-US.png)

12. Select whether to install a cloud monitoring plug-in on your ECS. 

    You can install a cloud monitoring plug-in on the ECS node to view the monitoring information of the created ECS instances in the CloudMonitor console.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16644/15420938068753_en-US.png)

13. Select whether to use Log Service. You can select an existing project or create a project. If you select **Using SLS**, the Log Service plug-in is automatically configured in the cluster. When creating an application, you can quickly use Log Service with a simple configuration. For details, see [Use Log Service in a Kubernetes cluster](reseller.en-US/User Guide/Kubernetes cluster/Logs/Use Log Service in a Kubernetes cluster.md#).

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16644/154209380612754_en-US.png)

14. Select whether to show advance config. 
    1.  Select a network plug-in. Available network plug-ins are Flannel and Terway. For details, see [Do I select the Terway or Flannel plug-in for my Kubernetes cluster network?](../../../../reseller.en-US/problem/Do I select the Terway or Flannel plug-in for my Kubernetes cluster network?.md#).
        -   Flannel: a simple and stable community Flannel CNI plug-in. It provides only a few simple features. For example, it does not support the Kubernetes Network Policy.
        -   Terway: a network plug-in developed by Alibaba Cloud Container service. It can allocate Alibaba Cloud Elastic Network Interfaces \(ENIs\) to containers. It can also define the access policies between containers according to the Kubernetes Network Policy. In addition, it supports bandwidth limiting for individual containers.
    2.  Set the number of pods for a node, that is, the maximum number of pods that can be run by a single node.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16644/15420938068754_en-US.png)

    3.  Select whether to use **Custom Image**. The ECS instance installs the default CentOS version if no custom image is selected.

        Currently, you can only select an image based on CentOS custom version to quickly deploy the environment you need.

    4.  Sets whether to use **Custom Cluster CA**. If this option is selected, the CA certificate can be added to the Kubernetes cluster, which enhances the security of information exchange between the server and client.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16644/15420938068755_en-US.png)

15. Click **Create**, confirm the Internet access for VPC in the displayed dialog box, and click **OK** to start the deployment. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16644/15420938068759_en-US.png)

    **Note:** A multi-node Kubernetes cluster typically takes 10 minutes to be created.


View cluster deployment results.

After the cluster is successfully created, you can view the cluster in the Cluster List of the Container Service console.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16644/154209380612902_en-US.png)

-   Click **View Logs** at the right of the cluster to view the cluster logs. To view more detailed information, click **Stack Events**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16644/15420938078756_en-US.png)

-   You can also click **Manage** on the right of the cluster to view the basic information and connection information about this cluster.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16644/154209380712915_en-US.png)

    **In the Cluster Info section:**

    -   **API Server Internet endpoint:** The IP address and port through which the Kubernetes API server provides services for the Internet. It enables you to manage the cluster by using kubectl or other tools on your terminal.
    -   **API Server Intranet endpoint:** The IP address and port through which the Kubernetes API server provides services inside the cluster. This IP address is the address of the SLB instance, and three Master nodes in the backend provide the services.
    -   **Master node SSH IP address:** You can directly log on to the Master nodes by using SSH to perform routine maintenance for the cluster.
    -   **Service Access Domain:** Provides the services in the cluster with access domain name for testing. The service access domain name suffix is `<cluster_id>.<region_id>.alicontainer.com`.

