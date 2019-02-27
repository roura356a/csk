# Create a Kubernetes cluster {#CS_user_guide_kubernetes .concept}

You can create a Kubernetes cluster quickly and easily in the Container Service console.

## Context {#section_sgh_hvv_ydb .section}

During cluster creation, Container Service performs the following operations:

-   Create Elastic Compute Service \(ECS\) instances and configure to log on to other nodes from management nodes with the SSH public key. Install and configure the Kubernetes cluster by using CloudInit.
-   Create a security group. This security group allows the Virtual Private Cloud \(VPC\) inbound access of all the ICMP ports.
-   Create a new VPC and VSwitch if you do not use the existing VPC, and then create SNAT for the VSwitch.
-   Create VPC routing rules.
-   Create a NAT Gateway and a shared bandwidth package or Elastic IP \(EIP\).
-   Create a Resource Access Management \(RAM\) user and an AccessKey. The RAM user has the permissions for querying, creating, and deleting ECS instances, the permissions for adding and deleting cloud disks, and all permissions for the operations on Server Load Balancer \(SLB\), CloudMonitor, VPC, Log Service, and Network Attached Storage \(NAS\). The Kubernetes cluster dynamically creates SLB instances, cloud disks, and VPC routing rules according to your configurations.
-   Create an intranet SLB instance and expose the port 6443.
-   Create an Internet SLB instance and expose the port 6443. \(If you select to enable SSH access for Internet when creating the cluster, port 22 is exposed. Otherwise, port 22 is not exposed.\)

## Prerequisites {#section_ugh_hvv_ydb .section}

The services such as Container Service, Resource Orchestration Service \(ROS\), and RAM have been activated.

Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs), [ROS console](https://partners-intl.console.aliyun.com/#/ros), and [RAM console](https://partners-intl.console.aliyun.com/#/ram) to activate the corresponding services.

**Note:** The deployment of Container Service Kubernetes clusters depends on the application deployment capabilities of Alibaba Cloud ROS. Therefore, you need to activate ROS before creating a Kubernetes cluster.

## Limits {#section_kqj_pvv_ydb .section}

-   The SLB instances created with the cluster support only the Pay-As-You-Go billing method.
-   Kubernetes clusters support only the VPC network type.
-   By default, each account has a specified quota for the cloud resources it can create. If the number of cloud resources exceeds the quota, the account cannot create a cluster. Make sure you have enough quota before creating a cluster. To increase your quota, open a ticket.
    -   By default, each account can create up to 5 clusters in all regions and add up to 40 nodes to each cluster. To create more clusters or nodes, open a ticket.

        **Note:** In a Kubernetes cluster, the maximum number of default VPC routs is 48, that is, the Kubernetes cluster has up to 48 nodes by default when using VPC. To increase the number of nodes, first open a ticket for the target VPC so as to increase the number of VPC routes, and then open a ticket for Container Service.

    -   By default, each account can create up to 100 security groups.
    -   By default, each account can create up to 60 Pay-As-You-Go SLB instances.
    -   By default, each account can create up to 20 EIPs.
-   The limits for ECS instances are as follows:
    -   Only the CentOS operating system is supported.
    -   The Pay-As-You-Go and Subscription ECS instances can be created.

## Procedures {#section_cpd_qvv_ydb .section}

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  Under Kubernetes, click **Clusters** in the left-side navigation pane.
3.  Click **Create Kubernetes Cluster** in the upper-right corner.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15512566399035_en-US.png)

    By default, the **Create Kubernetes Cluster** page is displayed.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/155125663910858_en-US.png)

4.  Enter the cluster name.

    The cluster name can be 1–63 characters long and contain numbers, Chinese characters, English letters, and hyphens \(-\).

5.  Select the region and zone where the cluster is located.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15512566399036_en-US.png)

6.  Set the cluster network type. Kubernetes clusters support only the VPC network type.

    **VPC**: You can select **Auto Create** to create a VPC together with the Kubernetes cluster, or select**Use Existing** to use an existing VPC. If you select **Use Existing**, you can select a VPC and VSwitch from the two displayed drop-down lists.

    -   **Auto Create**: The system automatically creates a NAT Gateway for your VPC when a cluster is created.
    -   **Use Existing**: If the selected VPC has a NAT Gateway, Container Service uses the NAT Gateway. Otherwise, the system automatically creates a NAT Gateway by default. If you do not want the system to automatically create a NAT Gateway, deselect the **Configure SNAT for VPC** check box.

        **Note:** If you deselect the check box, configure the NAT Gateway on your own to implement the VPC Internet environment with secure access, or manually configure the SNAT. Otherwise, instances in the VPC cannot access the Internet normally, which leads to cluster creation failure.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15512566399037_en-US.png)

7.  Set the node type. Pay-As-You-Go and Subscription types are supported.
8.  Configure the Master nodes.

    Select the instance type for the Master nodes.

    **Note:** 

    -   Currently, only the CentOS operating system is supported.
    -   Currently, you can create only three Master nodes.
    -   System disks are attached to the Master nodes by default. Available system disks are SSD Cloud Disks and Ultra Cloud Disks.
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15512566399038_en-US.png)

9.  Configure the Worker nodes. You can create Worker nodes or add existing instances.

    **Note:** 

    -   Currently, only the CentOS operating system is supported.
    -   Each cluster can contain up to 37 Worker nodes. To create more nodes, open a ticket.
    -   System disks are attached to the Worker nodes by default. Available system disks are SSD Cloud Disks and Ultra Cloud Disks.
    -   You can also manually attach a data disk to a Worker node. The disk can be an SSD Cloud Disk, an Ultra Cloud Disk, or a Basic Disk.
    1.  To create Worker nodes, select the instance type and set the number of Worker nodes. In this example, create one Worker node.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15512566399039_en-US.png)

    2.  To add existing instances, you must create ECS instances in the current region in advance.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15512566399040_en-US.png)

10. Set the logon mode.
    -   Set the key pair.

        When creating a cluster, select the key pair logon mode and click **Create a new key pair**. In the ECS console, create a key pair. For details, see [Create an SSH key pair](../../../../../reseller.en-US/Security/Key pairs/Create an SSH key pair.md#). After the key pair is created, set the key pair as the credentials for logging on to the cluster.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15512566399041_en-US.png)

    -   Set the password.
        -   **Logon Password**: Set the node logon password.
        -   **Confirm Password**: Confirm your node logon password.
11. Set the**Pod Network CIDR** and **Service CIDR** parameters.

    **Note:** These two parameters are available only when you select to **Use Existing** VPC.

    Specify **Pod Network CIDR** and **Service CIDR**. Both of them cannot overlap with the Classless Inter-Domain Routing \(CIDR\) block used by VPC and the existing Kubernetes clusters in VPC. The values cannot be modified after the cluster is created. In addition, service CIDR cannot overlap with pod network CIDR. For more information about how to plan Kubernetes CIDR blocks, see [Plan Kubernetes CIDR blocks under VPC](reseller.en-US/Best Practices/Cluster/Plan Kubernetes CIDR blocks under VPC.md#).

12. Available Docker versions and Kubernetes versions are displayed. You can view the versions and select a version according to your needs.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/155125663912780_en-US.png)

13. Select whether to configure a SNAT Gateway for a VPC.

    **Note:** If you select **Auto Create**, you must configure a SNAT Gateway. If you select **Use Existing**, you can select whether to automatically configure a SNAT Gateway. If you select not to automatically configure a SNAT Gateway, you can configure a NAT Gateway for VPC instances to securely access the Internet, or you can configure a SNAT Gateway manually. Otherwise, instances in the VPC cannot access the Internet, which causes cluster creation failure.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15512566399042_en-US.png)

14. Select whether to enable **Use Public SLB to Expose API Server**.

    API server provides add, delete, edit, check, watch, and other HTTP Rest interfaces for a variety of resource objects \(such as pods and services\).

    1.  If you select to enable this option, the Internet SLB is created and the port 6443 of the Master nodes is exposed. The port corresponds to the API server. Then you can use kubeconfig to connect to and operate the clusters through the Internet.
    2.  If you select not to enable this option, the Internet SLB is not created. You can only use kubeconfig to connect to and operate the clusters inside the VPC.
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16644/155125663912073_en-US.png)

15. Select whether to enable SSH logon for Internet.

    **Note:** To enable SSH access for Internet, you must select **Use Public SLB to Expose API Server**.

    -   If you select to enable SSH access for Internet, you can use SSH to access a cluster.
    -   If you select not to enable SSH access for Internet, you cannot access a cluster by using SSH or connect to a cluster by using kubectl. To access a cluster instance by using SSH, manually bind an EIP to the ECS instance, configure security group rules, and open the SSH port \(22\). For details, see [Access Kubernetes clusters by using SSH](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Access Kubernetes clusters by using SSH.md#).
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15512566399043_en-US.png)

16. Select whether to install a cloud monitoring plug-in on your ECS.

    You can install a cloud monitoring plug-in on the ECS node to view the monitoring information of the created ECS instances in the CloudMonitor console.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15512566399044_en-US.png)

17. Select whether to use Log Service. You can select an existing project or create a project.

    If you select **Using SLS**, the Log Service plug-in is automatically configured in the cluster. When creating an application, you can quickly use Log Service with a simple configuration. For details, see [Use Log Service to collect Kubernetes cluster logs](reseller.en-US/User Guide/Kubernetes cluster/Log management/Use Log Service to collect Kubernetes cluster logs.md#).

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15512566409244_en-US.png)

18. Select whether to show advance config.
    1.  Select a network plug-in. Available network plug-ins are Flannel and Terway. For details, see [Do I select the Terway or Flannel plugin for my Kubernetes cluster network?](../../../../../reseller.en-US/FAQ/Do I select the Terway or Flannel plugin for my Kubernetes cluster network?.md#).
        -   Flannel: a simple and stable community Flannel CNI plug-in. It provides only a few simple features. For example, it does not support the Kubernetes Network Policy.
        -   Terway: a network plug-in developed by Alibaba Cloud Container service. It can allocate Alibaba Cloud Elastic Network Interfaces \(ENIs\) to containers. It can also define the access policies between containers according to the Kubernetes Network Policy. In addition, it supports bandwidth limiting for individual containers.
    2.  Set the number of pods for a node, that is, the maximum number of pods that can be run by a single node. We recommend that you use the default value.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15512566409045_en-US.png)

    3.  Select whether to use **Custom Cluster CA**. If this option is selected, the CA certificate can be added to the Kubernetes cluster, which enhances the security of information exchange between the server and client.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15512566409046_en-US.png)

19. Click **Create** in the upper-right corner.

**Note:** A multi-node Kubernetes cluster typically takes 10 minutes to be created.

## View cluster deployment results. {#section_xhh_hvv_ydb .section}

After the cluster is successfully created, you can view the cluster in the Cluster List of the Container Service - Kubernetes console.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15512566409047_en-US.png)

-   Click **View Logs** on the right of the cluster to view the cluster logs. To view more detailed information, click **Stack Events**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15512566409048_en-US.png)

-   You can also click **Manage** on the right of the cluster to view the basic information and connection information about this cluster.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15512566409049_en-US.png)

    **In the Cluster Info section:**

    -   **API Server Internet endpoint**: The IP address and port through which the Kubernetes API server provides services for the Internet. It enables you to manage the cluster by using kubectl or other tools on your terminal.
    -   **API Server Intranet endpoint**: The IP address and port through which the Kubernetes API server provides services inside the cluster. This IP address is the address of the SLB instance, and three Master nodes in the backend provide the services.
    -   **Master node SSH IP address**: You can directly log on to the Master nodes by using SSH to perform routine maintenance for the cluster.
    -   **Service Access Domain**: Provides the services in the cluster with access domain name for testing. The service access domain name suffix is `<cluster_id>.<region_id>.alicontainer.com`.
    For example, you can log on to the Master nodes by using SSH, and run `kubectl get node` to view the node information of the cluster.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15512566409050_en-US.png)

    As shown in the preceding figure, the cluster has four nodes, including three Master nodes and one Worker node configured when we set the parameters.


