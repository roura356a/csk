---
keyword: create a managed Kubernetes cluster with GPU-accelerated nodes
---

# Create a managed Kubernetes cluster with GPU-accelerated nodes

This topic describes how to create a managed Kubernetes cluster for heterogeneous computing in the Container Service for Kubernetes \(ACK\) console.

ACK performs the following operations when a cluster is created:

-   Creates ECS instances, configures a public key to enable Secure Shell \(SSH\) logon from master nodes to other nodes, and then configures the ACK cluster through CloudInit.
-   Creates a security group that allows access to the VPC over Internet Control Message Protocol \(ICMP\).
-   If you do not specify an existing VPC, ACK creates a VPC and vSwitch and creates SNAT entries for the vSwitch.
-   Adds route entries to the VPC.
-   Creates a NAT gateway and EIPs.
-   Creates a Resource Access Management \(RAM\) user and an AccessKey pair. Grants the following permissions to the RAM user: permissions to query, create, and delete ECS instances, permissions to add and delete disks, and full permissions on SLB, CloudMonitor, VPC, Log Service, and Apsara File Storage NAS. The ACK cluster automatically creates SLB instances, disks, and VPC route entries based on your configuration.
-   Creates an internal-facing SLB instance and opens port 6443.
-   Creates an Internet-facing SLB instance and opens ports 6443, 8443, and 22. If you enable SSH logon when you create the cluster, port 22 is opened. Otherwise, port 22 is not exposed.

## Limits

-   SLB instances that are created along with an ACK cluster support only the pay-as-you-go billing method.
-   ACK clusters support only VPCs.
-   By default, each account has specific quotas on cloud resources that can be created. You cannot create clusters if the quota is reached. Make sure that you have sufficient quotas before you create a cluster.

    To request a quota increase, submit a ticket.

    -   By default, you can create up to 50 clusters across all regions with each account. Each cluster can contain up to 100 nodes. To increase the quota of clusters or nodes,[submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).

        **Note:** By default, you can add up to 48 route entries to a VPC. This means that you can deploy up to 48 nodes in an ACK cluster that uses Flannel. An ACK cluster that uses Terway is not subject to this limit. To deploy more nodes in this case,[submit a ticket](https://workorder-intl.console.aliyun.com/console.htm) to apply for an increase on the quota of route entries in the VPC that you want to use.

    -   By default, you can create up to 100 security groups with each account.
    -   By default, you can create up to 60 pay-as-you-go SLB instances with each account.
    -   By default, you can create up to 20 EIPs with each account.
-   Limits on ECS instances:

    The pay-as-you-go and subscription billing methods are supported.

    **Note:** After an ECS instance is created, you can change its billing method from pay-as-you-go to subscription in the ECS console. For more information, see [Change the billing method of an instance from pay-as-you-go to subscription](/intl.en-US/Pricing/Change the billing method/Change the billing method of an instance from pay-as-you-go to subscription.md).


## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  In the upper-right corner of the Clusters page, click **Cluster Template**.

4.  In the Select Cluster Template dialog box, find **Heterogeneous Computing Cluster** in the **Managed Clusters** section and click **Create**.

5.  On the **Managed Kubernetes** tab, configure the cluster.

    1.  Configure basic settings of the cluster.

        |Parameter|Description|
        |---------|-----------|
        |**Cluster Name**|Enter a name for the cluster. **Note:** The name must be 1 to 63 characters in length, and can contain digits, letters, and hyphens \(-\). |
        |**Cluster Specification**|Select the required edition. By default, the Standard edition is selected.|
        |**Region**|Select a region to deploy the cluster.|
        |**Resource Group**|Move the pointer over **All Resources** at the top of the page and select the resource group to which the cluster belongs. The name of the selected resource group is displayed in the Resource Group field. ![Resource Group](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9688404061/p127165.png) |
        |**Kubernetes Version**|Select a Kubernetes version. The following versions are supported: 1.18.8-aliyun.1 and 1.16.9-aliyun.1.|
        |**Container Runtime**|The **containerd**, **Docker**, and **Sandboxed-Container** runtimes are supported. For more information, see [How do I choose between Docker and Sandboxed-Container?](/intl.en-US/User Guide for Kubernetes Clusters/Sandboxed-Container management/Comparison of Docker, containerd, and Sandboxed-Container.md). |
        |**VPC**|Select a virtual private cloud \(VPC\) to deploy the cluster. Standard VPCs and shared VPCs are supported.         -   Shared VPC: The owner of a VPC \(resource owner\) can share the vSwitches in the VPC with other accounts in the same organization.
        -   Standard VPC: The owner of a VPC \(resource owner\) cannot share the vSwitches in the VPC with other accounts.
**Note:** ACK clusters support only VPCs. You can select a VPC from the drop-down list. If no VPC is available, click **Create VPC** to create one. For more information, see [Create a VPC](/intl.en-US/VPCs and vSwitchs/Work with VPCs.md). |
        |**VSwitch**|Set the vSwitch. You can select up to three vSwitches that are deployed in different **zones**. If no vSwitch is available, click **Create VSwitch** to create one. For more information, see [Create a vSwitch](/intl.en-US/VPCs and vSwitchs/Work with vSwitches.md). |
        |**Network Plug-in**|Select and configure the network plug-in. Flannel and Terway are available. For more information, see [t64408.md\#section\_k1z\_f1n\_lmh](/intl.en-US/User Guide for Kubernetes Clusters/Network/Container network/Use the Terway plug-in.md).         -   Flannel: an open source Container Network Interface \(CNI\) plug-in, which is simple and stable. Flannel provides a few simple features. However, it does not support standard Kubernetes network policies.
        -   Terway: a network plug-in developed by Alibaba Cloud for ACK. Terway allows you to assign Alibaba Cloud elastic network interfaces \(ENIs\) to containers and use standard Kubernetes network policies to regulate how containers communicate with each other. Terway also supports bandwidth throttling on individual containers.

**Note:**

            -   The number of pods that can be deployed on a node depends on the number of ENIs that are attached to the node and the maximum number of secondary IP addresses provided by these ENIs.
            -   If you select a shared VPC for the cluster, you must select the Terway plug-in. |
        |**IP Addresses per Node**|If you select **Flannel** as the network plug-in, you must set **IP Addresses per Node**.

**Note:**

        -   **IP Addresses per Node** specifies the maximum number of IP addresses that can be assigned to each node. We recommend that you use the default value.
        -   After you select the VPC and specify the number of IP addresses per node, recommended values are automatically generated for Pod CIDR block and Service CIDR block. The system also provides the maximum number of nodes that can be deployed in the cluster and the maximum number of pods that can be deployed on each node. You can modify the values based on your business requirements. |
        |**Pod CIDR Block**|If you select **Flannel**, you must set the **Pod CIDR Block** parameter. The CIDR block specified by **Pod CIDR Block** cannot overlap with that of the VPC or the CIDR blocks of existing ACK clusters in the VPC. After you create the cluster, you cannot modify the pod CIDR block. In addition, the Service CIDR block cannot overlap with the pod CIDR block. For more information about subnetting for ACK clusters, see [Plan CIDR blocks for an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Network/Plan CIDR blocks for an ACK cluster.md). |
        |**Terway Mode**|If you set Network Plug-in to **Terway**, the **Terway Mode** parameter is available. When you set **Terway Mode**, you can select or clear **Assign One ENI to Each Pod**.

        -   If you select **Assign One ENI to Each Pod**, an ENI is assigned to each pod.
        -   If you clear **Assign One ENI to Each Pod**, an ENI is shared among multiple pods. A secondary IP address of the ENI is assigned to each pod.
**Note:** To use this feature, [submit a ticket](https://selfservice.console.aliyun.com/ticket/scene/ecs/%E4%BA%91%E6%9C%8D%E5%8A%A1%E5%99%A8%20ECS/detail). |
        |**Service CIDR**|Set the **Service CIDR** parameter. The CIDR block specified by **Service CIDR** cannot overlap with that of the VPC or the CIDR blocks of existing ACK clusters in the VPC. After you create the cluster, you cannot modify the Service CIDR block. In addition, the Service CIDR block cannot overlap with the pod CIDR block. For more information about subnetting for ACK clusters, see [Plan CIDR blocks for an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Network/Plan CIDR blocks for an ACK cluster.md).|
        |**Configure SNAT**|Specify whether to configure Source Network Address Translation \(SNAT\) rules for the VPC.         -   If the specified VPC has a Network Address Translation \(NAT\) gateway, ACK uses this NAT gateway.
        -   Otherwise, the system automatically creates a NAT gateway. If you do not want the system to create a NAT gateway, clear **Configure SNAT for VPC**. In this case, you must manually create a NAT gateway and configure SNAT rules to enable Internet access for the VPC. Otherwise, ACK clusters in the VPC cannot access the Internet and the cluster cannot be created. |
        |**Access to API Server**|By default, an internal-facing Server Load Balancer \(SLB\) instance is created for the cluster API server. You can modify the specification of the SLB instance. For more information, see [Instance types and specifications](/intl.en-US/Classic Load Balancer/User Guide/Instance/SLB instance overview.md).

**Note:** If you delete the SLB instance, you cannot access the cluster API server.

Select or clear **Expose API Server with EIP**. The ACK API server provides multiple HTTP-based RESTful APIs, which can be used to create, delete, modify, query, and monitor resources, such as pods and Services.

        -   If you select this check box, an elastic IP address \(EIP\) is created and associated with an Internet-facing SLB instance. Port 6443 used by the API server is opened on master nodes. You can connect to and manage the ACK cluster by using kubeconfig over the Internet.
        -   If you clear this check box, no EIP is created. You can connect to and manage the ACK cluster by using kubeconfig only within the VPC. |
        |**RDS Whitelist**|Set the Relational Database Service \(RDS\) whitelist. Add the IP addresses of the nodes in the ACK cluster to the RDS whitelist.|
        |**Security Group**|You can select **Create Basic Security Group**, **Create Advanced Security Group**, or **Select Existing Security Group**. For more information, see [Overview](/intl.en-US/Security/Security groups/Overview.md). |
        |**Deletion Protection**|Specify whether to enable deletion protection. Deletion protection prevents the cluster from being accidentally deleted in the console or by calling the API. This prevents user errors. |

    2.  Configure advanced settings.

        |Parameter|Description|
        |---------|-----------|
        |**Time Zone**|Select a time zone for the ACK cluster. By default, the time zone configured for your browser is selected. |
        |**Kube-proxy Mode**|iptables and IPVS are supported.

        -   iptables is a mature and stable kube-proxy mode. It uses iptables rules to conduct service discovery and load balancing. The performance of this mode is restricted by the size of the ACK cluster. This mode is suitable for ACK clusters that manage a small number of Services.
        -   IPVS is a high-performance kube-proxy mode. It uses Linux Virtual Server \(LVS\) to conduct service discovery and load balancing. This mode is suitable for ACK clusters that manage a large number of Services. We recommend that you use this mode in scenarios where high-performance load balancing is required. |
        |**Labels**|Add labels to the cluster. Enter a key and a value, and click **Add**.

**Note:**

        -   Key is required. Value is optional.
        -   Keys are not case-sensitive. A key must be 1 to 64 characters in length, and cannot start with aliyun, http://, or https://.
        -   Values are not case-sensitive. A value can be empty and can contain up to 128 characters in length. It cannot be http:// or https://.
        -   The keys of labels that are added to the same resource must be unique. If you add a label with a used key, the label overwrites the others that use the same key.
        -   You can add up to 20 labels to each resource. If you add more than 20 labels to a resource, all labels become invalid. You must remove unused labels for the other labels to take effect. |
        |**Cluster Domain**|Set the domain name of the cluster.

**Note:** The default domain name is **cluster.local**. You can enter a custom domain name. A domain name consists of two parts. Each part must be 1 to 63 characters in length and can contain only letters and digits. You cannot leave these parts empty. |
        |**Custom Certificate SANs**|You can enter custom subject alternative names \(SANs\) for the API server certificate of the cluster to accept requests from specified IP addresses or domain names.

For more information, see [Customize the SAN of the API server certificate for a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Security management/Infrastructure security/Customize the SAN of the API server certificate for a managed Kubernetes cluster.md). |
        |**Service Account Token Volume Projection**|**Service account token volume projection** reduces security risks when pods use service accounts to access the API server. This feature enables kubelet to request and store the token on behalf of the pod. This feature also allows you to configure token properties, such as the audience and validity duration. For more information, see [Enable service account token volume projection](/intl.en-US/User Guide for Kubernetes Clusters/Security management/Infrastructure security/Enable service account token volume projection.md). |
        |**Secret Encryption**|If you select **Select Key**, you can use a key that is created in the Key Management Service \(KMS\) console to encrypt Kubernetes Secrets. For more information, see [Use KMS to encrypt Kubernetes Secrets](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/Use KMS to encrypt Kubernetes Secrets.md).|

6.  Click **Next:Worker Configurations** to configure worker nodes.

    1.  Set worker nodes.

        -   If you select **Create Instance**, you must set the parameters that are listed in the following table.

            |Parameter|Description|
            |---------|-----------|
            |**Billing Method**|These billing methods are supported: **Pay-As-You-Go** and **Subscription**.|
            |**Duration**|If you select **Subscription**, you must specify the subscription duration. You can select 1 Month, 2 Months, 3 Months, or 6 Months. If you require a longer duration, you can select 1 Year, 2 Years, 3 Years, 4 Years, or 5 Years.|
            |**Auto Renewal**|If you select **Subscription**, you must specify whether to enable **auto-renewal**.|
            |**Instance Type**|Click **Heterogeneous Computing** and then click **Compute Optimized Type with GPU**. In the list of available instance types, select the instance types that you want to use. For more information, see [Instance families](/intl.en-US/Instance/Instance families.md). **Note:** If no instance type is available, you can change vSwitches on the **Cluster Configurations** wizard page.

![Heterogeneous Computing](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7232869161/p127318.png) |
            |**Selected Types**|The selected instance types are displayed. |
            |**Quantity**|Specify the number of worker nodes that you want to create. |
            |**System Disk**|**Enhanced SSDs**, **standard SSDs**, and **ultra disks** are supported.

**Note:**

            -   You can select **Enable Backup** to back up disk data.
            -   If you select an **enhanced SSD** as the system disk, you can set a custom **performance level** for the disk.

You can select higher performance levels for enhanced SSDs with larger storage capacities. For example, you can select performance level 2 for an enhanced SSD with a storage capacity of more than 460 GiB. You can select performance level 3 for an enhanced SSD with a storage capacity of more than 1,260 GiB. For more information, see [Capacity and PLs](/intl.en-US/Block Storage/Block Storage overview/ESSDs.md). |
            |**Mount Data Disk**|**ESSD Disk**, **SSD Disk**, and **Ultra Disk** are supported. You can select **Encrypt Disk** and **Enable Backup** when you mount data disks. |
            |**Operating System**|ACK supports the following node operating systems:

            -   Alibaba Cloud Linux 2. This is the default operating system.
            -   CentOS 7.x

**Note:** CentOS 8.x and later are not supported. |
            |**Logon Type**|            -   Set the key pair.

When you create the cluster, select a key pair that is used to log on to the cluster. If no key pair is available, click **create a key pair** to create one in the Elastic Compute Service \(ECS\) console. For more information, see [Create an SSH key pair](/intl.en-US/Security/Key pairs/Use an SSH key pair/Create an SSH key pair.md). After the key pair is created, set it as the credential to log on to the cluster.

            -   Set the password.
                -   **Password**: Enter the password that is used to log on to the nodes.
                -   **Confirm Password**: Enter the password again. |
            |**Key Pair**|

        -   If you select **Add Existing Instance**, make sure that you have created ECS instances in the region where the cluster is deployed. Then, set **Operating System**, **Logon Type**, and **Key Pair** based on the preceding settings.
    2.  Configure advanced settings of the worker nodes.

        |Parameter|Description|
        |---------|-----------|
        |**Node Protection**|Specify whether to enable node protection.

**Note:** By default, this check box is selected. Node protection prevents nodes from being accidentally deleted in the console or by calling the API. This prevents user errors. |
        |**User Data**|For more information, see [Overview of ECS instance user data](/intl.en-US/Instance/Manage instance configurations/Manage instance user data/Overview of ECS instance user data.md). |
        |**Custom Image**|You can select a custom image for your nodes. After you select a custom image, all nodes in the cluster are deployed by using this image. For more information about how to create a custom image, see [Create a Kubernetes cluster by using a custom image](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a Kubernetes cluster by using a custom image.md).

**Note:**

        -   Only custom images based on CentOS 7.x and Alibaba Cloud Linux 2.x are supported.
        -   To use this feature, [submit a ticket](https://selfservice.console.aliyun.com/ticket/scene/ecs/%E4%BA%91%E6%9C%8D%E5%8A%A1%E5%99%A8%20ECS/detail) to apply for this feature to be enabled on your account. |
        |**Custom Node Name**|Specify whether to use a **custom node name**.

A node name consists of a prefix, an IP substring, and a suffix.

        -   Both the prefix and suffix can contain one or more parts that are separated with periods \(**.**\).These parts can contain lowercase letters, digits, and hyphens \(-\), and must start and end with a lowercase letter or digit.
        -   The IP substring length specifies the number of digits to be truncated from the end of the returned node IP address. Valid values: 5 to 12.
For example, if the node IP address is 192.1xx.x.xx, the prefix is aliyun.com, the IP substring length is 5, and the suffix is test, the node name will be aliyun.com00055test. |
        |**CPU Policy**|Set the CPU policy.

        -   none: This policy indicates that the default CPU affinity is used. This is the default policy.
        -   static: This policy allows pods with specific resource characteristics on the node to be granted with enhanced CPU affinity and exclusivity. |
        |**Taints**|Add taints to the worker nodes in the ACK cluster. |

7.  Click **Next:Component Configurations** to configure components.

    |Parameter|Description|
    |---------|-----------|
    |**Ingress**|Specify whether to install Ingress controllers. By default, **Install Ingress Controllers** is selected. For more information, see [Configure an Ingress](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Configure an Ingress.md). **Note:** If you want to select **Create Ingress Dashboard**, you must first enable Log Service. |
    |**Volume Plug-in**|Select a volume plug-in. You can select Flexvolume or CSI. ACK clusters can be automatically bound to Alibaba Cloud disks, Apsara File Storage NAS \(NAS\) file systems, and Object Storage Service \(OSS\) buckets that are mounted to pods. For more information, see [Storage management-FlexVolume](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/Overview.md) and [Storage management-CSI](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/Overview.md).|
    |**Monitoring Agents**|Specify whether to install the CloudMonitor agent. By default, **Install CloudMonitor Agent on ECS Instance** and **Enable Prometheus Monitoring** are selected. After the CloudMonitor agent is installed on ECS instance-based nodes, you can view monitoring data about the nodes in the CloudMonitor console. |
    |**Alerts**|Select **Use Default Alert Template** to enable the alerting feature and use the default alert rules. For more information, see [Alert management](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Alert management.md). |
    |**Log Service**|Specify whether to enable Log Service. You can select an existing Log Service project or create one. By default, **Enable Log Service** is selected. When you create an application, you can enable Log Service through a few steps. For more information, see [Collect log files from containers by using Log Service](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Log management/Collect log files from containers by using Log Service.md).

After you select **Enable Log Service**, you can specify whether to select **Create Ingress Dashboard** and **Install node-problem-detector and Create Event Center**. |
    |**Log Collection for Control Plane Components**|If you select **Enable**, log of the control plane components is collected to the specified Log Service project that belongs to the current account. For more information, see [Collect the logs of control plane components in a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Log management/Collect the logs of control plane components in a managed Kubernetes cluster.md). |
    |**Workflow Engine**|Specify whether to enable Alibaba Cloud Genomics Service \(AGS\).

**Note:** To use this feature, submit a ticket.

    -   If you select this check box, the system automatically installs the AGS workflow plug-in when the system creates the cluster.
    -   If you clear this check box, you must manually install the AGS workflow plug-in. For more information, see [Introduction to AGS CLI](/intl.en-US/User Guide for Genomics Service/AGS workflow/Introduction to AGS CLI.md). |

8.  Click **Next:Confirm Order**.

9.  Select **Terms of Service** and click **Create Cluster**.

    **Note:** It requires approximately 10 minutes for the system to create a managed Kubernetes cluster that contains multiple nodes.


## What to do next

After the cluster is created, go to the Clusters page, find the created cluster, and then click the cluster name or click **Details** in the **Actions** column. In the left-side navigation pane of the details page, choose **Nodes** \> **Nodes**. On the page that appears, select the worker node that is configured when you create the cluster, and choose **More** \> **Details** in the **Actions** column. Then, you can view the GPU devices that are associated with the node.

