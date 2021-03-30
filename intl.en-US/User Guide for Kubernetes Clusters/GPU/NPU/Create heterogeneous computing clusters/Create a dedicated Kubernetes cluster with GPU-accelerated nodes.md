# Create a dedicated Kubernetes cluster with GPU-accelerated nodes

This topic describes how to create a dedicated Kubernetes cluster for heterogeneous computing in the Container Service for Kubernetes \(ACK\) console.

You must perform the following steps in the ACK console to create an ACK cluster:

-   Create ECS instances, configure a public key to enable Secure Shell \(SSH\) logon from master nodes to other nodes, and then configure the ACK cluster by using cloud-init.
-   Create a security group that allows access to a VPC network through Internet Control Message Protocol \(ICMP\).
-   Select a VPC network for the cluster. If no VPC network is available or you do not want to use an existing VPC network, create a VPC network and VSwitches, and create Source Network Address Translation \(SNAT\) entries for the VSwitches.
-   Add route entries to the VPC network.
-   Create a Network Address Translation \(NAT\) gateway and EIPs.
-   Create a Resource Access Management \(RAM\) user and an AccessKey pair. Grant the following permissions to the RAM user: permissions to query, create, and delete ECS instances, permissions to add and delete cloud disks, and full permissions on SLB, Cloud Monitor, VPC, Log Service, and Network Attached Storage \(NAS\). The ACK cluster automatically creates SLB instances, cloud disks, and VPC routing entries based on your configurations.
-   Create an internal SLB instance and open port 6443.
-   Create a public-facing SLB instance and open ports 6443, 8443, and 22. If you enable SSH logon when you create the cluster, port 22 is opened. Otherwise, port 22 is closed.

## Limits

-   SLB instances that are created together with the ACK cluster support only the pay-as-you-go billing method.
-   ACK clusters support only VPC networks.
-   Each account can consume only a limited amount of computing resources. You fail to create clusters if you do not have sufficient computing resources. When you create clusters, make sure that you have sufficient resources.

    To increase the quota of computing resources for your account, submit a ticket.

    -   You can create up to 50 clusters across regions for each account. You can deploy up to 100 nodes in each cluster. To increase the quota of clusters or nodes for your account, [submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).

        **Note:** By default, you can add up to 48 route entries to each VPC network in an ACK cluster. This means that you can deploy up to 48 nodes in an ACK cluster that uses Flannel. An ACK cluster that uses Terway is not subject to this limit. To increase the quota of route entries for a VPC network, [submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).

    -   You can create up to 100 security groups under each account.
    -   You can create up to 60 pay-as-you-go SLB instances under each account.
    -   You can create up to 20 EIPs under each account.
-   Limits on ECS instances:

    The pay-as-you-go and subscription billing methods are supported.

    **Note:** After an ECS instance is created, you can change its billing method from pay-as-you-go to subscription in the ECS console. For more information, see [Change the billing method of an instance from pay-as-you-go to subscription](/intl.en-US/Pricing/Change the billing method/Change the billing method of an instance from pay-as-you-go to subscription.md).


## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  In the left-side navigation pane of the ACK console, choose **Clusters** \> **Clusters**.

4.  On the Clusters page, click **Create Kubernetes Cluster** in the upper-right corner of the page. In the Select Cluster Template dialog box, find **Dedicated Cluster for Heterogeneous Computing** and click **Create**. Configure the cluster on the Dedicated Kubernetes tab.

5.  In the upper-right corner of the Clusters page, click **Cluster Template**.

6.  In the Select Cluster Template dialog box, find **Dedicated Cluster for Heterogeneous Computing** in the **Other Clusters** section and click **Create**.

7.  On the **Dedicated Kubernetes** tab, configure the cluster.

    1.  Configure basic settings of the cluster.

        |Parameter|Description|
        |---------|-----------|
        |**Cluster Name**|Enter a name for the cluster. **Note:** The name must be 1 to 63 characters in length, and can contain digits, letters, and hyphens \(-\). |
        |**Region**|Select the region where you want to deploy the cluster.|
        |**Time Zone**|Select a time zone for the ACK cluster. By default, the time zone configured for your browser is selected. |
        |**Resource Group**|Move the pointer over **All Resources** at the top of the page and select the resource group to which the cluster belongs. The name of the selected resource group is displayed in the Resource Group field.![Resource group](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9688404061/p127165.png) |
        |**Kubernetes Version**|Select a Kubernetes version.|
        |**Container Runtime**|**Containerd**, **Docker**, and **Sandboxed-Container** are supported. For more information, see [Comparison of Docker, containerd, and Sandboxed-Container](/intl.en-US/User Guide for Kubernetes Clusters/Sandboxed-Container management/Comparison of Docker, containerd, and Sandboxed-Container.md).

**Note:** The Containerd runtime is in public preview. To use Containerd, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm) to apply for it to be available for your account. |
        |**VPC**|Select a virtual private cloud \(VPC\) to deploy the cluster. Shared VPCs and standard VPCs are supported.         -   Shared VPC: The owner of a VPC \(resource owner\) can share vSwitches in the VPC with other accounts in the same organization.
        -   Standard VPC: The owner of a VPC \(resource owner\) cannot share vSwitches in the VPC with other accounts.
**Note:** ACK clusters support only VPCs. You can select a VPC from the drop-down list. If no VPC is available, click **Create VPC** to create one. For more information, see [Work with VPCs](/intl.en-US/VPCs and vSwitchs/Work with VPCs.md). |
        |**VSwitch**|Set the vSwitch. You can select up to three vSwitches that are deployed in different **zones**. If no vSwitch is available, click **Create VSwitch** to create one. For more information, see [t2436.md\#](/intl.en-US/VPCs and vSwitchs/Work with vSwitches.md). |
        |**Network Plug-in**|Select and configure the network plug-in. Flannel and Terway are available. For more information, see [t64408.md\#section\_k1z\_f1n\_lmh](/intl.en-US/User Guide for Kubernetes Clusters/Network/Container network/Work with Terway.md).         -   Flannel: an open source Container Network Interface \(CNI\) plug-in, which is simple and stable. Flannel provides a few simple features and does not support standard Kubernetes network policies.
        -   Terway: a network plug-in developed by Alibaba Cloud for ACK. Terway allows you to assign Alibaba Cloud elastic network interfaces \(ENIs\) to containers and use standard Kubernetes network policies to regulate how containers communicate with each other. Terway also supports bandwidth throttling on individual containers.

**Note:**

            -   The number of pods that can be deployed on a node depends on the number of ENIs that are attached to the node and the maximum number of secondary IP addresses provided by these ENIs.
            -   If you select a shared VPC for the cluster, you must select the Terway network plug-in. |
        |**Pod CIDR Block**|If you select **Flannel**, you must set the **Pod CIDR Block** parameter. The CIDR block specified by **Pod CIDR Block** cannot overlap with that of the VPC or the CIDR blocks of existing ACK clusters in the VPC. After you create the cluster, you cannot modify the pod CIDR block. In addition, the Service CIDR block cannot overlap with the pod CIDR block. For more information about subnetting for ACK clusters, see [Plan CIDR blocks for an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Network/Plan CIDR blocks for an ACK cluster.md). |
        |**Terway Mode**|If you set Network Plug-in to **Terway**, the **Terway Mode** parameter is available. When you set **Terway Mode**, you can select or clear **Assign One ENI to Each Pod**.

        -   If you select **Assign One ENI to Each Pod**, an ENI is assigned to each pod.
        -   If you clear **Assign One ENI to Each Pod**, an ENI is shared among multiple pods. A secondary IP address of the ENI is assigned to each pod.
**Note:** This feature is available to only users in the whitelist. To apply to be added to the whitelist, [submit a ticket](https://selfservice.console.aliyun.com/ticket/scene/ecs/%E4%BA%91%E6%9C%8D%E5%8A%A1%E5%99%A8%20ECS/detail). |
        |**Pod VSwitch**|If you set Network Plug-in to **Terway**, the **Pod VSwitch** parameter is available. **Pod VSwitch** specifies vSwitches for pods. The ENIs that are assigned to pods must be in the same zone as the nodes that host the pods. For each vSwitch that is assigned to nodes, select a vSwitch for pods in the same zone. Pod vSwitches assign IP addresses to pods when the cluster is started. We recommend that you select vSwitches whose prefix length is no greater than 19 bits. This ensures a sufficient number of pods.|
        |**Service CIDR**|Set the **Service CIDR** parameter. The CIDR block specified by **Service CIDR** cannot overlap with that of the VPC or the CIDR blocks of existing ACK clusters in the VPC. After you create the cluster, you cannot modify the Service CIDR block. In addition, the Service CIDR block cannot overlap with the pod CIDR block. For more information about subnetting for ACK clusters, see [Plan CIDR blocks for an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Network/Plan CIDR blocks for an ACK cluster.md).|
        |**IP Addresses per Node**|If you select **Flannel**, you must specify a value for the **IP Addresses per Node** parameter. **Note:** **IP Addresses per Node** specifies the maximum number of IP addresses that can be assigned to each node. We recommend that you use the default value. |
        |**Configure SNAT**|Specify whether to configure Source Network Address Translation \(SNAT\) rules for the VPC.         -   If the specified VPC has a Network Address Translation \(NAT\) gateway, ACK uses this NAT gateway.
        -   Otherwise, the system automatically creates a NAT gateway. If you do not want the system to create a NAT gateway, clear **Configure SNAT for VPC**. In this case, you must manually create a NAT gateway and configure SNAT rules to enable Internet access for the VPC. Otherwise, ACK clusters in the VPC cannot access the Internet and the cluster cannot be created. |
        |**Access to API Server**|By default, an internal-facing Server Load Balancer \(SLB\) instance is created for the cluster API server. You can modify the specifications of the SLB instance. For more information, see [Instance types and specifications](/intl.en-US/Classic Load Balancer/User Guide/Instance/SLB instance overview.md).

**Note:** If you delete the SLB instance, you cannot access the API server.

Select or clear **Expose API Server with EIP**. The ACK API server provides multiple HTTP-based RESTful APIs, which can be used to create, delete, modify, query, and monitor resources, such as pods and Services.

        -   If you select this check box, an elastic IP address \(EIP\) is created and attached to an Internet-facing SLB instance. Port 6443 used by the API server is opened on master nodes. You can connect to and manage the ACK cluster by using kubeconfig over the Internet.
        -   If you clear this check box, no EIP is created. You can connect to and manage the ACK cluster only by using kubeconfig from within the VPC. |
        |**SSH Logon**|To enable Secure Shell \(SSH\) logon, you must first select **Expose API Server with EIP**.

        -   If you select Use SSH to Access the Cluster from the Internet, you can access the cluster through SSH.
        -   If you clear Use SSH to Access the Cluster from the Internet, you cannot access the cluster through SSH or kubectl. If you want to access an Elastic Compute Service \(ECS\) instance in the cluster through SSH, you must manually bind an elastic IP address \(EIP\) to the ECS instance and configure security group rules to open SSH port 22. For more information, see [t16642.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use SSH to connect to an ACK cluster.md). |
        |**RDS Whitelist**|Set the Relational Database Service \(RDS\) whitelist. Add the IP addresses of the nodes in the cluster to the RDS whitelist.|
        |**Security Group**|You can select **Create Basic Security Group**, **Create Advanced Security Group**, or **Select Existing Security Group**. For more information, see [Overview](/intl.en-US/Security/Security groups/Overview.md). |

    2.  Configure advanced settings.

        |Parameter|Description|
        |---------|-----------|
        |**Kube-proxy Mode**|iptables and IPVS are supported.

        -   iptables is a kube-proxy mode. It uses iptables rules to conduct Service discovery and load balancing. The performance of this mode is limited by the size of the cluster. This mode is suitable for clusters that run a small number of Services.
        -   IPVS is a high-performance kube-proxy mode. It uses Linux IP Virtual Server \(IPVS\) to conduct Service discovery and load balancing. This mode is suitable for clusters that run a large number of Services. We recommend that you use this mode in scenarios where high-performance load balancing is required. |
        |**Labels**|Add labels to the nodes in the cluster. Enter keys and values, and then click **Add**.

**Note:**

        -   Key is required. Value is optional.
        -   Keys are not case-sensitive. A key must not exceed 64 characters in length, and cannot start with aliyun, http://, or https://.
        -   Values are not case-sensitive. A value must not exceed 128 characters in length, and cannot start with http:// or https://.
        -   The keys of labels that are added to the same resource must be unique. If you add a label with a used key, the label overwrites the one that uses the same key.
        -   You can add up to 20 labels to each resource. If you add more than 20 labels to a resource, all labels become invalid. You must remove unused labels for the other labels to take effect. |
        |**Custom Image**|You can select a custom image to replace the default image. |
        |**Cluster Domain**|Set the domain name of the cluster. **Note:** The default domain name is **cluster.local**. You can enter a custom domain name. A domain name consists of two parts. Each part must not exceed 63 characters in length, and can contain only letters and digits. You cannot leave these parts empty. |
        |**Custom Certificate SANs**|You can specify custom subject alternative names \(SANs\) for the API server certificate. Separate multiple IP addresses or domain names with commas \(,\). |
        |**Service Account Token Volume Projection**|Enable **Service Account Token Volume Projection** to enhance security when you use service accounts. For more information, see [Enable service account token volume projection](/intl.en-US/User Guide for Kubernetes Clusters/Security management/Infrastructure security/Enable service account token volume projection.md). |
        |**Cluster CA**|If you select Custom Cluster CA, upload a Certificate Authority \(CA\) certificate for the cluster to ensure secure data transmission between the server and client.|
        |**Deletion Protection**|Specify whether to enable deletion protection. If you select this check box, the cluster cannot be deleted in the console or by calling API operations. This protects the cluster from being accidentally deleted.|

8.  Click **Next:Master Configurations** to configure master nodes.

    |Parameter|Description|
    |---------|-----------|
    |**Billing Method**|**Pay-As-You-Go** and **Subscription** are supported.|
    |**Duration**|If you select **Subscription**, you must specify the subscription duration.|
    |**Auto Renewal**|If you select **Subscription**, you must specify whether to enable **Auto Renewal**.|
    |**Master Node Quantity**|Specify the number of master nodes. You can create three or five master nodes.|
    |**Instance Type**|Select an instance type for the master nodes. For more information, see [Instance families](/intl.en-US/Instance/Instance families.md).**Note:** If no instance type is available, you can change vSwitches on the **Cluster Configurations** wizard page. |
    |**System Disk**|By default, system disks are mounted to master nodes. **ESSD Disk**, **SSD Disk**, and **Ultra Disk** are supported. **Note:**

    -   You can select **Enable Backup** to back up disk data.
    -   If you select an **enhanced SSD** as the system disk, you can set a **performance level** for the disk.

You can select higher performance levels for enhanced SSDs with larger storage capacities. For example, you can select performance level 2 for an enhanced SSD with a storage capacity of more than 460 GiB. You can select performance level 3 for an enhanced SSD with a storage capacity of more than 1,260 GiB. For more information, see [Capacity and PLs of ESSDs](/intl.en-US/Block Storage/Block Storage overview/Enhanced SSDs.md). |

9.  Click **Next:Worker Configurations** to configure worker nodes.

    1.  Set worker instances.

        -   If you select **Create Instance**, you must set the parameters that are listed in the following table.

            |Parameter|Description|
            |---------|-----------|
            |**Instance Type**|Choose **Heterogeneous Computing GPU/FPGA/NPU** \> **Compute Optimized Type with GPU**. In the list of available instance types, select one or more required instance types. For more information, see [Instance families](/intl.en-US/Instance/Instance families.md). **Note:** If no instance type is available, you can change vSwitches on the **Cluster Configurations** wizard page. |
            |**Selected Types**|The selected instance types are displayed.|
            |**Quantity**|Specify the number of worker nodes.|
            |**System Disk**|**Enhanced SSDs**, **SSDs**, and **ultra disks** are supported.

**Note:**

            -   You can select **Enable Backup** to back up disk data.
            -   If you select an **enhanced SSD** as the system disk, you can also set a custom **performance level** for the disk.

You can select higher performance levels for enhanced SSDs with larger storage capacities. For example, you can select performance level 2 for an enhanced SSD with a storage capacity of more than 460 GiB. You can select performance level 3 for an enhanced SSD with a storage capacity of more than 1,260 GiB. For more information, see [Capacity and PLs of ESSDs](/intl.en-US/Block Storage/Block Storage overview/Enhanced SSDs.md). |
            |**Mount Data Disk**|**ESSD Disk**, **SSD Disk**, and **Ultra Disk** are supported. You can select **Encrypt Disk** and **Enable Backup** when you mount data disks. |
            |**Operating System**|ACK supports the following node operating systems:

            -   Alibaba Cloud Linux 2. This is the default operating system.
            -   CentOS 7.x

**Note:** CentOS 8.x and later are not supported. |
            |Logon Type|            -   Key pair logon.
                -   **Key Pair**: Select an SSH key pair from the drop-down list.
                -   **create a key pair**: Create an SSH key pair if none is available. For more information about how to create an SSH key pair, see [Create an SSH key pair](/intl.en-US/Security/Key pairs/Use an SSH key pair/Create an SSH key pair.md). After the key pair is created, set it as the credential that is used to log on to the cluster.
            -   Password logon.
                -   **Password**: Enter the password that is used to log on to the nodes.
                -   **Confirm Password**: Enter the password again. |

        -   If you select **Add Existing Instance**, make sure that you have created ECS instances in the region where the cluster is deployed. Then, set **Operating System**, **Logon Type**, and **Key Pair** based on the preceding settings.
    2.  Configure advanced settings.

        |Parameter|Description|
        |---------|-----------|
        |**Node Protection**|Specify whether to enable node protection. **Note:** By default, this check box is selected. The nodes in the cluster cannot be deleted in the console or by calling the API. This protects the nodes from being accidentally deleted. |
        |**User Data**|For more information, see [Prepare user data](/intl.en-US/Instance/Manage instances/User data/Prepare user data.md).|
        |**Custom Node Name**|Specify whether to enable **Custom Node Name**. A node name consists of a prefix, an IP substring, and a suffix.

        -   Both the prefix and suffix can contain one or more parts that are separated with periods \(**.**\). These parts can contain lowercase letters, digits, and hyphens \(**-**\), and must start and end with a digit or lowercase letter.
        -   The IP substring length specifies the number of digits to be truncated from the end of the node IP address. Valid values: 5 to 12.
For example, if the node IP address is 192.168.0.55, the prefix is aliyun.com, the IP substring length is 5, and the suffix is test, the node name is aliyun.com00055test. |
        |**Node Port Range**|Set the node port range. The default port range is 30000 to 32767.|
        |**CPU Policy**|Set the CPU policy.         -   None: indicates that the default CPU affinity is used. This is the default policy.
        -   Static: allows pods with specific resource characteristics on the node to be granted with enhanced CPU affinity and exclusivity. |
        |**Taints**|Add taints to all worker nodes in the cluster.|

10. Click **Next:Component Configuration** to configure components.

    |Parameter|Description|
    |---------|-----------|
    |**Ingress**|Specify whether to install Ingress controllers. By default, **Install Ingress Controllers** is selected. For more information, see [Configure an Ingress](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Configure an Ingress.md).|
    |**Volume Plug-in**|Select a volume plug-in. FlexVolume and CSI are supported. ACK clusters can be automatically bound to Alibaba Cloud disks, Apsara File Storage NAS \(NAS\) file systems, and Object Storage Service \(OSS\) buckets that are mounted to pods. For more information, see [Storage management-Flexvolume](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/Overview.md) and [Storage management-CSI](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/Overview.md).|
    |**Monitoring Agents**|Specify whether to install the Cloud Monitor agent. You can select **Install CloudMonitor Agent on ECS Instance**. After the Cloud Monitor agent is installed, you can view monitoring information about ECS instances in the Cloud Monitor console. |
    |**Log Service**|Specify whether to enable Log Service. You can select an existing project or create a project.

If you select **Enable Log Service**, the Log Service plug-in is automatically installed in the cluster. When you deploy an application, you can activate Log Service through a few steps. For more information, see [Use Log Service to collect container logs](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Log management/Use Log Service to collect container logs.md).

After you select **Enable Log Service**, you can specify whether to select **Create Ingress Dashboard** and **Install node-problem-detector and Create Event Center**. |
    |**Workflow Engine**|Specify whether to enable Alibaba Cloud Genomics Compute Service \(AGS\).     -   If you select this check box, the system automatically installs the AGS workflow plug-in when the system creates the cluster.
    -   If you clear this check box, you must manually install the AGS workflow plug-in. For more information, see [Introduction to AGS CLI](/intl.en-US/User Guide for Genomics Service/AGS workflow/Introduction to AGS CLI.md). |

11. Click **Next:Confirm Order**.

12. Select **Terms of Service** and click **Create Cluster**.

    **Note:** It takes approximately 10 minutes for the system to create a managed Kubernetes cluster that contains multiple nodes.


## What to do next

After the cluster is created, go to the Clusters page, find the created cluster, and then click the cluster name or click **Details** in the **Actions** column. In the left-side navigation pane of the details page, click **Nodes** and go to the Nodes page. Select the worker node that is configured when you create the cluster, and choose **More** \> **Details** in the **Actions** column. Then, you can view the GPU devices that are associated with the node.

