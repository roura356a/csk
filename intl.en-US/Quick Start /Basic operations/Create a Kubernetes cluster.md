# Create a Kubernetes cluster {#task_mmv_33q_n2b .task}

This topic describes how to use the Container Service console to quickly create a Kubernetes cluster. That is, you must retain the default settings for most of the parameters.

Activate the following services: Container Service, Resource Orchestration Service \(ROS\), and Resource Access Management \(RAM\). For more information about the limits and instructions, see [Create a Kubernetes cluster](../../../../reseller.en-US/User Guide/Kubernetes cluster/Clusters/Create a cluster.md#).

Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs), [ROS console](https://partners-intl.console.aliyun.com/#/ros), and [RAM console](https://partners-intl.console.aliyun.com/#/ram) to activate the corresponding services.

This example shows how to create a Kubernetes cluster. Some configurations use the default or the simplest configuration.

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane under Container Service-Kubernetes, choose **Clusters** \> **Clusters**.
3.  In the upper-right corner, click **Create Kubernetes Cluster**. On the Select Cluster Template page, find the **Standard Dedicated Cluster** template, and click **Create** in the template.
4.  Set cluster parameters. 

    Most of the configurations in this example retain the default values. The specific configuration is shown in the following figure.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16128/15646578437352_en-US.png)

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16128/15646578437353_en-US.png)

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16128/156465784351231_en-US.png)

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16128/156465784451232_en-US.png)

    |Configuration|Description|
    |-------------|-----------|
    |Cluster Name|The cluster name must be 1 to 63 characters in length, and can contain letters, numbers, Chinese characters, letters, and hyphens \(-\).|
    |Resource Group|The resource group for the cluster.|
    |Region|The region in which the cluster is located.|
    |VPC|The VPC that can be used by the cluster.     -   If the selected VPC has an existing NAT gateway, ACK then uses the already created NAT gateway.
    -   If the selected VPC has an existing NAT gateway, ACK then uses the already created NAT gateway.undefined

**Note:** If you do not want ACK to automatically create a NAT gateway for your selected VPC, clear **Configure SNAT for VPC**. Then, you must manually create a NAT gateway, or set an SNAT entry to ensure that your selected VPC is accessible to the Internet. Otherwise, instances in the VPC cannot access the Internet, which results in a cluster creation failure.

 |
    |VSwitch|The VSwitches that can be used by the cluster. We recommend that you select three VSwitches that are located in different zones.|
    |Node Type|Pay-As-You-Go and Subscription types are supported.|
    |Duration|The period that you use the nodes. **Note:** If you select the Subscription node type, this parameter takes effect.

 |
    |Auto Renewal|The period that nodes can be automatically renewed. **Note:** If you select the Subscription node type, this parameter takes effect.

 |
    |Master node|Select the number of Master nodes, the instance type, and system disk.     -   **Quantity**: You can select 3 or 5.
    -   **Instance Type**: For more information, see[Instance type families](../../../../reseller.en-US/Instances/Instance type families.md#)
    -   **System Disk**: SSD disk and Ultra Disk are supported.
 |
    |Worker node|You can create Worker node instances for the cluster or add existing Worker node instances to the cluster. To create Worker node instances, set the following parameters:     -   **Instance Type**: Indicates the ECS instance type. You can select multiple ECS instance types. For more information, see [Instance type families](../../../../reseller.en-US/Instances/Instance type families.md#).
    -   **System Disk**: Indicates the type and capacity of the system disk. Available system disks are SSD disks and Ultra disks.
    -   **Attach Data Disk**: Indicates the type and capacity of the data disk. Available data disks are SSD disks, Ultra disks, and basic disks.
 |
    |Docker Version and Kubernetes Version|The Docker version and Kubernetes version.|
    |Login|Key pair and password logon types are supported. For more information, see[Access Kubernetes clusters by using SSH key pairs](../../../../reseller.en-US/User Guide/Kubernetes cluster/Clusters/Access Kubernetes clusters by using SSH key pairs.md#).|
    |Network Plugin|Available network plugins are Flannel and Terway. For more information, see [Do I select the Terway or Flannel plugin for my Kubernetes cluster network?](../../../../reseller.en-US/FAQ/Do I select the Terway or Flannel plugin for my Kubernetes cluster network?.md#)|
    |Pod Network CIDR and Service CIDR|For more information, see [Plan Kubernetes CIDR blocks under VPC](../../../../reseller.en-US/User Guide/Kubernetes cluster/Plan Kubernetes CIDR blocks under VPC.md#).|
    |Configure SNAT|Optional. If you do not set this parameter, you must manually create a NAT gateway or an SNAT entry for the VPC.|
    |Public Access|     -   If you enable this function, ACK then automatically creates an EIP and attaches it to an intranet SLB instance. In this case, the Master node port \(namely, port 6443\) is opened, and you can use the kubeconfig file to connect to and operate the cluster through the Internet.

**Note:** The API server uses the Master node port.

    -   If you do not enable this function, no EIP is created. In this case, you can only use the kubeconfig file to connect to and operate the cluster within your selected VPC.
 |
    |SSH Login|     -   If you select to enable SSH access for Internet, you can access a cluster by using SSH.
    -   If you select not to enable SSH access for Internet, you cannot access a cluster by using SSH or connect to a cluster by using kubectl. You can manually enable SSH access. For more information, see [Access Kubernetes clusters by using SSH](../../../../reseller.en-US/User Guide/Kubernetes cluster/Clusters/Access Kubernetes clusters by using SSH.md#).
 **Note:** To enable this function, you must select**Expose API Server with EIP**.

 |
    |CloudMonitor Agent|You can install a cloud monitoring plug-in on the ECS node to view the monitoring information of the created ECS instances in the CloudMonitor console.|
    |Log Service|Select **Enable Log Service**, and click **Select Project** or **Create Project**. **Note:** 

    -   A project is a space provided by Log Service to store log data.
    -   If you select the check box, ACK then automatically installs the Log Service plugin for the cluster. This plugin allows you to collect logs of applications that run in the cluster and store the logs in the project that you set. For more information, see [EN-US\_TP\_17400.md\#](reseller.en-US/User Guide for Kubernetes Clusters/Log management/Use Log Service to collect Kubernetes cluster logs.md#).
 |
    |RDS Whitelist|Add the IP addresses of the ECS instances to the RDS instance whitelist.|
    |Master Node Protection|By default, this check box is selected to prevent users from releasing Master nodes by using the console or API.|
    |Label|Attach tags to the cluster. To attach tags to a cluster, the following rules apply:

    -   For each tag, a key is required, but its value is optional.
    -   A key can contain up to 64 characters but cannot start with `aliyun`, `http://`, or `https://`. It is not case sensitive.
    -   The value must be 1 to 128 characters in length, and cannot start with `http://` or `https://`. It is not case sensitive.
    -   For a cluster, each tag attached to it must be unique. If you attach a new tag that shares a key with an existing tag to a cluster, the new tag overwrites the existing tag.
    -   A maximum of 20 tags can be attached to a cluster. If you want to continue to attach more tags, you must first remove the necessary number of tags to allow these tags to be added.
 |
    |Advance Options|     -   Set the number of pods for each node.

**Note:** This parameter specifies the maximum number of pods that run on a single node. We recommend that you retain the default setting.

    -   Set the proxy mode for kube-proxy.

        -   `iptables`: This is a mature and stable proxy mode that provides average performance. The service discovery and load balancing feature of a Kubernetes cluster uses `iptables` rules to configure services. This proxy mode is applicable only to a Kubernetes cluster that runs a few services.
        -   `IPVS`: This is a high-performance kube-proxy mode. The service discovery and load balancing feature of a Kubernetes cluster uses the Linux IPVS module to configure services. This proxy mode can meet the requirement of the high-performance load balancing service. Therefore, this proxy mode is applicable to a Kubernetes cluster that runs a large number of services.
    -   Set the range of node ports.

**Note:** By default, the range of node ports is 30000 to 32767.

    -   Set the CUP affinity policy.
        -   **none**: Uses the default CPU affinity policy.
        -   **static**: Allows you to set enhanced CPU affinity for specific pods or even set a CPU exclusive to these pods.
    -   Enable the **Custom Cluster CA** function. A CA certificate ensures secure information exchanges between the server and the client. To enable this function, select the check box to add the CA certificate to the Kubernetes cluster.
    -   Enable the Ags function.
        -   If you select the **AGS** check box, the workflow plugin of Ags is automatically installed on the cluster.
        -   If you do not select the **AGS** check box, you must manually install the workflow plugin of Ags on the cluster to use Ags.
 |

5.  In the upper-right corner, click **Create**. Then, in the displayed dialog box, click **OK**.

On the Cluster List page, verify that the Kubernetes cluster is created.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16128/15646578447356_en-US.png)

