# Create a managed Kubernetes cluster {#task_skz_qwk_qfb .task}

This topic describes how to use the Container Service console to create a managed Kubernetes cluster.

[Container Service](https://cs.console.aliyun.com/), [ROS](https://ros.console.aliyun.com/), [RAM](https://ram.console.aliyun.com/), and [Auto Scaling](https://essnew.console.aliyun.com) must be activated.

**Note:** Alibaba Cloud Resource Orchestration Service \(ROS\) provides the capabilities to deploy resources objects, such as an application and a managed Kubernetes cluster. Therefore, to create a managed Kubernetes cluster with Alibaba Cloud Container Service for Kubernetes \(ACK\), you must activate ROS.

-   The SLB instance created with the cluster supports only the Pay-As-You-Go billing method.
-   Kubernetes clusters support only the Virtual Private Cloud \(VPC\) network type.
-   By default, each Alibaba Cloud account has a specified quota for the cloud resources it can create. If the number of cloud resources exceeds the quota, the account cannot create a cluster. Make sure you have enough quota before creating a cluster. To increase your quota, open a ticket.
    -   By default, each account can create up to 100 security groups.
    -   By default, each account can create up to 60 Pay-As-You-Go SLB instances.
    -   By default, each account can create up to 20 EIPs.

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane under Container Service-Kubernetes, choose **Clusters** \> **Clusters**.
3.  In the upper-right corner, click **Create Kubernetes Cluster**.
4.  On the Select Cluster Template page, click **Standard Managed Cluster**.
5.  Select the resource group for the cluster. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/156351971749901_en-US.png)

6.  Enter the cluster name. 

    The cluster name can be 1–63 characters long and contain numbers, Chinese characters, English letters, and hyphens \(-\).

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/156351971721257_en-US.png)

7.  Select the region where you want to locate the cluster. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/156351971821258_en-US.png)

8.  Select a VPC for the cluster. 

    **Note:** Kubernetes clusters support only the VPC network type.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15635197189037_en-US.png)

    -   If a NAT gateway has been created for the selected VPC, ACK then uses the NAT gateway.
    -   If no NAT gateway has been created for the selected VPC, ACK automatically creates a NAT gateway.undefined

        **Note:** If you do not want ACK to automatically create a NAT gateway for your selected VPC, clear **Configure SNAT for VPC**. Then, you must manually create a NAT gateway, or set an SNAT entry to ensure that your selected VPC is accessible to the Internet. Otherwise, instances in the VPC cannot access the Internet, which results in a cluster creation failure.

9.  Set VSwitches for the cluster. 

    -   Select one to three existing VSwitches.

        **Note:** To ensure that the cluster is highly available, we recommend that you select two or three VSwitches that are located in different zones.

    -   If any of the existing VSwitches cannot meet your requirements, click **Create VSwitch** . For more information, see [Manage VSwitches](../../../../reseller.en-US/User Guide/VPC and subnets/Manage VSwitches.md#).
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/156351971849904_en-US.png)

10. Set Worker nodes. 

    You can create Worker node instances for the cluster or add existing Worker node instances to the cluster.

    -   To create Worker node instances, set these parameters.

        -   **Node Type**: Indicates the billing method of the node. Available billing methods are Pay-As-You-Go and Subscription.
        -   **Instance Type**: Indicates the ECS instance type. You can select multiple ECS instance types. For more information, see [Instance type families](../../../../reseller.en-US/Instances/Instance type families.md#).
        -   **Selected Types**: Indicates the selected ECS instance types.
        -   **Quantity**: Indicates the number of Worker nodes to be created.
        -   **System Disk**: Indicates the type and capacity of the system disk. Available system disks are SSD disks and Ultra disks.
        -   **Attach Data Disk**: Indicates the type and capacity of the data disk. Available data disks are SSD disks, Ultra disks, and basic disks.
        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15635197189039_en-US.png)

    -   To add existing Worker node instances to the cluster, click **Add Existing Instance**.

        **Note:** If no ECS instance is created in the region where you create the cluster, you must first create one or more ECS instances in this region.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15635197189040_en-US.png)

    **Note:** 

    -   At least 2 Worker nodes must be set for a Kubernetes cluster.
    -   Up to 48 Worker nodes can be set for a Kubernetes cluster. To set more nodes, open a ticket.
11. Select the Kubernetes version, and the operating system to run the cluster.![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/156351971841590_en-US.png)

 

    **Note:** 

    -   The Docker version is also displayed.
    -   In this topic, the Linux operating system is selected. You can also select the Windows operating system. For more information, see [Create a Kubernetes cluster that supports Windows](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster that supports Windows.md#).
12. Set the logon type for the Worker nodes. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/156351971921262_en-US.png)

    -   Set the key pair.
        1.  Click **Create a new key pair**.

            **Note:** You must use the ECS console to create a key pair. For more information, see [Create an SSH key pair](../../../../reseller.en-US/Security/Key pairs/Use an SSH key pair.md#).

        2.  Select the key pair that you created from the **Key Pair Name** drop-down list.
    -   Set the password.
        -   **Logon Password**: Set the node logon password.
        -   **Confirm Password**: Confirm your node logon password.
13. Select the network plugin. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/156351971921269_en-US.png)

    Available network plugins are Flannel and Terway. For more information, see [Do I select the Terway or Flannel plugin for my Kubernetes cluster network?](../../../../reseller.en-US/FAQ/Do I select the Terway or Flannel plugin for my Kubernetes cluster network?.md#).

14. Set the **Pod Network CIDR** and **Service CIDR** . 

    **Note:** 

    -   The two Classless Inter-Domain Routing \(CIDR\) blocks cannot overlap with each other, or with your selected VPC, or with the CIDR blocks used by any other existing Kubernetes clusters in the selected VPC. For more information, see [Plan Kubernetes CIDR blocks under VPC](reseller.en-US/Best Practices/Cluster/Plan Kubernetes CIDR blocks under a VPC.md#).
    -   These two CIDR blocks cannot be modified after the cluster is created.
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/156351971921265_en-US.png)

15. Set an SNAT gateway for your selected VPC. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/156351971921267_en-US.png)

    **Note:** 

    -   If you clear **Configure SNAT for VPC**, ACK then does not automatically create a NAT gateway for your selected VPC. In this case, you must manually create a NAT gateway or an SNAT entry for the VPC.
    -   We recommend that you select this check box. Otherwise, instances in the VPC cannot access the Internet, which will results in a cluster creation failure.
16. Set the cluster to be accessible through the Internet by selecting **Expose API Server with EIP**. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/156351971949921_en-US.png)

    The API server provides add, delete, edit, check, watch, and other HTTP REST interfaces for resource objects, such as pods and services.

    -   If you enable this function, ACK then automatically creates an EIP and attaches it to an intranet SLB instance. In this case, the Master node port \(namely, port 6443\) is opened, and you can use the kubeconfig file to connect to and operate the cluster through the Internet.

        **Note:** The API server uses the Master node port.

    -   If you do not enable this function, no EIP is created. In this case, you can only use the kubeconfig file to connect to and operate the cluster within your selected VPC.
17. Install the CloudMonitor plugin on the ECS instances that you set for the cluster. 

    **Note:** If you install the CloudMonitor plugin on the ECS instances, you can use the CloudMonitor console to monitor the ECS instances.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/156351971921268_en-US.png)

18. Install the Log Service plugin on the cluster. Select **Enable Log Service**, and click **Select Project** or **Create Project**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/156351971949924_en-US.png)

    **Note:** 

    -   A project is a space provided by Log Service to store log data.
    -   If you select the check box, ACK then automatically installs the Log Service plugin for the cluster. This plugin allows you to collect logs of applications that run in the cluster and store the logs in the project that you set. For more information, see [Use Log Service to collect Kubernetes cluster logs](reseller.en-US/User Guide/Kubernetes cluster/Log management/Use Log Service to collect Kubernetes cluster logs.md#).
19. Add an RDS instance to the RDS whitelist. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/156351972021270_en-US.png)

20. Attach tags to the cluster. Enter a key and its value, and click **Add**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/156351972049925_en-US.png)

21. Set advanced parameters. 
    -   Set the maximum number of pods that run on a single node.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15635197209045_en-US.png)

        **Note:** We recommend that you retain the default setting.

    -   Set the proxy mode for kube-proxy.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/149635/156351972041576_en-US.png)

        You can set the `iptables` or `IPVS` proxy mode.

        -   `iptables`: This is a mature and stable proxy mode that provides average performance. The service discovery and load balancing feature of a Kubernetes cluster uses `iptables` rules to configure services. This proxy mode is applicable only to a Kubernetes cluster that runs a few services.
        -   `IPVS`: This is a high-performance kube-proxy mode. The service discovery and load balancing feature of a Kubernetes cluster uses the Linux IPVS module to configure services. This proxy mode can meet the requirement of the high-performance load balancing service. Therefore, this proxy mode is applicable to a Kubernetes cluster that runs a large number of services.
    -   Enable the workflow engine Ags.
22. In the upper-right corner, click **Create**. Then, in the displayed dialog box, click **OK**. 

    **Note:** A managed Kubernetes cluster for which multiple nodes are set typically takes about five minutes to be created.


-   On the Cluster List page, you can view the managed Kubernetes cluster that you created.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/156351972021425_en-US.png)

-   In the **Action** column, click **View Logs** to view the cluster logs. To view more detailed logs, click **Stack Events**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/156351972021426_en-US.png)

-   On the Cluster List page, find the created cluster. Then, in the **Action** column, click **Manage** to view details about this cluster and its resources.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/156351972021427_en-US.png)

    -   **API Server Internet endpoint**: the IP address and the port through which the Kubernetes API server provides services to the Internet. It enables you to manage the cluster by using kubectl or other tools on your terminal.
    -   **API Server Intranet endpoint**: the IP address and the port through which the Kubernetes API server provides services within the cluster. This IP address is the SLB instance IP address.
    -   **Pod Network CIDR**: the CIDR block that contains the IP addresses of pods.
    -   **Service CIDR**: the CIDR block that contains the IP addresses of services.
    -   **Testing Domain**: provides the services in the cluster with access domain name for testing. The service access domain name has the following suffix: `<cluster_id>.<region_id>.alicontainer.com`.
    -   Kube-proxy Proxy Mode: the service discovery and load balancing feature uses a proxy mode to configure services. The `iptables` and `IPVS` modes are supported.
    -   **Pod Number for Node**: the maximum number of pods that run on a single node. The default value is 128.
-   Use [kubectl](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Connect to a Kubernetes cluster by using kubectl.md#) to access the created cluster, and then run the kubectl get node command to view the nodes that you set for the cluster.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/156351972121438_en-US.png)


