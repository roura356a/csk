# Create a Kubernetes cluster {#CS_user_guide_kubernetes .concept}

You can create a Kubernetes cluster quickly and easily in the Container Service console. 

## Instructions {#section_sgh_hvv_ydb .section}

During cluster creation, Container Service performs the following operations:

-   Create Elastic Compute Service \(ECS\) instances and configure to log on to other nodes from management nodes with the SSH public key. Install and configure the Kubernetes cluster by using CloudInit.
-   Create a security group. This security group allows the Virtual Private Cloud \(VPC\) inbound access of all the ICMP ports.
-   Create a new VPC and VSwitch if you do not use the existing VPC, and then create SNAT for the VSwitch.
-   Create VPC routing rules.
-   Create a NAT Gateway and a shared bandwidth package or Elastic IP \(EIP \).
-   Create a Resource Access Management \(RAM\) user and the AccessKey. This RAM user has the permissions of querying, creating, and deleting ECS instances, the permissions of adding and deleting cloud disks, and all permissions for Server Load Balancer \(SLB\), CloudMonitor, VPC, Log Service, and Network Attached Storage \(NAS\). The Kubernetes cluster dynamically creates SLB instances, cloud disks, and VPC routing rules according to your configurations.
-   Create an intranet SLB instance and expose the port 6443.
-   Create an Internet SLB instance and expose the port 6443. \(If you select to enable the SSH logon for Internet when creating the cluster, port 22 is exposed. Otherwise, port 22 is not exposed.\)

## Prerequisites {#section_ugh_hvv_ydb .section}

Activate the following services: Container Service, Resource Orchestration Service \(ROS\), and Resource Access Management \(RAM\).

Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs), [ROS console](https://partners-intl.console.aliyun.com/#/ros), and [RAM console](https://partners-intl.console.aliyun.com/#/ram) to activate the corresponding services.

**Note:** The deployment of Container Service Kubernetes clusters depends on the application deployment capabilities of Alibaba Cloud ROS. Therefore, activate ROS before creating a Kubernetes cluster.

## Limits {#section_kqj_pvv_ydb .section}

-   The SLB instance created with the cluster supports only the Pay-As-You-Go billing method.
-   Kubernetes clusters support only the VPC network type.
-   By default, each account has a certain quota for the cloud resources it can create. The cluster fails to be created if the quota is exceeded. Make sure you have enough quota before creating the cluster. To increase your quota, open a ticket.
    -   By default, each account can create at most five clusters in all regions and add up to 40 worker nodes to each cluster. To create more clusters or nodes, open a ticket.

        **Note:** In a Kubernetes cluster, the maximum number of default VPC routs is 48, that is, the Kubernetes cluster has up to 48 nodes by default when using VPC. To increase the number of nodes, first open a ticket for the target VPC so as to increase the number of VPC routes, and then open a ticket for Container Service.

    -   By default, each account can create at most 100 security groups.
    -   By default, each account can create at most 60 Pay-As-You-Go SLB instances.
    -   By default, each account can create at most 20 EIPs.
-   Limits for ECS instances are as follows:
    -   Only the Centos operating system is supported.
    -   Creating Pay-As-You-Go and Subscription ECS instances is supported.

## Procedure {#section_cpd_qvv_ydb .section}

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  Under Kubernetes, click **Clusters** in the left-side navigation pane.
3.  Click **Create Kubernetes Cluster** in the upper-right corner.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15397777669035_en-US.png)

    By default, the **Create Kubernetes Cluster** page is displayed.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/153977776610858_en-US.png)

4.  Enter the cluster name.

    The cluster name can be 1–63 characters long and contain numbers, Chinese characters, English letters, and hyphens \(-\).

5.  Select the region and zone where the cluster resides.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15397777669036_en-US.png)

6.  Set the cluster network type. Kubernetes clusters support only the VPC network type.

    **VPC**: You can select **Auto Create** to create a VPC together with the Kubernetes cluster, or select**Use Existing** to use an existing VPC. By selecting **Use Existing**, you can select a VPC and VSwitch from the two displayed drop-down lists.

    -   **Auto Create**: The system automatically creates a NAT gateway for your VPC when the cluster is created.
    -   **Use Existing**: If the selected VPC has a NAT gateway, Container Service uses the NAT gateway. Otherwise, the system automatically creates a NAT gateway by default. If you do not want the system to automatically create a NAT gateway, clear the **Configure SNAT for VPC** check box.

        **Note:** If you select to not automatically create a NAT gateway, configure the NAT gateway on your own to implement the VPC public network environment with secure access, or manually configure the SNAT. Otherwise, instances in the VPC cannot access public network normally, which leads to cluster creation failure.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15397777669037_en-US.png)

7.  Configure the node type, Pay-As-You-Go and Subscription types are supported.
8.  Configure the master nodes.

    Select the instance type for the master nodes.

    **Note:** 

    -   Currently, master nodes support only the CentOS operating system.
    -   Currently, you can create only three master nodes.
    -   System disks are attached to the master node by default. Available system disks are SSD Cloud Disks and Ultra Cloud Disks.
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15397777669038_en-US.png)

9.  Configure the worker nodes. Select to create a worker node or add existing ECS instances.

    **Note:** 

    -   Currently, worker nodes support only the CentOS operating system.
    -   Each cluster can contain up to 37 worker nodes. To create more nodes, open a ticket.
    -   System disks are attached to the worker node by default. Available system disks are SSD Cloud Disks and Ultra Cloud Disks.
    -   You can also choose to manually attach a disk to the worker node, which can be an SSD Cloud Disk, Ultra Cloud Disk, or Basic Disk.
    1.  To create worker nodes, select the instance type and set the number worker nodes. In this example, create one worker node.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15397777669039_en-US.png)

    2.  To add existing ECS instances, you must create ECS instances in the current region in advance.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15397777669040_en-US.png)

10. Configure the logon mode.
    -   Configure the key pair.

        Select the key pair logon mode when creating the cluster and click **Create a new key pair**. In the ECS console, create a key pair, see [Create an SSH key pair](../../../../reseller.en-US/User Guide/Key pairs/Create an SSH key pair.md#). After the key pair is created, set the key pair as the credentials for logging on to the cluster.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15397777669041_en-US.png)

    -   Configure the password.
        -   **Logon Password**: Configure the node logon password.
        -   **Confirm Password**: Confirm your node logon password.
11. Configure the **Pod Network CIDR** and **Service CIDR**.

    **Note:** These two parameters are available only when you select to **Use Existing** VPC.

    Specify **Pod Network CIDR** and **Service CIDR**. Both of them cannot overlap with the Classless Inter-Domain Routing \(CIDR\) block used by VPC and the existing Kubernetes clusters in VPC. The values cannot be modified after the cluster is created. Besides, Service CIDR cannot overlap with Pod Network CIDR. For more information about how to plan Kubernetes CIDR blocks, see [Plan Kubernetes CIDR blocks under VPC](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Plan Kubernetes CIDR blocks under VPC.md#).

12. Available Docker versions and Kubernetes versions are displayed. You can view the versions and select a version according to your needs.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/153977776712780_en-US.png)

13. Set whether to configure a SNAT gateway for VPC.

    **Note:** SNAT must be configured if you select **Auto Create** VPC. If you select to **Use Existing** VPC, you can select whether to automatically configure SNAT gateway. If you choose not to automatically configure SNAT, you can configure the NAT gateway to implement VPC security access to the public network or you can configure SNAT manually. Otherwise, instances in the VPC cannot access the public network, which causes cluster creation failure.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15397777679042_en-US.png)

14. Set whether to enable **Expose API SERVER with public SLB**.

    API server provides add, delete, edit, check, watch, and other HTTP Rest interfaces for a variety of resource objects \(such as pods and services\).

    1.  If you choose to enable this option, the Internet SLB is created and the port 6443 of master nodes is exposed. The port corresponds to API Server. Then you can use kubeconfig to connect to and operate the cluster in the network outside the VPC.
    2.  If you choose not to enable the option, the Internet SLB is not created. You use kubeconfig to connect to and operate the cluster inside the VPC.
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16644/153977776712073_en-US.png)

15. Select whether to enable SSH access for Internet.

    **Note:** To enable SSH access for Internet, you must enable **Expose API SERVER with public SLB**.

    -   With this check box selected, you can access the cluster by using SSH.
    -   If this check box is not selected, you cannot access the cluster by using SSH or connect to the cluster by using kubectl. To access a cluster instance by using SSH, manually bind EIP to the ECS instance, configure security group rules, and open the SSH port \(22\). For more information, see [Access Kubernetes clusters by using SSH](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/SSH访问Kubernetes集群.md#).
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/153977776712075_en-US.png)

16. Select whether to install the cloud monitoring plug-in.

    Installing a cloud monitoring plug-in on the node allows you to view the monitoring information of the created ECS instance in the CloudMonitor console

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15397777679044_en-US.png)

17. Sets whether to enable Log Service. You can use an existing project or create a new project.

    If you select the **Using SLS** check box, the Log Service plug-in is automatically configured in the cluster. When creating an application later, you can use Log Service quickly with simple configuration. For more information, see [Use Log Service in a Kubernetes cluster](reseller.en-US/User Guide/Kubernetes cluster/Logs/Use Log Service in a Kubernetes cluster.md#).

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15397777679244_en-US.png)

18. Select whether to enable advanced configurations.
    1.  Select a network plug-in. Available network plug-ins are Flannel and Terway.
        -   Flannel: Simple and stable community Flannel cni plug-in.
        -   Terway: Alibaba Cloud Container Service self-developed network plug-in, which supports Alibaba Cloud flexible network card to be distributed to the container, and supports Kubernetes NetworkPolicy to define the inter-container access policy. Supports bandwidth limiting for the separate containers. Currently it is in the public beta.
    2.  Set the number of pods for one node, that is, the maximum number of pods that can be run by a single node. We recommend that you maintain the default value.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15397777679045_en-US.png)

    3.  Sets whether to use **Custom Cluster CA**. With this check box selected, the CA certificate can be added to the Kubernetes cluster, which enhances the security of information exchange between the server and client.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15397777679046_en-US.png)

19. Click **Create** in the upper-right corner.

**Note:** Creating a Kubernetes cluster with multiple nodes lasts more than 10 minutes.

## View cluster deployment results {#section_xhh_hvv_ydb .section}

After the cluster is successfully created, you can view the cluster in the Kubernetes cluster list of the Container Service console.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15397777679047_en-US.png)

-   Click **View Logs** at the right of the cluster to view the cluster logs. To view more detailed information, click **Stack Events**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15397777679048_en-US.png)

-   You can also click **Manage** at the right of the cluster to view the basic information and connection information of this cluster.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15397777679049_en-US.png)

    **In the Connection Information section:**

    -   **API Server Internet endpoint**: The address and port through which the Kubernetes API server provides services for the Internet. It enables you to manage the cluster by using kubectl or other tools on your terminal.
    -   **API Server Intranet endpoint**: The address and port through which the Kubernetes API server provides services inside the cluster. This IP address is the address of the Server Load Balancer instance, and three master nodes in the backend provide the service.
    -   **Master node SSH IP address**: You can directly log on to the master nodes by using SSH to perform routine maintenance for the cluster.
    -   **Service Access Domain**: Provides the service in the cluster with access domain name for testing. The service access domain name suffix is `<cluster_id>.<region_id>.alicontainer.com`.
    For example, you can log on to the master nodes by using SSH, and run the `kubectl get node` to view the node information of the cluster.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15397777689050_en-US.png)

    As shown in the preceding figure, the cluster has four nodes, including three master nodes and one worker node configured when creating the cluster.


