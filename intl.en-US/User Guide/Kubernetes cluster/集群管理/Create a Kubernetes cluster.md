# Create a Kubernetes cluster {#CS_user_guide_kubernetes .concept}

You can create a Kubernetes cluster quickly and easily in the Container Service console. 

## Instructions {#section_sgh_hvv_ydb .section}

During cluster creation, the Container Service performs the following operations:

-   Create Elastic Compute Service \(ECS\) instances and configure to log on to other nodes from management nodes with the SSH public key. Install and configure the Kubernetes cluster by using CloudInit.
-   Create a security group that allows the VPC Access to all ICMP ports.
-   Create a new VPC and VSwitch if you do not use the existing VPC, and then create SNAT for the VSwitch.
-   Create VPC routing rules.
-   Create NAT gateway and Elastic IP \(EIP\).
-   Create a Resource Access Management \(RAM\) user and the AccessKey. This RAM user has the permissions of querying, creating, and deleting ECS instances, adding and deleting cloud disks, all permissions for VPC, all permissions for the log service, all permissions for NAS. The kubernetes cluster dynamically creates slbs, clouds, and VPC routing rules according to the configuration of the user deployment.
-   Create the internal network SLB and expose port 6443.
-   Create an Internet Server Load Balancer instance and expose the ports 6443, 8443, and 22. If you select to enable the SSH logon for Internet when creating the cluster, port 22 is exposed.  Otherwise, port 22 is not exposed.

## Prerequisites {#section_ugh_hvv_ydb .section}

Activate the following services: Container Service, Resource Orchestration Service \(ROS\), and RAM.

Log on to the [Container Service console](https://cs.console.aliyun.com/), [ROS console](https://ros.console.aliyun.com/), and [RAM console](https://ram.console.aliyun.com/) to activate the corresponding services.

**Note:** The deployment of Container Service Kubernetes clusters depends on the application deployment capabilities of Alibaba Cloud ROS. Therefore, activate ROS before creating a Kubernetes cluster.

## Limits {#section_kqj_pvv_ydb .section}

-   The Server Load Balancer instance created with the cluster only supports the Pay-As-You-Go billing method.
-   Kubernetes clusters only support the network type VPC.
-   By default, each account has a certain quota for the cloud resources they can create. The cluster fails to be created if the quota is exceeded. Make sure you have enough quota before creating the cluster.  To increase your quota, open a ticket.
    -   By default, each account can create up to five clusters in all regions and add up to 40 nodes to each cluster. If you want to create more clusters or nodes, submit a job request.
    -   By default, each account can create at most 100 security groups.
    -   By default, each account can create up to 60 Load Balancing instances on a paid basis.
    -   By default, each account can create at most 20 EIPs.
-   Limits for ECS instances are as follows:
    -   Only Centos operating systems are supported.
    -   Creating Pay-As-You-Go and Subscription ECS instances is supported.

## Procedure {#section_cpd_qvv_ydb .section}

1.  Log on to the [Container Service console](https://cs.console.aliyun.com).
2.  Under Kubernetes, click **Clusters** in the left-side navigation pane.
3.  Click **Create Kubernetes Cluster** in the upper-right corner.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15382906669035_en-US.png)

    ****

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/153829066610858_en-US.png)

4.  Enter the name of the cluster.

    The cluster name can be 1–63 characters long and contain numbers, Chinese characters, English letters, and hyphens \(-\).

5.  Select the region and zone where the cluster resides.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15382906669036_en-US.png)

6.  Set the cluster network type. Kubernetes clusters only support the VPC network type.

    You can select **Auto Create**  to create a **Virtual Private Cloud \(VPC\)** together with the Kubernetes cluster or **Use existing** to use an existing VPC.  With **Use Existing** selected, choose the VPC and VSwitch from the appeared drop-down list.

    -   With **Auto Create** selected, the system automatically creates a NAT gateway for your VPC when the cluster is created.
    -   With **Use Existing** selected, if the selected VPC already has a NAT gateway, Container Service uses the existing NAT gateway. Otherwise, the system automatically creates a NAT gateway by default.  If you do not want the system to automatically create a NAT gateway, clear the **Configure SNAT for VPC** check box.

        **Note:** If you select to not automatically create a NAT gateway, configure the NAT gateway on your own to implement the VPC public network environment with secure access, or manually configure the SNAT. Otherwise, instances in the VPC cannot access public network normally, which leads to cluster creation failure.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15382906669037_en-US.png)

7.  Configure the node type, Pay-As-You-Go and Subscription types are supported.
8.  Configure the master nodes.

    Select the generation, family, and type for the master nodes.

    **Note:** 

    -   Only support the Centos operating system.
    -   Currently, you can only create three master nodes.
    -   Supports mounting system disks for the master node, SSD and high-efficiency cloud disks are supported.
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15382906669038_en-US.png)

9.  Configure the worker nodes. Select whether to create a worker node or add an existing ECS instance as the worker node.

    **Note:** 

    -   Only support the Centos operating system
    -   Each cluster can contain at most 37 worker nodes.  To create more nodes, open a ticket.
    -   Supports mounting system disks for the worker node, SSD, high-efficiency, and basic cloud disks are supported. 
    1.  If you want to add an instance, you must generation, family, and type for the worker node., and number for the worker nodes \(in this example, select to create one worker node\).

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15382906679039_en-US.png)

    2.  To add an existing ECS instance as the worker node, you must create an ECS instance in the current region in advance.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15382906679040_en-US.png)

10. Configure the logon mode.
    -   Set the secrets.

        Select the key pair logon mode when creating the cluster, click **New Key Pair**. Go to the ECS console, and create a key pair, see [Create an SSH key pair](../../../../intl.en-US/User Guide/Key pairs/Create an SSH key pair.md#). After the key pair is created, set the key pair as the credentials for logging on to the cluster.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15382906679041_en-US.png)

    -   Set the password.
        -   **Logon Password**: Configure the node logon password.
        -   **Confirm Password**: Confirm your node logon password.
11. Configure the **Pod Network CIDR** and **Service CIDR**.

    **Note:** This option is available when you select to **use an existing** VPC. 

    Specify the **Pod Network CIDR** and **Service CIDR**.  Both of them cannot overlap with the Classless Inter-Domain Routing \(CIDR\) block used by VPC and the existing Kubernetes clusters in VPC, and you cannot modify the values after the cluster is created.  Service address segment cannot be repeated with the Pod address segment. Besides, the service CIDR block cannot overlap with the pod CIDR block. For more information about how to plan the Kubernetes CIDR blocks, see [Plan Kubernetes CIDR blocks under VPC](intl.en-US/User Guide/Kubernetes cluster/集群管理/Plan Kubernetes CIDR blocks under VPC.md#).

12. Set whether to configure a SNAT gateway for a private network.

    **Note:** SNAT must be configured if you select **Auto Create** VPC. If you select **Use existing** VPC, you can select whether to automatically configure SNAT gateway. If you select not to configure SNAT automatically, you can configure the NAT gateway to implement VPC security access to the public network. You can also configure SNAT manually. Otherwise, the VPC cannot access the public network.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15382906679042_en-US.png)

13. Select whether to enable SSH logon for Internet.

    -   With this check box selected, you can access the cluster by using SSH.
    -   If you do not enable SSH logon for Internet, you cannot access the cluster by using SSH or connect to the cluster by using kubectl. to access the cluster by using SSH, manually bind EIP to the ECS instance, configure security group rules, and open the SSH port \(22\). For more information, see [EN-US\_TP\_16642.md\#](intl.en-US/User Guide/Kubernetes cluster/集群管理/SSH访问Kubernetes集群.md#).
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15382906679043_en-US.png)

14. Sets whether the cloud monitoring plug-in is enabled.

    You can choose to install the cloud monitoring plug-in on the ECS node, this allows you to view monitoring information for the created ECS instance in the cloud monitor console.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15382906679044_en-US.png)

15. Sets whether Log Service is enabled. You can use an existing project or create a new project.

    To automatically configure Log Service plug-ins in the cluster, select the **SLS** check box. When creating an application, you can quickly use Log Service with a simple configuration. For more information, see [Use Log Service in a Kubernetes cluster](intl.en-US/User Guide/Kubernetes cluster/Logs/Use Log Service in a Kubernetes cluster.md#).

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15382906679244_en-US.png)

16. Select whether to enable the advanced configurations.
    1.  Enable the network plug-ins,  Flannel and Terway network plug-ins are supported.
        -   Flannel: The Flannel cni plug-in for simple and stable communities.
        -   Terway: Alibaba Cloud Container Service self-developed network plug-in, which supports Alibaba Cloud flexible network card to be distributed to the container, and supports Kubernetes NetworkPolicy to define the inter-container access policy. Supports bandwidth limiting for the separate containers. Currently it is in the public beta.
    2.  Set the number of nodes pod, which is the maximum number of pods that can be run by a single node. We recommend to maintain the  default value.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15382906679045_en-US.png)

    3.  Select whether or not to use the **custom image**.  The ECS instance installs the default CentOS version if no custom image is selected.

        Currently, you can only select an image based on CentOS to deploy the environment you need. For example, the image deployed and tested based on the CentOS 7.2 LAMP.

    4.  Sets whether to use a **Custom cluster CA**. With this check box selected, the CA certificate can be added to the Kubernetes cluster, which enhances the security of information exchange between server and client.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15382906679046_en-US.png)

17. Click **Create Cluster** in the upper-right corner.

**Note:** A multi-node Kubernetes cluster typically takes 10 minutes to be created.

## Viewing cluster deployment results {#section_xhh_hvv_ydb .section}

After the cluster is successfully created, you can view the cluster in the Kubernetes Cluster List of the Container Service console.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15382906679047_en-US.png)

Click **View Logs** at the right of the cluster to view the cluster logs. To view more detailed information, click **Stack Events**.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15382906679048_en-US.png)

You can also click **Manage** at the right of the cluster to view the basic information and connection information of this cluster.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15382906679049_en-US.png)

**In the Connection Information section:**

-   **API Server Internet endpoint**: The address and port used by the Kubernetes API server  to provide the service for the Internet. You can use kubectl or other tools on the user terminal by means of this service to manage the cluster.
-   **API Server intranet endpoint**: The address and port used by the Kubernetes API server  to provide the service for the intranet. This IP address is the address of the Server Load Balancer instance, and three master nodes in the backend are providing the service.
-   **Master node SSH IP address**: You can directly log on to the master  node to perform routine maintenance for the cluster.
-   **Service Access Domain**: Provides the service in the cluster with access domain name for testing.  The service access domain name suffix is `<cluster_id>.<region_id>.alicontainer.com`.

For example, you can log on to the master nodes by using SSH, and run the `kubectl get node` to view the node information of the cluster.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15382906679050_en-US.png)

As shown in the preceding figure, the cluster has four nodes, including three master nodes and one worker node configured when creating the cluster.

