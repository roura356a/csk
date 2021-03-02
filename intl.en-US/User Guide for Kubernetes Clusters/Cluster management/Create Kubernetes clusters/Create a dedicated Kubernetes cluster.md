---
keyword: [Kubernetes, create a Kubernetes cluster, dedicated Kubernetes clusters]
---

# Create a dedicated Kubernetes cluster

A dedicated Kubernetes cluster contains at least three master nodes. This ensures high availability and provides fine-grained management on clusters. You must manually size, maintain, and upgrade dedicated Kubernetes clusters. This topic describes how to create a dedicated Kubernetes cluster in the Container Service for Kubernetes \(ACK\) console.

Resource Access Management \(RAM\) is activated in the [RAM console](https://ram.console.aliyun.com/). Auto Scaling \(ESS\) is activated in the [ESS console](https://essnew.console.aliyun.com).

**Note:**

To use an ACK cluster, take note of the following limits:

-   Server Load Balancer \(SLB\) instances that are created along with an ACK cluster support only the pay-as-you-go billing method.
-   ACK clusters support only virtual private clouds \(VPCs\).
-   By default, you can create only a limited amount of cloud resources with each Alibaba Cloud account. You cannot create clusters if the quotas are exhausted. To request a quota increase, submit a ticket.
    -   By default, you can create up to 50 clusters across all regions with each account. Each cluster can contain up to 100 nodes. To increase the quota of clusters or nodes,[submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).

        **Note:** By default, you can add up to 48 route entries to the VPC where an ACK cluster is deployed. This means that you can configure up to 48 route entries for ACK clusters deployed in a VPC. To increase the quota of route entries for a VPC, submit a ticket.

    -   By default, you can create up to 100 security groups with each account.
    -   By default, you can create up to 60 pay-as-you-go SLB instances with each account.
    -   By default, you can create up to 20 elastic IP addresses \(EIPs\) with each account.
-   Limits on Elastic Compute Service \(ECS\) instances:

    The pay-as-you-go and subscription billing methods are supported.


1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  In the upper-right corner of the Clusters page, click **Create Kubernetes Cluster**.

4.  Click the **Dedicated Kubernetes** tab and configure the cluster.

    1.  Configure basic settings of the cluster.

        |Parameter|Description|
        |---------|-----------|
        |**Cluster Name**|Enter a name for the ACK cluster.

**Note:** The name must be 1 to 63 characters in length. It can contain digits, letters, and hyphens \(-\). |
        |**Region**|Select the region where you want to create the cluster. |
        |**Time Zone**|Select a time zone for the ACK cluster. By default, the time zone configured for your browser is selected. |
        |**Resource Group**|Move the pointer over **All Resources** at the top of the page and select the resource group to which the cluster belongs. The name of the selected resource group is displayed.

![Resource Group](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9688404061/p127165.png) |
        |**Kubernetes Version**|Select a Kubernetes version. |
        |**Container Runtime**|By default, the Docker runtime is selected. Use the default setting.|
        |**VPC**|Select a virtual private cloud \(VPC\) to deploy the cluster. Shared VPCs and standard VPCs are supported.

        -   Shared VPC: The owner of a VPC \(resource owner\) can share vSwitches in the VPC under the account of the owner with other accounts in the same organization.
        -   Standard VPC: The owner of a VPC \(resource owner\) cannot share vSwitches in the VPC under the account of the owner with other accounts.
**Note:** ACK clusters support only VPCs. You can select a VPC from the drop-down list. If no VPC is available, click **Create VPC** to create one. For more information, see [Create a VPC](/intl.en-US/VPCs and vSwitchs/Create a VPC.md). |
        |**VSwitch**|Select vSwitches.

You can select up to three vSwitches that are deployed in different **zones**. If no vSwitch is available, click **Create VSwitch** to create one. For more information, see [Create a vSwitch](/intl.en-US/VPCs and vSwitchs/Create a VSwitch.md). |
        |**Network Plug-in**|Select a network plug-in. Both Flannel and Terway are supported. For more information, see [t64408.md\#section\_k1z\_f1n\_lmh](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Container network/Work with Terway.md).

        -   Flannel: a simple and stable Container Network Interface \(CNI\) plug-in that is developed by the Kubernetes community. Flannel provides a few simple features. However, it does not support standard Kubernetes network policies.
        -   Terway: a network plug-in that is developed by ACK. Terway allows you to assign elastic network interfaces \(ENIs\) of Alibaba Cloud to containers. It also allows you to customize Kubernetes network policies to control intercommunication among containers and implement bandwidth throttling on individual containers.

**Note:**

            -   The number of pods that can be deployed on a node depends on the number of ENIs that are attached to the node and the maximum number of secondary IP addresses that are provided by these ENIs.
            -   If you select a shared VPC for an ACK cluster, you must select the Terway network plug-in.
            -   If you select **Terway**, an ENI is shared among multiple pods. A secondary IP address of the ENI is assigned to each pod. |
        |**Pod CIDR Block**|If you set Network Plug-in to **Flannel**, you must set **Pod CIDR Block**.

The CIDR block specified by **Pod CIDR Block** cannot overlap with that of the VPC or the CIDR blocks of existing ACK clusters in the VPC. After you create the cluster, you cannot modify the pod CIDR block. The Service CIDR block cannot overlap with the pod CIDR block. For more information about subnetting for ACK clusters, see [Plan CIDR blocks for an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Plan CIDR blocks for an ACK cluster.md). |
        |**Terway Mode**|If you select **Terway** for **Network Plug-in**, you must set **Terway Mode**.

        -   Select or clear **Assign One ENI to Each Pod**.

-   If you select the check box, an ENI is assigned to each pod.
-   If you clear the check box, an ENI is shared among multiple pods. A secondary IP address that is provided by the ENI is assigned to each pod.
        -   Select or clear **IPVLAN**.

-   This option is available only when you clear Assign One ENI to Each Pod.
-   If you select IPVLAN, IPVLAN and extended Berkeley Packet Filter \(eBPF\) are used for network virtualization when an ENI is shared among multiple pods. This improves network performance. Only the Alibaba Cloud Linux 2 operating system is supported.
-   If you clear IPVLAN, policy-based routes are used for network virtualization when an ENI is shared among multiple pods. The CentOS 7 and Alibaba Cloud Linux 2 operating systems are supported. This is the default setting.
**Note:** Before you select **Assign One ENI to Each Pod**,you must [submit a ticket](https://workorder-intl.console.aliyun.com/console.htm) to apply for this feature to be enabled on your account.

        -   Select or clear **Support for NetworkPolicy**.

-   The NetworkPolicy feature is available only when you clear Assign One ENI to Each Pod. By default, Assign One ENI to Each Pod is unselected.
-   If you select Support for NetworkPolicy, you can use Kubernetes network policies to control communication among pods.
-   If you clear Support for NetworkPolicy, you cannot use Kubernetes network policies to control communication among pods. This prevents Kubernetes network policies from posing heavy loads on the Kubernetes API server. |
        |**Service CIDR**|Set **Service CIDR**. The CIDR block specified by **Service CIDR** cannot overlap with that of the VPC or the CIDR blocks of existing ACK clusters in the VPC. After you create the cluster, you cannot modify the Service CIDR block. In addition, the Service CIDR block cannot overlap with the pod CIDR block. For more information about subnetting for ACK clusters, see [Plan CIDR blocks for an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Plan CIDR blocks for an ACK cluster.md). |
        |**IP Addresses per Node**|If you set Network Plug-in to **Flannel**, you must set **IP Addresses per Node**.

**Note:** **IP Addresses per Node** specifies the maximum number of IP addresses that can be assigned to each node. We recommend that you use the default value. |
        |**Configure SNAT**|By default, an ACK cluster cannot be accessed over the Internet. If the VPC that you select for the ACK cluster cannot access the Internet, you can select **Configure SNAT for VPC**. Then, ACK creates a Network Address Translation \(NAT\) gateway and configures Source Network Address Translation \(SNAT\) entries to enable Internet access for the VPC. |
        |**Access to API Server**|By default, an internal-facing Server Load Balancer \(SLB\) instance is created for the API server. You can modify the specifications of the SLB instance. For more information, see [Specification](/intl.en-US/Classic Load Balancer/User Guide/Instance/SLB instance overview.md).

**Note:** If you delete the SLB instance, you cannot access the API server.

Select or clear **Expose API Server with EIP**. The ACK API server provides multiple HTTP-based RESTful APIs, which can be used to create, delete, modify, query, and monitor resources such as pods and Services.

        -   If you select this check box, an elastic IP address \(EIP\) is created and attached to an Internet-facing SLB instance. Port 6443 used by the API server is opened on master nodes. You can connect to and manage the ACK cluster by using kubeconfig over the Internet.
        -   If you clear this check box, no EIP is created. You can connect to and manage the ACK cluster only by using kubeconfig from within the VPC. |
        |**SSH Logon**|To enable Secure Shell \(SSH\) logon, you must first select **Expose API Server with EIP**.

        -   If you select this check box, you can access the cluster by using SSH.
        -   If you clear this check box, you cannot access the cluster by using SSH or kubectl. If you want to access an ECS instance in the cluster by using SSH, you must manually associate an EIP to the instance and configure security group rules to open SSH port 22. For more information, see [Use SSH to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use SSH to connect to an ACK cluster.md). |
        |**RDS Whitelist**|Set the Relational Database Service \(RDS\) whitelist. Add the IP addresses of nodes in the ACK cluster to the RDS whitelist.

**Note:** To enable an RDS instance to access the ACK cluster, you must deploy the RDS instance in the VPC where the ACK cluster is deployed. |
        |**Security Group**|You can select **Create Basic Security Group**, **Create Advanced Security Group**, or **Select Existing Security Group**. For more information, see [Overview](/intl.en-US/Security/Security groups/Overview.md). |

    2.  Configure advanced settings of the cluster.

        |Parameter|Description|
        |---------|-----------|
        |**Kube-proxy Mode**|IPVS and iptables are supported.

        -   iptables is a mature and stable kube-proxy mode. It uses iptables rules to conduct service discovery and load balancing. The performance of this mode is restricted by the size of the ACK cluster. This mode is suitable for ACK clusters that manage a small number of Services.
        -   IPVS is a high-performance kube-proxy mode. It uses Linux Virtual Server \(LVS\) to conduct service discovery and load balancing. This mode is suitable for ACK clusters that manage a large number of Services. We recommend that you use this mode in scenarios where high-performance load balancing is required. |
        |**Labels**|Add labels to cluster nodes. Enter a key and a value, and then click **Add**.

**Note:**

        -   Key is required. Value is optional.
        -   Keys are not case-sensitive. A key must be 1 to 64 characters in length, and cannot start with aliyun, http://, or https://.
        -   Values are not case-sensitive. A value must be 1 to 128 characters in length and cannot start with http:// or https://. This parameter can be empty.
        -   The keys of labels that are added to the same resource must be unique. If you add a label with a used key, the label overwrites the one that uses the same key.
        -   You can add up to 20 labels to each resource. If you add more than 20 labels to a resource, all labels become invalid. You must remove unused labels for the remaining labels to take effect. |
        |**Custom Image**|You can select a custom image for your nodes. After you select a custom image, all nodes in the cluster are deployed by using the image. For more information about how to create a custom image, see [Create a Kubernetes cluster by using a custom image](/intl.en-US/Best Practices/Cluster/Create a Kubernetes cluster by using a custom image.md).

**Note:**

        -   Only custom images based on CentOS 7.x and Alibaba Cloud Linux 2.x are supported.
        -   To use this feature, you must [submit a ticket](https://selfservice.console.aliyun.com/ticket/scene/ecs/%E4%BA%91%E6%9C%8D%E5%8A%A1%E5%99%A8%20ECS/detail) to apply for this feature to be enabled on your account. |
        |**Cluster Domain**|Set the domain name of the ACK cluster.

**Note:** The default domain name is **cluster.local**. You can enter a custom domain name. A domain name consists of two parts. Each part must be 1 to 63 characters in length and can contain only letters and digits. You cannot leave these parts empty. |
        |**Custom Certificate SANs**|You can enter custom subject alternative names \(SANs\) for the API server certificate of the cluster to accept requests from specified IP addresses or domain names. |
        |**Service Account Token Volume Projection**|**Service account token volume projection** reduces security risks when pods use service accounts to access the API server. This feature enables kubelet to request and store the token on behalf of the pod. This feature also allows you to configure token properties, such as the audience and validity duration. For more information, see [Deploy service account token volume projection](/intl.en-US/User Guide for Kubernetes Clusters/Security management/Use service account token volume projection.md). |
        |**Cluster CA**|If you select this check box, upload a Certificate Authority \(CA\) certificate for the ACK cluster to ensure secure data transmission between the server and client.|
        |**Deletion Protection**|Specify whether to enable deletion protection. If you select this check box, the ACK cluster cannot be deleted in the console or by calling the API. This prevents user errors. |

5.  Click **Next:Master Configurations** to configure master nodes.

    |Parameter|Description|
    |---------|-----------|
    |**Billing Method**|The **pay-as-you-go** and **subscription** billing methods are supported. If you select **pay-as-you-go**, you must set the following parameters:

    -   **Duration**: You can select 1, 2, 3, or 6 months. If you require a longer duration, you can select 1 to 5 years.
    -   **Auto Renewal**: Specify whether to enable auto renewal. |
    |**Master Node Quantity**|Select the number of master nodes. You can create three or five master nodes.|
    |**Instance Type**|Select an instance type for the master nodes. For more information, see [Instance families](/intl.en-US/Instance/Instance families.md).|
    |**System Disk**|By default, system disks are mounted to master nodes. Enhanced SSDs, SSDs, and ultra disks are supported.**Note:** You can select **Enable Backup** to back up disk data. |

6.  Click **Next:Worker Configurations** to configure worker nodes.

    1.  Set worker nodes.

        -   If you select **Create Instance**, you must set the parameters that are listed in the following table.

            |Parameter|Description|
            |---------|-----------|
            |**Instance Type**|You can select multiple instance types. For more information, see [Instance families](/intl.en-US/Instance/Instance families.md). |
            |**Selected Types**|The selected instance types are displayed. |
            |**Quantity**|Specify the number of worker nodes \(ECS instances\) to be created. |
            |**System Disk**|**Enhanced SSDs**, **SSDs**, and **ultra disks** are supported.

**Note:**

            -   You can select **Enable Backup** to back up disk data.
            -   If you select an **enhanced SSD** as the system disk, you can also set a custom **performance level** for the disk.

You can select higher performance levels for enhanced SSDs with larger storage capacities. For example, you can select performance level 2 for an enhanced SSD with a storage capacity of over 460 GiB. You can select performance level 3 for an enhanced SSD with a storage capacity of over 1,260 GiB. For more information, see [Capacity and PLs of ESSDs](/intl.en-US/Block Storage/Block Storage overview/Enhanced SSDs.md). |
            |**Mount Data Disk**|**Enhanced SSDs**, **SSDs**, and **ultra disks** are supported. You can enable disk **encryption** and **backup** when you mount data disks. |
            |**Operating System**|ACK supports the following node operating systems:

            -   Alibaba Cloud Linux 2. This is the default operating system.
            -   CentOS 7.x

**Note:** CentOS 8.x and later are not supported. |
            |**Logon Type**|            -   Key pair logon.
                -   **Key Pair**: Select an SSH key pair from the drop-down list.
                -   **create a key pair**: Create an SSH key pair if no SSH key pair is available. For more information about how to create an SSH key pair, see [Create an SSH key pair](/intl.en-US/Security/Key pairs/Use an SSH key pair/Create an SSH key pair.md). After the key pair is created, set it as the credential that is used to log on to the cluster.
            -   Set the password.
                -   **Password**: Enter the password that is used to log on to the nodes.
                -   **Confirm Password**: Enter the password again. |

        -   If you select **Add Existing Instance**, you must select ECS instances that are deployed in the region where the cluster is deployed. Then, set **Operating System**, **Logon Type**, and **Key Pair** based on the preceding settings.
    2.  Configure advanced settings of worker nodes.

        |Parameter|Description|
        |---------|-----------|
        |**Node Protection**|Specify whether to enable node protection.

**Note:** By default, this check box is selected. The nodes in the ACK cluster cannot be deleted in the console or by calling the API. This prevents user errors. |
        |**User Data**|For more information, see [Prepare user data](/intl.en-US/Instance/Manage instances/User data/Prepare user data.md). |
        |**Custom Node Name**|Specify whether to use a **custom node name**.

A node name consists of a prefix, an IP substring, and a suffix.

        -   Both the prefix and suffix can contain one or more parts that are separated with periods \(**.**\). These parts can contain lowercase letters, digits, and hyphens \(-\), and must start and end with a lowercase letter or digit.
        -   The IP substring length specifies the number of digits to be truncated from the end of the returned node IP address. Valid values: 5 to 12.
For example, if the node IP address is 192.1xx.x.xx, the prefix is aliyun.com, the IP substring length is 5, and the suffix is test, the node name will be aliyun.com00055test. |
        |**Node Port Range**|Set the node port range. The default port range is 30000 to 32767.|
        |**CPU Policy**|Set the CPU policy.

        -   none: This policy indicates that the default CPU affinity is used. This is the default policy.
        -   static: This policy allows pods with specific resource characteristics on the node to be granted with enhanced CPU affinity and exclusivity. |
        |**Taints**|Add taints to worker nodes in the ACK cluster. |

7.  Click **Next:Component Configurations** to configure components.

    |Parameter|Description|
    |---------|-----------|
    |**Ingress**|Specify whether to install Ingress controllers. By default, **Install Ingress Controllers** is selected. For more information, see [Configure an Ingress](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Ingress management/Configure an Ingress.md). |
    |**Volume Plug-in**|Select a volume plug-in. FlexVolume and CSI are supported. An ACK cluster can be automatically bound to cloud disks of Alibaba Cloud, Network Attached Storage \(NAS\) file systems, and Object Storage Service \(OSS\) buckets that are mounted to pods in the cluster. For more information, see [Storage management-FlexVolume](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/Overview.md) and [Storage management-CSI](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/Overview.md). |
    |**Monitoring Agents**|Specify whether to install the Cloud Monitor agent. By default, **Install CloudMonitor Agent on ECS Instance** is selected. |
    |**Log Service**|Specify whether to enable Log Service. You can select an existing Log Service project or create a new one. By default, **Enable Log Service** is selected. When you create an application, you can perform a few simple steps to enable Log Service. For more information, see [Use Log Service to collect container logs](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Log management/Use Log Service to collect container logs.md).

By default, **Install node-problem-detector and Create Event Center** is selected. You can also specify whether to **create Ingress dashboard** in the Log Service console. |
    |**Workflow Engine**|Specify whether to enable Alibaba Cloud Genomics Compute Service \(AGS\).

    -   If you select this check box, the system automatically installs the AGS workflow plug-in when the system creates the ACK cluster.
    -   If you clear this check box, you must manually install the AGS workflow plug-in. For more information, see [Introduction to AGS CLI](/intl.en-US/User Guide for Genomics Service/AGS workflow/Introduction to AGS CLI.md). |

8.  Click **Next:Confirm Order**.

9.  Read Terms of Service, select the check box, and then click **Create Cluster**.

    **Note:** It requires about 10 minutes to create a dedicated Kubernetes cluster that contains multiple nodes.


-   After the cluster is created, you can view the newly created cluster on the Clusters page in the ACK console.
-   Click **View Logs** in the Actions column. On the Log Information page, you can view the cluster log. To view detailed log information, click **Stack events**.
-   On the Clusters page, find the newly created cluster and click **Details** in the **Actions** column. On the details page of the cluster, click the **Basic Information** tab to view basic information about the cluster and click the **Connection Information** tab to view information about how to connect to the cluster.

    The following information is displayed:

    -   **API Server Public Endpoint**: the IP address and port that the API server uses to provide services over the Internet. It allows you to manage the cluster by using kubectl or other tools on the client.
    -   **API Server Internal Endpoint**: the IP address and port that the API server uses to provide services within the cluster. The endpoint belongs to the SLB instance that is bound to the cluster. Three master nodes work as the backend servers of the SLB instance.
    -   **Testing Domain**: the domain name that is used to test Services. The suffix of the domain name is `<cluster_id>.<region_id>.alicontainer.com`.

        **Note:** To rebind the domain name, click **Rebind Domain Name**.

-   You can [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md) and run the `kubectl get node` command to view information about the nodes in the cluster.

![Cluster information](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6565359951/p9050.png)


