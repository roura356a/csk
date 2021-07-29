---
keyword: create a professional edge Kubernetes cluster
---

# Create a professional edge Kubernetes cluster

Professional edge Kubernetes clusters offer higher reliability, security, and schedulability than standard edge Kubernetes clusters. In addition, professional edge Kubernetes clusters are covered by the service level agreement \(SLA\) that supports compensation clauses. This type of cluster is suitable for enterprise users who require high stability and security for large-scale workloads. This topic describes how to create a professional edge Kubernetes cluster in the Container Service for Kubernetes \(ACK\) console.

Resource Access Management \(RAM\) is activated in the [RAM console](https://ram.console.aliyun.com/). Auto Scaling is activated in the [Auto Scaling console](https://essnew.console.aliyun.com).

**Note:**

When you create a Container Service for Kubernetes \(ACK\) cluster, take note of the following limits:

-   Server Load Balancer \(SLB\) instances that are created along with an ACK cluster support only the pay-as-you-go billing method.
-   ACK clusters support only VPCs.
-   By default, each account has specific quotas on cloud resources that can be created. You cannot create clusters if the quota is reached. Make sure that you have sufficient resource quotas before you create a cluster.
    -   For more information about the maximum numbers of clusters and nodes that can be created with each account, see [t16032.md\#section\_sm4\_by2\_chb](/intl.en-US/Product Introduction/Limits.md).
    -   By default, you can create up to 100 security groups with each account.
    -   By default, you can create up to 60 pay-as-you-go SLB instances with each account.
    -   By default, you can create up to 20 elastic IP addresses \(EIPs\) with each account.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  In the upper-right corner of the Clusters page, click **Create Kubernetes Cluster**.

4.  On the **Managed Edge Kubernetes** tab, configure the professional edge Kubernetes cluster.

    1.  Configure basic settings of the cluster.

        |Parameter|Description|
        |---------|-----------|
        |**Cluster Name**|Enter a name for the ACK cluster.

**Note:** The name must be 1 to 63 characters in length, and can contain digits, letters, and hyphens \(-\). |
        |**Cluster Specification**|Select a cluster type. You can select **Standard edition** or **Professional**.

Select **Professional** to create a professional edge Kubernetes cluster. |
        |**Region**|Select a region to deploy the cluster. |
        |**All Resources**|Move the pointer over **All Resources** at the top of the page and select the resource group that you want to use. After you select a resource group, virtual private clouds \(VPCs\) and vSwitches are filtered based on the selected resource group. When you create a cluster, only the VPCs and vSwitches that belong to the selected resource group are displayed in the console.

![Resource Groups](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9688404061/p127165.png) |
        |**Kubernetes Version**|The Kubernetes versions that are supported by professional edge Kubernetes clusters are displayed. |
        |**VPC**|Select a VPC to deploy the cluster. Standard VPCs and shared VPCs are supported.

        -   Shared VPC: The owner of a VPC \(resource owner\) can share the vSwitches in the VPC with other accounts in the same organization.
        -   Standard VPC: The owner of a VPC \(resource owner\) cannot share the vSwitches in the VPC with other accounts.
**Note:** ACK clusters support only VPCs. You can select a VPC from the drop-down list. If no VPC is available, click **Create VPC** to create one. For more information, see [Create a VPC](/intl.en-US/VPCs and vSwitchs/Work with VPCs.md). |
        |**VSwitch**|Select vSwitches.

You can select at most three vSwitches that are deployed in different **zones**. If no vSwitch is available, click **Create VSwitch** to create one. For more information, see [Create a vSwitch](/intl.en-US/VPCs and vSwitchs/Work with vSwitches.md). |
        |**Pod CIDR Block**|If you select **Flannel** as the network plug-in, you must set **Pod CIDR Block**.

The CIDR block specified by **Pod CIDR Block** cannot overlap with that of the VPC or those of the existing clusters in the VPC. The CIDR block cannot be modified after the cluster is created. The Service CIDR block cannot overlap with the pod CIDR block. For more information about subnetting for ACK clusters, see [Plan CIDR blocks for an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Network/Plan CIDR blocks for an ACK cluster.md). |
        |**Service CIDR**|Set **Service CIDR**. The CIDR block specified by **Service CIDR** cannot overlap with that of the VPC or those of the existing clusters in the VPC. The CIDR block cannot be modified after the cluster is created. The Service CIDR block cannot overlap with the pod CIDR block. For more information about subnetting for ACK clusters, see [Plan CIDR blocks for an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Network/Plan CIDR blocks for an ACK cluster.md). |
        |**IP Addresses per Node**|If you select **Flannel** as the network plug-in, you must set **IP Addresses per Node**.

**Note:**

        -   **IP Addresses per Node** specifies the maximum number of IP addresses that can be assigned to each node. We recommend that you use the default value.
        -   After you select the VPC and specify the number of IP addresses per node, recommended values are automatically generated for Pod CIDR block and Service CIDR block. The system also provides the maximum number of nodes that can be deployed in the cluster and the maximum number of pods that can be deployed on each node. You can modify the values based on your business requirements. |
        |**Configure SNAT**|By default, an ACK cluster cannot access the Internet. If the VPC that you select for the ACK cluster cannot access the Internet, you can select **Configure SNAT for VPC**. This way, ACK will create a NAT gateway and configure Source Network Address Translation \(SNAT\) rules to enable Internet access for the VPC. |
        |**Access to API Server**|By default, an internal-facing Server Load Balancer \(SLB\) instance is created for the cluster API server. You can modify the specification of the SLB instance. For more information, see [Instance types and specifications](/intl.en-US/Classic Load Balancer/User Guide/Instance/SLB instance overview.md).

**Note:** If you delete the SLB instance, you cannot access the cluster API server.

Select or clear **Expose API Server with EIP**. The ACK API server provides multiple HTTP-based RESTful APIs, which can be used to create, delete, modify, query, and monitor resources, such as pods and Services.

        -   If you select this check box, an elastic IP address \(EIP\) is created and associated with an Internet-facing SLB instance. Port 6443 used by the API server is opened on master nodes. You can connect to and manage the ACK cluster by using kubeconfig over the Internet.
        -   If you clear this check box, no EIP is created. You can connect to and manage the ACK cluster by using kubeconfig only within the VPC.
**Note:** Edge nodes interact with the API server in the cloud over the Internet. If you clear **Expose API Server with EIP**, the edge nodes cannot connect to the cluster in the cloud. As a result, the created cluster cannot be used in edge computing scenarios. |
        |**RDS Whitelist**|Set the Relational Database Service \(RDS\) whitelist. Add the IP addresses of the nodes in the cluster to the RDS whitelist.

**Note:** To enable an RDS instance to access the cluster, you must deploy the RDS instance in the VPC where the cluster is deployed. |
        |**Security Group**|You can select **Create Basic Security Group**, **Create Advanced Security Group**, or **Select Existing Security Group**. For more information, see [Overview](/intl.en-US/Security/Security groups/Overview.md).

**Note:** To select **Select Existing Security Group**, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm) to apply to be added to a whitelist. |
        |**Deletion Protection**|Specify whether to enable deletion protection. Deletion protection prevents the cluster from being accidentally deleted in the console or by calling the API. This prevents user errors. |
        |**Resource Group**|The resource group that owns the cluster to be created. Each resource can belong to only one resource group. You can regard a resource group as a project, an application, or an organization based on your business scenarios. For more information, see [Resource groups](/intl.en-US/Tag & Resource/Resource/Resource groups.md). |

    2.  Configure advanced settings of the cluster.

        |Parameter|Description|
        |---------|-----------|
        |**Kube-proxy Mode**|iptables and IPVS are supported.

        -   iptables is a mature and stable kube-proxy mode. It uses iptables rules to conduct service discovery and load balancing. The performance of this mode is restricted by the size of the ACK cluster. This mode is suitable for ACK clusters that manage a small number of Services.
        -   IPVS is a high-performance kube-proxy mode. It uses Linux Virtual Server \(LVS\) to conduct service discovery and load balancing. This mode is suitable for ACK clusters that manage a large number of Services. We recommend that you use this mode in scenarios where high-performance load balancing is required. |
        |**Labels**|Add labels to the cluster. Enter a key and a value, and click **Add**.

**Note:**

        -   Key is required. Value is optional.
        -   Keys are not case-sensitive. A key must be 1 to 64 characters in length, and cannot start with aliyun, http://, or https://.
        -   Values are not case-sensitive. A value can be empty and can contain up to 128 characters in length. It cannot be http:// or https://.
        -   The keys of labels that are added to the same resource must be unique. If you add a label with a used key, the label overwrites the others that use the same key.
        -   If you add more than 20 labels to a resource, all labels become invalid. You must remove excess labels for the remaining labels to take effect. |
        |**Secret Encryption**|If you select **Select Key**, you can use a key that is created in the Key Management Service \(KMS\) console to encrypt Kubernetes Secrets. For more information, see [Use KMS to encrypt Kubernetes Secrets](/intl.en-US/User Guide for Edge Container Service/ACK@Edge Pro edition cluster/Use KMS to encrypt Kubernetes Secrets.md).|

5.  Click **Next:Worker Configurations** to configure worker nodes.

    **Note:** In a professional edge Kubernetes cluster, you must configure at least one worker node to deploy controllers.

    |Parameter|Description|
    |---------|-----------|
    |**Instance Type**|You can select multiple instance types. For more information, see [Instance families](/intl.en-US/Instance/Instance families.md).

**Note:** To use advanced features such as logging, monitoring, and reverse tunneling, you must deploy the related components in the cloud. Therefore, you must create at least one Elastic Compute Service \(ECS\) instance as a worker node. |
    |**Selected Types**|The selected instance types are displayed. |
    |**Quantity**|Specify the number of worker nodes \(ECS instances\) to be created. |
    |**System Disk**|**Enhanced SSDs**, **standard SSDs**, and **ultra disks** are supported.

**Note:**

    -   You can select **Enable Backup** to back up disk data.
    -   If you select **enhanced SSD** as the system disk type, you can set a custom **performance level** for the system disk.

You can select higher performance levels for enhanced SSDs with larger storage capacities. For example, you can select performance level 2 for an enhanced SSD with a storage capacity of more than 460 GiB. You can select performance level 3 for an enhanced SSD with a storage capacity of more than 1,260 GiB. For more information, see [Capacity and PLs](/intl.en-US/Block Storage/Block Storage overview/ESSDs.md). |
    |**Mount Data Disk**|**Enhanced SSDs**, **standard SSDs**, and **ultra disks** are supported. You can enable **disk encryption** and **disk backup** when you mount a data disk. |
    |**Logon Type**|    -   Key pair logon
        -   **Key Pair**: Select an SSH key pair from the drop-down list.
        -   **create a key pair**: Create an SSH key pair if none is available. For more information about how to create an SSH key pair, see [Create an SSH key pair](/intl.en-US/Security/Key pairs/Use an SSH key pair/Create an SSH key pair.md). After the key pair is created, set it as the credential that is used to log on to the cluster.
    -   Password logon
        -   **Password**: Enter the password that is used to log on to the nodes.
        -   **Confirm Password**: Enter the password again.
**Note:** You must set the logon type if you select **Install CloudMonitor Agent on ECS Instance** or **Enable Log Service**. |

6.  Click **Next:Component Configurations** to configure components.

    |Parameter|Description|
    |---------|-----------|
    |**CloudMonitor Agent**|Select whether to install the CloudMonitor agent. If you select **Install the CloudMonitor Agent on ECS Nodes**, you can view monitoring data about the nodes in the CloudMonitor console. |
    |**Log Service**|Specify whether to enable Log Service. You can select an existing Log Service project or create one. By default, **Enable Log Service** is selected. When you create an application, you can enable Log Service through a few steps. For more information, see [Collect log files from containers by using Log Service](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Log management/Collect log files from containers by using Log Service.md).

You can also select or clear **Collect Logs of Control Plane Components**. If you select this check box, log data of control plane components is collected to the specified Log Service project within your account. For more information, see [Collect the logs of control plane components in a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Log management/Collect the logs of control plane components in a managed Kubernetes cluster.md).

**Note:** By default, **Collect Logs of Control Plane Components** is selected for a professional edge Kubernetes cluster. |

7.  Click **Next:Confirm Order**.

8.  Read **Terms of Service**, select the check box, and then click **Create Cluster**.

    **Note:** It requires about 10 minutes to create a professional edge Kubernetes cluster that contains multiple nodes.


-   After the cluster is created, you can view the created cluster on the Clusters page in the ACK console.
-   Click **View Logs** in the **Actions** column. On the Log Information page, you can view the cluster log. To view detailed log information, click **Stack events**.

-   Click **Details** in the **Actions** column. On the details page of the cluster, click the **Basic Information** tab to view basic information about the cluster. You can also click the **Connection Information** tab to view information about how to connect to the cluster. The following information is displayed:
    -   **API Server Public Endpoint**: the IP address and port that the API server uses to provide services over the Internet. It allows you to manage the cluster by using kubectl or other tools on your terminal.
    -   **API Server Internal Endpoint**: the IP address and port that the API server uses to provide services within the cluster. The IP address belongs to the SLB instance that is bound to the cluster.
    -   **Testing Domain**: the domain name that is used to test Services. The suffix of the domain name is *<cluster\_id\>.<region\_id\>.alicontainer.com*.

        **Note:** On the **Basic Information** tab, you can click **Rebind Domain Name** on the right side of **Testing Domain** to rebind the domain name.


