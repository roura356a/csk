# Create a Kubernetes cluster {#CS_user_guide_kubernetes .concept}

This topic describes how to quickly create a Kubernetes cluster in the Container Service console.

## Detail analysis {#section_sgh_hvv_ydb .section}

During cluster creation, Container Service performs the following operations:

-   Creates Elastic Compute Service \(ECS\) instances, sets the public key used for SSH logon from the management node to other nodes, and installs and configures the Kubernetes cluster by using cloud-init.
-   Creates a security group to allow inbound access to all ICMP ports in a Virtual Private Cloud \(VPC\).
-   Creates a new VPC and VSwitch \(if no existing VPC is selected\), and creates an SNAT entry for the VSwitch.
-   Creates VPC routing rules.
-   Creates a NAT gateway and a shared bandwidth package or Elastic IP \(EIP\).
-   Creates a Resource Access Management \(RAM\) user and an AccessKey. The RAM user has the permissions to query, create, and delete an ECS instance, add and delete a cloud disk, and all permissions to perform required actions on Server Load Balancer \(SLB\) instances, CloudMonitor, VPC, Log Service, and the Network Attached Storage \(NAS\) service. The Kubernetes cluster dynamically creates SLB instances, cloud disks, and VPC routing rules according to your configurations.
-   Creates an intranet SLB instance and allows access to port 6443.
-   Creates an Internet SLB instance and allows access to port 6443. \(If you enable the SSH logon for Internet access when creating the cluster, port 22 is opened. Otherwise, port 22 is closed.\)

## Prerequisites {#section_ugh_hvv_ydb .section}

You have activated the following services: Container Service, Resource Orchestration Service \(ROS\), RAM, and Auto Scaling service.

If you have not activated one or some of these services, log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs), [ROS console](https://partners-intl.console.aliyun.com/#/ros), [RAM console](https://partners-intl.console.aliyun.com/#/ram), and [Auto Scaling console](https://partners-intl.console.aliyun.com/#/essnew) to activate the corresponding service or services.

**Note:** The deployment of Kubernetes clusters in Alibaba Cloud Container Service for Kubernetes is dependent on the application deployment capabilities of Alibaba Cloud ROS. Therefore, you need to activate ROS before creating a Kubernetes cluster.

## Limits {#section_kqj_pvv_ydb .section}

-   The SLB instances created with the cluster support only the Pay-As-You-Go billing method.
-   The Kubernetes cluster supports only the VPC network type.
-   By default, each account has a set quota of cloud resources that can be used to create clusters. If you exceed this number when creating a cluster, the cluster creation fails. However, you can open a ticket to increase your quota of cloud resources.
    -   By default, each account can create up to 5 clusters across all regions and add up to 40 nodes to each cluster. You can open a ticket to increase your quota to create more clusters or nodes.

        **Note:** In a Kubernetes cluster, the maximum number of default routes a VPC can have is 48, which means the number of nodes the Kubernetes cluster can use is also a maximum of 48. To increase the number of nodes, you need to first open a ticket for the target VPC to increase the number of VPC routes, and then open a ticket for Container Service to apply the increase.

    -   By default, each account can create up to 100 security groups.
    -   By default, each account can create up to 60 Pay-As-You-Go SLB instances.
    -   By default, each account can create up to 20 EIPs.
-   The limits for ECS instances are as follows:
    -   Only the CentOS operating system is supported.
    -   Both Pay-As-You-Go and Subscription ECS instances can be created.

## Procedure {#section_cpd_qvv_ydb .section}

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane under Kubernetes, click **Clusters**.
3.  In the upper-right corner, click **Create Kubernetes Cluster**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15520169439035_en-US.png)

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/155201694310858_en-US.png)

4.  Enter the cluster name.

    The cluster name must be 1 to 63 characters in length and can contain letters, numbers, Chinese characters, letters, and hyphens \(-\).

5.  Select the region and the zone where you want to locate the cluster.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15520169449036_en-US.png)

6.  Set the cluster network type. Kubernetes clusters support only the VPC network type.

    **VPC**: You can click **Auto Create** to create a VPC together with the Kubernetes cluster, or click **Use Existing** to use an existing VPC. If you click **Use Existing**, you can select a VPC and a VSwitch from the two displayed drop-down lists.

    -   If you click **Auto Create**, the system automatically creates a NAT gateway for your VPC when creating the cluster.
    -   If you click **Use Existing** and the selected VPC has an existing NAT gateway, the system then uses the already created NAT gateway. Otherwise, the system automatically creates a NAT gateway by default. Alternatively, if you do not want the system to automatically create a NAT gateway, deselect the **Configure SNAT for VPC** check box.

        **Note:** If you set the system to not automatically create a NAT gateway, you need to manually set a NAT gateway, or set an SNAT entry to ensure that your selected VPC is accessible to the Internet. Otherwise, instances in the VPC cannot access the Internet, which result in a cluster creation failure.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15520169449037_en-US.png)

7.  Set the required billing method for the node type. Pay-As-You-Go and Subscription are supported.
8.  Set the Master node configuration by selecting the Master node instance type and the system disk.

    **Note:** 

    -   Only the CentOS operating system is supported.
    -   By default, the system creates three Master nodes for each cluster. This number cannot be modified.
    -   A system disk is attached to Master nodes by default. Available system disks are SSD disks and Ultra disks.
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15520169449038_en-US.png)

9.  Set Worker nodes. You can create Worker node instances or add existing Worker node instances.

    **Note:** 

    -   Only the CentOS operating system is supported.
    -   Each cluster can contain up to 37 Worker nodes. To create more Worker nodes, open a ticket.
    -   A system disk is attached to Worker nodes by default. Available system disks are SSD disks and Ultra disks.
    -   You can manually attach a data disk to Worker nodes. Available data disks are SSD disks, Ultra disks, and basic disks.
    -   To create Worker node instances, select the instance type and set the number of Worker nodes. In this example, one Worker node is created.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15520169449039_en-US.png)

    -   To add existing Worker node instances, you must create ECS instances in the target region in advance.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15520169449040_en-US.png)

10. Set the logon mode.
    -   Set a key pair.

        Click **Create a new key pair** to create a key pair in the ECS console, and then set the key pair as the credentials for logging on to the cluster. For more information, see [Create an SSH key pair](../../../../../reseller.en-US/Security/Key pairs/Create an SSH key pair.md#).

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15520169449041_en-US.png)

    -   Set a password.
        -   **Logon Password**: Set the node logon password.
        -   **Confirm Password**: Confirm your node logon password.
11. Set the **Pod Network CIDR** and **Service CIDR**.

    **Note:** You need to set these two parameters only if you choose to use an existing VPC.

    The two Classless Inter-Domain Routing \(CIDR\) blocks cannot overlap with each other, or with your selected VPC, or with the CIDR blocks used by any other existing Kubernetes clusters in the selected VPC. Furthermore, they cannot be modified after the cluster is created. For more information, see [Plan Kubernetes CIDR blocks under VPC](reseller.en-US/Best Practices/Cluster/Plan Kubernetes CIDR blocks under VPC.md#).

12. Select a Kubernetes version as needed. The system displays the Kubernetes version and the Docker versions.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/155201694412780_en-US.png)

13. Configure an SNAT gateway for the VPC.

    **Note:** We recommend that you configure this feature. Otherwise, instances in the VPC cannot access the Internet, which will result in a cluster creation failure.

    -   If you set the system to automatically create a VPC, you must configure an SNAT gateway for the VPC.
    -   If you set the system to use an existing VPC, you can perform either of the following operations:
        -   Set the system to automatically configure an SNAT gateway.
        -   Manually configure a NAT gateway for the VPC or manually configure an SNAT gateway for the VPC.
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15520169449042_en-US.png)

14. Enable the **Use Public SLB to Expose API Server** function.

    The API server provides add, delete, edit, check, watch, and other HTTP REST interfaces for resource objects, such as pods and services.

    -   If you enable this function by selecting the check box, an Internet SLB is created and the Master node port \(namely, port 6443\) is opened. In this case, you can use kubeconfig to connect to and operate the cluster through the Internet.

        **Note:** The API server uses the Master node port.

    -   If you do not enable this function, no Internet SLB is created. In this case, you can only use kubeconfig to connect to and operate the cluster within the VPC.
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16644/155201694412073_en-US.png)

15. Enable the Internet SSH logon.

    **Note:** To enable this function, you must enable the **Use Public SLB to Expose API Server** function.

    -   If you enable this function, you can use SSH to access the cluster.
    -   If you do not enable this function, the cluster cannot be accessed by using SSH or kubectl. To access the cluster by using SSH in such a case, you must manually associate an EIP to the ECS instance, set a security group rule, and open the SSH port \(port 22\). For more information, see [Access a Kubernetes cluster by using SSH](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Access a Kubernetes cluster by using SSH.md#).
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15520169449043_en-US.png)

16. Install cloud monitoring plug-ins on your ECS node instances.

    If you install cloud monitoring plug-ins on the ECS nodes, you can view the ECS instance monitoring information in the CloudMonitor console.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15520169449044_en-US.png)

17. Enable Log Service. You can select an existing project or create a new project.

    If you select the **Using SLS** check box, a Log Service plugin is automatically installed in the cluster. Then, when you create an application in the cluster, you can immediately use Log Service with only a few configurations required. For more information, see [Use Log Service to collect Kubernetes cluster logs](reseller.en-US/User Guide/Kubernetes cluster/Log management/Use Log Service to collect Kubernetes cluster logs.md#).

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15520169449244_en-US.png)

18. Set advanced configurations.
    1.  Select a network plugin. Available network plugins are Flannel and Terway. For more information, see [Do I select the Terway or Flannel plugin for my Kubernetes cluster network?](../../../../../reseller.en-US/FAQ/Do I select the Terway or Flannel plugin for my Kubernetes cluster network?.md#).
    2.  Set the number of pods for a node. This parameter specifies the maximum number of pods that can be run by a single node. We recommend that you retain the default setting.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15520169449045_en-US.png)

    3.  Enable the **Custom Cluster CA** function. A CA certificate ensures secure information exchanges between the server and the client. To enable this function, select the check box to add the CA certificate to the Kubernetes cluster.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15520169449046_en-US.png)

19. Click **Create** in the upper-right corner.

**Note:** A Kubernetes cluster that contains multiple nodes typically takes ten minutes to be created.

## View the cluster deployment result {#section_xhh_hvv_ydb .section}

After the cluster is created, you can view the cluster in the cluster list of the Container Service console.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15520169449047_en-US.png)

-   To view the cluster logs, click **View Logs** on the right of the cluster. To view more details, click **Stack Events**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15520169449048_en-US.png)

-   To view the basic information, the connection information, and other information, return to the cluster list page and click **Manage** in the action column of the cluster.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15520169459049_en-US.png)

    **Cluster information**

    -   **API Server Internet endpoint**: the IP address and the port through which the Kubernetes API server provides services to the Internet. It enables you to manage the cluster by using kubectl or other tools on your terminal.
    -   **API Server Intranet endpoint**: the IP address and the port through which the Kubernetes API server provides services within the cluster. This IP address is the SLB instance IP address. The three Master nodes on its backend provide services.
    -   **Master node SSH IP address**: You can directly log on to Master nodes by using SSH to maintain the cluster.
    -   **Service Access Domain**: provides the services in the cluster with access domain name for testing. The service access domain name has the following suffix: `<cluster_id>.<region_id>.alicontainer.com`.
    For example, you can log on to the Master node by using SSH, and run the `kubectl get node` command to view the cluster nodes.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15520169459050_en-US.png)

    In this example, the system displays four nodes, which are the three Master nodes and one Worker node.


