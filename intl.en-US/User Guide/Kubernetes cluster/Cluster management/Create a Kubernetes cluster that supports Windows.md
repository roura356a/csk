# Create a Kubernetes cluster that supports Windows {#concept_186587 .concept}

This topic describes how to use the Container Service console to create a Kubernetes cluster that supports Windows.

## Prerequisites {#section_xq3_nzh_mhb .section}

The following services are activated: Container Service, Resource Orchestration Service \(ROS\), Resource Access Management \(RAM\), and Auto Scaling service.

Log on to the [Container Service console](https://cs.console.aliyun.com/), [ROS console](https://ros.console.aliyun.com/), [RAM console](https://ram.console.aliyun.com/), and [Auto Scaling console](https://essnew.console.aliyun.com) to activate the corresponding services.

**Note:** The deployment of Kubernetes clusters that support Windows in Container Service is dependent on the application deployment capabilities of Alibaba Cloud ROS. Therefore, you must activate ROS before you create a Kubernetes cluster that supports Windows.

## Limits {#section_yq3_nzh_mhb .section}

-   An SLB instance created with the cluster supports only the Pay-As-You-Go billing method.
-   Kubernetes clusters that support Windows support only the VPC network type.
-   Each account has a set quota of resources that can be used to create clusters. The default numbers are as follows:

    -   Each account can create up to 100 security groups.
    -   Each account can create up to 60 SLB instances of the Pay-As-You-Go billing method.
    -   Each account can create up to 20 EIPs.
    **Note:** If any of the preceding quotas are exceeded when you create a cluster, the cluster fails to be created. In the case that you need a larger quota for any of these resources, you can open a ticket.


## Procedure {#section_br3_nzh_mhb .section}

1.  Log on to the [Container Service console](https://cs.console.aliyun.com/).
2.  In the left-side navigation pane under Container Service-Kubernetes, choose **Clusters** \> **Clusters**. Then click **Create Kubernetes Cluster** in the upper-right corner.
3.  On the displayed page, find **Windows Cluster**, and then click **Create**.
4.  Enter the cluster name.

    **Note:** The cluster name must be 1 to 63 characters in length, and can contain letters, numbers, Chinese characters, and hyphens \(-\).

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/149635/155619763841558_en-US.png)

5.  Select the region and zone where you want to locate the cluster.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/149635/155619763841560_en-US.png)

6.  Select a VPC.

    **Note:** Kubernetes clusters support only the VPC network type.

    **VPC**: You can click **Auto Create** to create a VPC together with the Kubernetes cluster, or click **Use Existing** to use an existing VPC. If you click **Use Existing**, you can select a VPC and a VSwitch from the two displayed drop-down lists.

    -   If you click **Auto Create**, the system automatically creates a NAT gateway for a VPC when you create a cluster.
    -   If you click **Use Existing** and the selected VPC is associated with an existing NAT gateway, the system then uses the preexisting NAT gateway. If the selected VPC is not associated with any existing NAT gateway, the system automatically creates a NAT gateway for the selected VPC. Furthermore, if you do not want the system to automatically create a NAT gateway for the selected VPC, clear the **Configure SNAT for VPC** check box.

        **Note:** If you set the system not to create a NAT gateway automatically, then you need to manually set a NAT gateway, or set an SNAT entry to ensure that your selected VPC is accessible to the Internet. Otherwise, instances in the VPC cannot access the Internet, which results in a cluster creation failure.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/149635/155619763845005_en-US.png)

7.  Set the node type by selecting a node billing method.

    **Note:** Pay-As-You-Go and Subscription are supported.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/149635/155619763845006_en-US.png)

8.  Set the Worker instance.

    **Note:** 

    -   Each cluster must contain at least 2 Worker nodes.
    -   Each cluster can contain up to 48 Worker nodes. To create more Worker nodes, open a ticket.
    -   By default, a system disk is attached to Worker nodes. For the system disk, available system disk types are SSD disks and Ultra disks.
    -   You can manually attach a data disk to Worker nodes. For data disks, available data types are SSD disks and Ultra disks.
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/149635/155619763845007_en-US.png)

9.  Select the Kubernetes version that supports the Windows operating system. By default, the Kubernetes version with this support is selected.

    **Note:** The system does not display the **Operating Stem** option if you select the Kubernetes version that does not support the Windows operating system.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/149635/155619763845008_en-US.png)

10. Select the Windows operating system.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/149635/155619763945009_en-US.png)

11. Set the logon password.

    -   **Logon Password**: Set the node logon password.
    -   **Confirm Password**: Confirm your node logon password.
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/149635/155619763945010_en-US.png)

12. Set the **Pod Network CIDR** and **Service CIDR**.

    **Note:** 

    -   If you choose to use an existing VPC, we recommend that you set these two parameters.
    -   The two Classless Inter-Domain Routing \(CIDR\) blocks cannot overlap with each other, or with your selected VPC, or with the CIDR blocks used by any other existing Kubernetes clusters in the selected VPC. Furthermore, they cannot be modified after the cluster is created. For more information, see [Plan Kubernetes CIDR blocks under VPC](intl.en-US/Best Practices/Cluster/Plan Kubernetes CIDR blocks under a VPC.md#).
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/149635/155619763945011_en-US.png)

13. Configure an SNAT gateway for the VPC.

    **Note:** We recommend that you configure this setting. Otherwise, instances in the VPC cannot access the Internet, which will result in a cluster creation failure.

    -   If you set the system to automatically create a VPC, you must configure an SNAT gateway for the VPC.
    -   If you set the system to use an existing VPC, you can perform one of the following two operations:
        -   Set the system to automatically configure an SNAT gateway.
        -   Manually configure a NAT gateway or SNAT gateway for the VPC.
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/149635/155619763945012_en-US.png)

14. Enable the **Use Public SLB to Expose API Server** function.

    The Kubernetes API supports such RESTful API actions as retrieving, creating, querying, updating, and deleting resources, such as pods and services.

    -   If you enable this function by selecting the check box, an Internet SLB instance is created and the Master node port \(namely, port 6443\) is opened. In this case, you can use kubeconfig to connect to and operate the cluster through the Internet.

        **Note:** The API server uses the Master node port.

    -   If you do not enable this function, no Internet SLB instance is created. In this case, you can only use kubeconfig to connect to and operate the cluster within the VPC.
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/149635/155619763945013_en-US.png)

15. Enable Log Service.

    You can select an existing project or create a new project.

    If you select the **Using Log Service** check box, a Log Service plugin is automatically installed in the cluster. Then, when you create an application in the cluster, you can immediately use Log Service with only a few configurations required. For more information, see [Use Log Service to collect Kubernetes cluster logs](intl.en-US/User Guide/Kubernetes cluster/Log management/Use Log Service to collect Kubernetes cluster logs.md#).

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/149635/155619763945014_en-US.png)

16. Set the RDS instance whitelist.

    Add the IP address of the node to the RDS instance whitelist.

    **Note:** If you choose to use an existing VPC, we recommend that you set this parameter.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/149635/155619763945015_en-US.png)

17. Set advanced configurations.

    Set the number of pods for a node. This parameter specifies the maximum number of pods that can be run by a single node. We recommend that you retain the default setting.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/149635/155619763945016_en-US.png)

18. Click **Create**, and then click **Create** in the displayed dialog box.

    **Note:** A Kubernetes cluster that contains multiple nodes typically takes ten minutes to be created.


## Verify the result {#section_xr3_nzh_mhb .section}

After the cluster is created, you can view the cluster in the cluster list of the Container Service console.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/149635/155619763945043_en-US.png)

-   To view the cluster logs, click **View Logs** on the right of the cluster. To view more details, click**Stack Events**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/149635/155619763945045_en-US.png)

-   To view the basic information, the connection information, and other information, return to the cluster list page and click Manage in the action column of the cluster.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/149635/155619763945046_en-US.png)


**Cluster information** 

-   **API Server Internet endpoint**: the IP address and the port through which the Kubernetes API server provides services to the Internet. It enables you to manage the cluster by using kubectl or other tools on your terminal.
-   **API Server Intranet endpoint**: the IP address and the port through which the Kubernetes API server provides services within the cluster. This IP address is the SLB instance IP address. The three Master nodes on its backend provide services.
-   **Pod Network CIDR**: the CIDR block where the pods of a Kubernetes cluster are located.
-   **Service CIDR**: the CIDR block where the services of a Kubernetes cluster are located.

For example, you can log on to the Master node by using SSH \(for more information, see [Connect to a Kubernetes cluster by using kubectl](intl.en-US/User Guide/Kubernetes cluster/Cluster management/Connect to a Kubernetes cluster by using kubectl.md#)\), and run the `kubectl get node`command to view the cluster nodes.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/149635/155619763941601_en-US.png)

