# Create a multi-zone Kubernetes cluster {#task_hyb_xwf_vdb .task}

You can create a multi-zone Kubernetes cluster to guarantee high availability of the cluster.

-   Activate the following services: Container Service, Resource Orchestration Service \(ROS\), and Resource Access Management \(RAM\).

    Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs), [ROS console](https://partners-intl.console.aliyun.com/#/ros), and [RAM console](https://partners-intl.console.aliyun.com/#/ram) to activate the corresponding services.

    **Note:** The deployment of Container Service Kubernetes clusters depends on the application deployment capabilities of Alibaba Cloud ROS. Therefore, activate ROS before creating a Kubernetes cluster.

-   You must create a Virtual Private Cloud \(VPC\) and create at least three VSwitches in the VPC. To achieve high availability, we recommend that you create VSwitches in different availability zones.
-   You need to manually configure SNAT for each VSwitch in the VPC. Otherwise, instances in the VPC cannot access the Internet normally.

You can create Kubernetes clusters with ECS instances in different availability zones by using the Container Service console to achieve high availability.

**Instructions**

During cluster creation, the Container Service performs the following operations:

-   Create Elastic Compute Service \(ECS\) instances and configure to log on to other nodes from management nodes with the SSH public key. Install and configure the Kubernetes cluster by using CloudInit.
-   Create a security group. This security group allows the VPC inbound access of all the ICMP ports.
-   Create a RAM user and the AccessKey. This RAM user has the permissions of querying, creating, and deleting ECS instances, the permissions of adding and deleting cloud disks, and all permissions for Server Load Balancer \(SLB\), CloudMonitor, VPC, Log Service, and Network Attached Storage \(NAS\). The Kubernetes cluster dynamically creates SLB instances, cloud disks, and VPC routing rules according to your configurations.
-   Create an intranet Server Load Balancer instance and expose the port 6443.
-   Create an Internet SLB instance and expose the port 6443. \(If you select to enable the SSH logon for Internet when creating the cluster, port 22 is exposed. Otherwise, port 22 is not exposed.\)

**Limits**

-   The Server Load Balancer instance created with the cluster supports only the Pay-As-You-Go billing method.
-   Kubernetes clusters support only the Virtual Private Cloud \(VPC\) network type.
-   By default, each account has a certain quota for the cloud resources it can create. The cluster fails to be created if the quota is exceeded. Make sure you have enough quota before creating the cluster. To increase your quota, open a ticket.
    -   By default, each account can create at most five clusters in all regions and add up to 40 worker nodes to each cluster. To create more clusters or nodes, open a ticket.
    -   By default, each account can create at most 100 security groups.
    -   By default, each account can create at most 60 Pay-As-You-Go Server Load Balancer instances.
-   Limits for ECS instances are as follows:
    -   Only the CentOS operating system is supported.
    -   Creating Pay-As-You-Go and Subscription ECS instances is supported.

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs). 
2.  Under Kubernetes, click **Clusters** in the left-side navigation pane. Click **Create Kubernetes Cluster** in the upper-right corner. 
3.  On the Create Kubernetes Cluster page, select **Multi-AZ Kubernetes**. 
4.  Enter the cluster name. 

    The cluster name can be 1–63 characters long and contain numbers, Chinese characters, English letters, and hyphens \(-\).

5.  Select the region where the cluster resides. 
6.  Select a VPC. 

    Select a VPC from the existing VPC drop-down list and select three VSwitches under the VPC. To achieve high availability, we recommend that you select VSwitches located in different zones.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16644/15395795578749_en-US.png)

7.  Configure the master nodes and worker nodes. 

    1.  Select a node payment type from Pay-As-You-Go and Subscription. 
    2.  Select instance types of the master nodes and worker nodes, and set the number of worker nodes. 

        **Note:** 

        -   Currently only the CentOS operating system is supported.
        -   Currently, only three master nodes can be created.
        -   Each cluster can contain at most 37 worker nodes. To create more nodes, open a ticket.
        -   System disks are attached to master nodes and worker nodes by default. Available system disks include SSD Cloud Disks and Ultra Cloud Disks.
        -   You can also choose to manually attach a disk to the worker node, which can be an SSD Cloud Disk or Ultra Cloud Disk.
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16644/15395795578750_en-US.png)

8.  Configure the logon mode. 
    -   Configure the key pair.

        Select the key pair logon mode when creating the cluster and click **Create a new key pair**. In the ECS console, create a key pair, see [Create an SSH key pair](../../../../reseller.en-US/User Guide/Key pairs/Create an SSH key pair.md#). After the key pair is created, set the key pair as the credentials for logging on to the cluster.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16644/15395795578751_en-US.png)

    -   Configure the password.
        -   **Logon Password**: Configure the node logon password.
        -   **Confirm Password**: Confirm your node logon password.
9.  Specify the **Pod Network CIDR** and **Service CIDR**. 

    Both of them cannot overlap with the Classless Inter-Domain Routing \(CIDR\) block used by VPC and the existing Kubernetes clusters in VPC. The values cannot be modified after the cluster is created. Besides, Service CIDR cannot overlap with Pod Network CIDR. For more information about how to plan Kubernetes CIDR blocks, see [Plan Kubernetes CIDR blocks under VPC](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Plan Kubernetes CIDR blocks under VPC.md#).

10. Set whether to enable **Expose API SERVER with public SLB**. API server provides add, delete, edit, check, watch, and other HTTP Rest interfaces for a variety of resource objects \(such as pods and services\).

    1.  If you choose to enable this option, the Internet SLB is created and the port 6443 of master nodes is exposed. The port corresponds to API Server. Then you can use kubeconfig to connect to and operate the cluster in the network outside the VPC.
    2.  If you choose not to enable the option, the Internet SLB is not created. You use kubeconfig to connect to and operate the cluster inside the VPC.
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16644/153957955712073_en-US.png)

11. Select whether to enable SSH logon for Internet. 

    **Note:** To enable SSH access for Internet, you must enable **Expose API SERVER with public SLB**.

    -   With this check box selected, you can access the cluster by using SSH.
    -   If this check box is not selected, you cannot access the cluster by using SSH or connect to the cluster by using kubectl. To access a cluster instance by using SSH, manually bind EIP to the ECS instance, configure security group rules, and open the SSH port \(22\). For more information, see [EN-US\_TP\_16642.md\#](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/SSH访问Kubernetes集群.md#).
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16644/15395795588752_en-US.png)

12. Select whether to install the cloud monitoring plug-in. 

    Installing a cloud monitoring plug-in on the node allows you to view the monitoring information of the created ECS instance in the CloudMonitor console

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16644/15395795588753_en-US.png)

13. Sets whether to enable Log Service. You can use an existing project or create a new project. If you select the **Using SLS** check box, the Log Service plug-in is automatically configured in the cluster. When creating an application later, you can use Log Service quickly with simple configuration. For more information, see [Use Log Service in a Kubernetes cluster](reseller.en-US/User Guide/Kubernetes cluster/Logs/Use Log Service in a Kubernetes cluster.md#).

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16644/153957955812754_en-US.png)

14. Select whether to enable advanced configurations. 
    1.  Select a network plug-in. Available network plug-ins are Flannel and Terway.
        -   Flannel: Simple and stable community Flannel cni plug-in.
        -   Terway: Alibaba Cloud Container Service self-developed network plug-in, which supports Alibaba Cloud flexible network card to be distributed to the container, and supports Kubernetes NetworkPolicy to define the inter-container access policy. Supports bandwidth limiting for the separate containers. Currently it is in the public beta.
    2.  Set the number of pods for one node, that is, the maximum number of pods that can be run by a single node.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16644/15395795588754_en-US.png)

    3.  Select whether to use **Custom Image**. The ECS instance installs the default CentOS version if no custom image is selected.

        Currently, you can only select an image based on CentOS to quickly deploy the environment you need.

    4.  Sets whether to use **Custom Cluster CA**. With this check box selected, the CA certificate can be added to the Kubernetes cluster, which enhances the security of information exchange between the server and client.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16644/15395795588755_en-US.png)

15. Click **Create**, confirm Internet access for VPC in the displayed dialog box, and click **OK** to start the deployment. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16644/15395795588759_en-US.png)

    **Note:** Creating a Kubernetes cluster with multiple nodes lasts more than 10 minutes.


View cluster deployment results

After the cluster is successfully created, you can view the cluster in the Kubernetes cluster list of the Container Service console.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16644/153957955812902_en-US.png)

-   Click **View Logs** at the right of the cluster to view the cluster logs. To view more detailed information, click **Stack Events**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16644/15395795588756_en-US.png)

-   You can also click **Manage** at the right of the cluster to view the basic information and connection information of this cluster.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16644/153957955812915_en-US.png)

    **In the Connection Information section:**

    -   **API Server Internet endpoint**: The address and port through which the Kubernetes API server provides services for the Internet. It enables you to manage the cluster by using kubectl or other tools on your terminal.
    -   **API Server Intranet endpoint**: The address and port through which the Kubernetes API server provides services inside the cluster. This IP address is the address of the Server Load Balancer instance, and three master nodes in the backend provide the service.
    -   **Master node SSH IP address**: You can directly log on to the master nodes by using SSH to perform routine maintenance for the cluster.
    -   **Service Access Domain**: Provides the service in the cluster with access domain name for testing. The suffix of the service access domain name is `<cluster_id>.<region_id>.alicontainer.com`.

