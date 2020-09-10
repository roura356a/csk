# Create a Kubernetes cluster

Activate the following services: Container Service, Resource Orchestration Service \(ROS\), and Resource Access Management \(RAM\). For more information about the limits and instructions, see [Create a cluster]().

Log on to the [Container Service console](https://cs.console.aliyun.com/), [ROS console](https://ros.console.aliyun.com/), and [RAM console](https://ram.console.aliyun.com/) to activate the corresponding services.

This example shows how to create a Kubernetes cluster. Some configurations use the default or the simplest configuration.

1.  Log on to the [Container Service console](https://cs.console.aliyun.com).

2.  Under Kubernetes, click **Clusters** in the left-side navigation pane. On the displayed page, click **Create Kubernetes Cluster** in the upper-right corner.

3.  Set cluster parameters.

    Most of the configurations in this example retain the default values. The specific configuration is shown in the following figure.

    ![](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/2857693751/p7352.png)

    ![](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/2857693751/p7353.png)

    |Configuration|Description|
    |-------------|-----------|
    |Cluster Name|The cluster name can be 1–63 characters long and contain numbers, Chinese characters, English letters, and hyphens \(-\).|
    |Region and Zone|The region and zone in which the cluster is located.|
    |VPC|You can select **Auto Create** or **Use Existing**.     -   **Auto Create**: The system automatically creates a NAT Gateway for your VPC when a cluster is created.
    -   **Use Existing**: If the selected VPC has a NAT Gateway, Container Service uses the NAT Gateway. Otherwise, the system automatically creates a NAT Gateway by default. If you do not want the system to automatically create a NAT Gateway, deselect the **Configure SNAT for VPC** check box.

**Note:** If you deselect the check box, configure the NAT Gateway on your own to implement the VPC Internet environment with secure access, or manually configure the SNAT. Otherwise, instances in the VPC cannot access the Internet normally, which leads to cluster creation failure. |
    |Node Type|Pay-As-You-Go and Subscription types are supported.|
    |MASTER Configuration|Select an instance type and system disk.     -   **Instance Type**: For details, see[Instance families](/intl.en-US/Instance/Instance families.md)
    -   **System Disk**: SSD disk and Ultra Disk are supported. |
    |WORKER Configuration|You can select to create a Worker node or add existing ECS instances. If you select to add an instance, you can configure it as follows.     -   **Instance Type**: For details, see[Instance families](/intl.en-US/Instance/Instance families.md)
    -   **System Disk**: SSD Disk and Ultra Disk are supported.
    -   **Attach Data Disk**: SSD Disk, Ultra Disk, and Basic Disk are supported. |
    |Login|Key Pair and Password are supported. For details, see[Access Kubernetes clusters by using SSH key pairs]()|
    |Pod Network CIDR and Service CIDR \(optional\)|For more information about the specific plan, see [Plan Kubernetes CIDR blocks under VPC](). **Note:** This option is available when you select **Use Existing** VPC. |
    |Configure SNAT|SNAT must be configured if you select **Auto Create** a VPC. If you select **Use Existing** VPC, you can select whether to automatically configure SNAT Gateway. If you select not to configure SNAT automatically, configure the NAT Gateway or configure SNAT manually.|
    |SSH Login|    -   If you select to enable SSH access for Internet, you can access a cluster by using SSH.
    -   If you select not to enable SSH access for Internet, you cannot access a cluster by using SSH or connect to a cluster by using kubectl. You can manually enable SSH access. For details, see [Access Kubernetes clusters by using SSH](). |
    |Monitoring Plug-in|You can install a cloud monitoring plug-in on the ECS node to view the monitoring information of the created ECS instances in the CloudMonitor console.|
    |RDS Whitelist \(optional\)|Add the IP addresses of the ECS instances to the RDS instance whitelist. **Note:** This option is available when select to **Use Existing** VPC. |
    |Show Advance Config|    -   Network Plugin: Flannel and Terway network plug-ins are supported. By default, Flannel is used. For details, see [How to select the network plug-in: Terway or Flannel]().
    -   Pod Number for Node: Maximum number of pods that can be run by a single node.
    -   Custom Image: Indicates whether to install a custom image. The ECS instance installs the default CentOS version if no custom image is selected.
    -   Cluster CA: Indicates whether to use a custom cluster CA. |

4.  Click **Create Cluster** in the upper-right corner.


After the cluster is successfully created, you can view the cluster in the Cluster List.

![](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/6837197951/p7356.png)

Now you have quickly created a Kubernetes cluster.

