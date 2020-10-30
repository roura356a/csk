# Create a managed Kubernetes cluster that runs sandboxed containers

This topic describes how to create a managed Kubernetes cluster that runs sandboxed containers in the Container Service for Kubernetes \(ACK\) console.

## Limits

-   SLB instances that are created together with the ACK cluster support only the pay-as-you-go billing method.
-   ACK clusters support only Virtual Private Cloud \(VPC\) networks.
-   Each account can consume only a limited amount of computing resources. You fail to create clusters if you do not have sufficient computing resources. When you create clusters, make sure that you have sufficient resources. To increase the quota of computing resources for your account, submit a ticket.
    -   You can create up to five clusters across regions for each account. You can deploy up to 40 nodes in each cluster. To increase the quota of clusters or nodes, [submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).

        **Note:** By default, you can add up to 48 route entries to a VPC. This means that you can configure up to 48 route entries for ACK clusters deployed in a VPC. To increase the quota of route entries for a VPC, [submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).

    -   You can create up to 100 security groups under each account.
    -   You can create up to 60 pay-as-you-go SLB instances under each account.
    -   You can create up to 20 elastic IP addresses \(EIPs\) under each account.
-   To create an ACK cluster that runs sandboxed containers, you must set the parameters as required in the following table.

    |Parameter|Description|
    |---------|-----------|
    |Zone|Select a zone where Elastics Compute Service \(ECS\) Bare Metal instances are deployed. Sandboxed containers support only ECS Bare Metal instances.|
    |Kubernetes Version|Select 1.14.6-aliyun.1 or later.|
    |Container Runtime|Select Sandboxed-Container.|
    |Worker Instance|Add worker nodes by creating new ECS instances.|
    |Billing Method|Select the subscription billing method.|
    |Instance Type|Select ECS Bare Metal instances.|
    |Mount Data Disk|Mount a data disk of at least 200 GiB. We recommend that you mount a data disk of at least 1 TB.|
    |Operating system|By default, the AliyunLinux operating system is used. You cannot change the operating system.|


## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click **Create Kubernetes Cluster** in the upper-right corner of the page.

4.  On the Select Cluster Template page, find **Standard Managed Cluster** and click **Create** in the **Standard Managed Cluster** section.

5.  On the **ACK managed edition** tab, configure the cluster.

    1.  Configure basic settings of the cluster.

        |Parameter|Description|
        |---------|-----------|
        |**Cluster Name**|Enter a name for the cluster of Container Service for Kubernetes \(ACK\).

**Note:** The name must be 1 to 63 characters in length, and can contain digits, letters, and hyphens \(-\). |
        |**Cluster specifications**|Select a cluster type. **Standard edition** and **Professional \(Preview\)** are supported. |
        |**Region**|Select a region to deploy the cluster. |
        |**Resource Group**|Move the pointer over **All Resources** at the top of the page and select the resource group to which the cluster belongs. The name of the selected resource group appears on the page.

![Resource Groups](../images/p127165.png) |
        |**Kubernetes Version**|Select a Kubernetes version.|
        |**Container Runtime**|Select **Sandboxed-Container**.|
        |**VPC**|Select a Virtual Private Cloud \(VPC\) network to deploy the cluster. Shared VPC networks and standard VPC networks are supported.

        -   Shared VPC network: The owner of a VPC network \(resource owner\) can share VSwitches in the VPC network under the account of the owner with other accounts in the same organization.
        -   Standard VPC network: The owner of a VPC network \(resource owner\) cannot share VSwitches in the VPC network under the account of the owner with other accounts.
**Note:** ACK clusters support only VPC networks. You can select a VPC network from the drop-down list. If no VPC network is available, click **Create VPC** to create one. For more information, see [Create a VPC](/intl.en-US/VPCs and VSwitches/VPC management/Create a VPC.md). |
        |**VSwitch**|Select VSwitches.

You can select up to three VSwitches deployed in different **zones**. If no VSwitch is available, click **Create VSwitch** to create one. For more information, see [Create a VSwitch](/intl.en-US/VPCs and VSwitches/VSwitch management/Create a VSwitch.md). |
        |**Network Plug-in**|Select a network plug-in. Both Flannel and Terway are supported. For more information, see [Flannel and Terway](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Use Terway.md).

        -   Flannel: a simple and stable Container Network Interface \(CNI\) plug-in developed by the Kubernetes community. Flannel offers a few simple features and does not support standard Kubernetes network policies.
        -   Terway: a network plug-in developed by ACK. Terway allows you to assign Alibaba Cloud elastic network interfaces \(ENIs\) to containers. It also allows you to customize Kubernetes network policies to control intercommunication among containers, and implement bandwidth throttling on individual containers.

**Note:**

            -   The number of pods that can be deployed on a node depends on the number of ENIs that are attached to the node and the maximum number of secondary IP addresses provided by these ENIs.
            -   If you select a shared VPC network for a cluster, you must select the Terway network plug-in.
            -   If you select **Terway**, an ENI is shared among multiple pods. A secondary IP address of the ENI is assigned to each pod. |
        |**Pod CIDR Block**|If you select **Flannel**, you must set **Pod CIDR Block**.

The CIDR block specified by **Pod CIDR Block** cannot overlap with that of the VPC network or existing ACK clusters in the VPC network. The CIDR Block cannot be modified after it is specified. The Service CIDR block cannot overlap with the Pod CIDR block. For more information about subnetting for ACK clusters, see [Assign CIDR blocks to resources in a Kubernetes cluster under a VPC](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Assign CIDR blocks to resources in a Kubernetes cluster under a VPC.md). |
        |**Pod VSwitch**|If you select Terway, you must allocate VSwitches to pods. Each pod VSwitch must correspond to a worker VSwitch. |
        |**Service CIDR**|Set **Service CIDR**. The CIDR block specified by **Service CIDR** cannot overlap with that of the VPC network or existing ACK clusters in the VPC network. The CIDR block cannot be modified after it is specified. The Service CIDR block cannot overlap with the Pod CIDR block. For more information about subnetting for ACK clusters, see [Assign CIDR blocks to resources in a Kubernetes cluster under a VPC](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Assign CIDR blocks to resources in a Kubernetes cluster under a VPC.md). |
        |**IP Addresses per Node**|If you select **Flannel**, you must set the number of **IP addresses per node**.

**Note:** **IP Addresses per Node** specifies the maximum number of IP addresses that can be assigned to each node. We recommend that you use the default value. |
        |**Configure SNAT**|By default, a cluster is not accessible over the Internet. If the VPC network that you select for the cluster cannot access the Internet, you can select **Configure SNAT for VPC**. Then, ACK creates a Network Address Translation \(NAT\) gateway and configures Source Network Address Translation \(SNAT\) entries to enable Internet access for the VPC network. |
        |**Public Access**|Specify whether to **expose the API server with an elastic IP address \(EIP\)**. |
        |**RDS Whitelist**|Set the Relational Database Service \(RDS\) whitelist. Add the IP addresses of cluster nodes to the RDS whitelist.

**Note:** To enable an RDS instance to access an ACK cluster, you must deploy the RDS instance in the same VPC network as the ACK cluster. |
        |**Security Group**|You can select **Create Basic Security Group**, **Create Advanced Security Group**, and **Select Existing Security Group**. For more information, see [Overview](/intl.en-US/Security/Security groups/Overview.md). |

    2.  Configure advanced settings of the cluster.

        |Parameter|Description|
        |---------|-----------|
        |**Kube-proxy Mode**|IPVS and iptables are supported.

        -   iptables is a kube-proxy mode. It uses iptables rules to conduct service discovery and load balancing. The performance of this mode is restricted by the size of the cluster. This mode is suitable for clusters that run a small number of Services.
        -   IPVS is a high-performance kube-proxy mode. It uses Linux Virtual Server \(LVS\) to conduct service discovery and load balancing. This mode is suitable for clusters that run a large number of Services. We recommend that you use this mode in scenarios where high-performance load balancing is required. |
        |**Labels**|Attach labels to nodes. Enter keys and values, and then click **Add**.

**Note:**

        -   Key is required. Value is optional.
        -   Keys are not case-sensitive. A key must be 1 to 64 characters in length, and cannot start with aliyun, http://, or https://.
        -   Values are not case-sensitive. A value must be 1 to 128 characters in length, and cannot start with http:// or https://.
        -   The keys of labels that are attached to the same resource must be unique. If you add a label with a used key, the label overwrites the one using the same key.
        -   You can attach up to 20 labels to each resource. If you attach more than 20 labels to a resource, all labels become invalid. You must detach unused labels for the remaining labels to take effect. |
        |**Cluster Domain**|Set the cluster domain.

**Note:** The default cluster domain is **cluster.local**. You can enter a custom domain. A domain consists of two parts. Each part must be 1 to 63 characters in length, and can contain only letters and digits. |
        |**Custom Certificate SANs**|You can enter custom subject alternative names \(SANs\) for the API server of the cluster. Separate multiple IP addresses or domains with commas \(,\). |
        |**Service Account Token Volume Projection**|**Service account token volume projection** reduces security risks when pods use service accounts to access the API server. This feature enables kubelet to request and store the token on behalf of the pod. This feature also allows you to configure token properties, such as the audience and validity duration. For more information, see [Deploy service account token volume projection](/intl.en-US/User Guide for Kubernetes Clusters/Security management/Use service account token volume projection.md). |
        |**Deletion Protection**|Specify whether to enable deletion protection. If you select this check box, the cluster cannot be deleted in the console or by calling the API. This allows you to avoid user errors. |

6.  Click **Next:Worker Configurations** to configure worker nodes.

    **Note:** To create an ACK cluster that runs sandboxed containers, you must select ECS Bare Metal instances as worker nodes.

    1.  Configure basic settings for worker nodes.

        |Parameter|Description|
        |---------|-----------|
        |**Worker Instance**|By default, **Create Instance** is selected. You cannot select **Add Existing Instance**.|
        |**Billing Method**|Select **Subscription**.**Note:** Worker instances support only ECS Bare Metal instances. ECS Bare Metal instances support only the **Subscription** billing method. The **pay-as-you-go** billing method is not supported by ECS Bare Metal instances. |
        |**Duration**|Select the duration of the subscription.|
        |**Auto Renewal**|Specify whether to enable **Auto Renewal**.|
        |**Instance Type**|Select **ECS Bare Metal Instance**. Only ECS Bare Metal instances are supported.|
        |**Selected Types**|The selected instance type appears here. You can select only ECS Bare Metal Instance as the instance type.|
        |**Quantity**|Specify the number of worker nodes to be created. |
        |**System Disk**|**ESSDs**, **SSDs**, and **ultra disks** are supported.

**Note:** You can select **Enable Backup** to back up disk data. |
        |**Mount Data Disk**|**ESSDs**, **SSDs**, and **ultra disks** are supported. You can enable disk **encryption** and **backup** when you add data disks.

**Note:** The data disk is used to store the root file system of all containers on the node. Therefore, you must mount a data disk of at least 200 GiB. We recommend that you mount a data disk of at least 1 TB. |
        |**Operating System**|By default, the AliyunLinux operating system is used. You cannot change the operating system.|
        |**Logon Type**|        -   Key Pair:
            -   **Key Pair**: Select an SSH key pair from the drop-down list.
            -   **create a key pair**: Create a key pair if no SSH key pair is available. For more information about how to create an SSH key pair, see [Create an SSH key pair](/intl.en-US/Security/Key pairs/Use an SSH key pair/Create an SSH key pair.md). After the key pair is created, set it as the credential for logging on to the cluster.
        -   Password:
            -   **Password**: Enter a password for logging on to the nodes.
            -   **Confirm Password**: Enter the password again. |

    2.  Configure advanced settings of worker nodes

        |Parameter|Description|
        |---------|-----------|
        |**Node Protection**|Specify whether to enable node protection.

**Note:** By default, this check box is selected. This way, cluster nodes cannot be deleted in the console or by calling the API. This allows you to avoid user errors. |
        |**User Data**|For more information, see [Prepare user data](/intl.en-US/Instance/Manage instances/User data/Prepare user data.md). |
        |**Custom Node Name**|Specify whether to use a **custom node name**.

A node name consists of a prefix, an IP substring, and a suffix.

        -   Both the prefix and suffix can contain one or more parts that are separated with periods \(**.**\) and must start and end with a lowercase letter or digit. These parts can contain lowercase letters, digits, and hyphens \(-\), and must start and end with a lowercase letter or digit.
        -   The IP substring length specifies the number of digits to be truncated from the end of the returned node IP address. Valid values: 5 to 12.
For example, if the node IP address is 192.1xx.x.xx, the prefix is aliyun.com, the IP substring length is 5, and the suffix is test, the node name will be aliyun.com00055test. |
        |**CPU Policy**|Set the CPU policy.

        -   none: This policy indicates that the default CPU affinity is used. This is the default policy.
        -   static: This policy allows pods with certain resource characteristics on the node to be granted with enhanced CPU affinity and exclusivity. |
        |**Taints**|Add taints to worker nodes in the cluster. |

7.  Click **Next:Component Configurations** to configure components.

    |Parameter|Description|
    |---------|-----------|
    |**Ingress**|Specify whether to install Ingress controllers. By default, **Install Ingress Controllers** is selected. For more information, see [Configure an Ingress](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Ingress management/Configure an Ingress.md).

**Note:** If you want to select **Create Ingress Dashboard**, you must first activate Log Service. |
    |**Volume Plug-in**|Only CSI is supported by ACK clusters that run sandboxed containers. ACK clusters can be automatically bound to cloud disks of Alibaba Cloud, Network Attached Storage \(NAS\) file systems, and Object Storage Service \(OSS\) bucketes that are mounted to pods. For more information, see [Storage management-CSI](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/Overview.md).|
    |**Monitoring Agents**|Specify whether to install the Cloud Monitor agent. By default, **Install CloudMonitor Agent on ECS Instance** and **Enable Prometheus Monitoring** are selected. After the Cloud Monitor agent is installed on ECS nodes, you can view monitoring information about the nodes in the Cloud Monitor console. |
    |**Log Service**|Specify whether to activate Log Service. You can select an existing Log Service project or create a Log Service project.

By default, **Enable Log Service** is selected. When you create an application, you can activate Log Service through a few steps. For more information, see [Use Log Service to collect container logs](/intl.en-US/User Guide for Kubernetes Clusters/Log management/Use Log Service to collect container logs.md).

By default, **Install node-problem-detector and Create Event Center** is selected. You can also specify whether to **create Ingress dashboard** in the Log Service console. |
    |**Workflow Engine**|Specify whether to activate Alibaba Cloud Genomics Compute Service \(AGS\).

    -   If you select this check box, the system automatically installs the AGS workflow plug-in when it creates the cluster.
    -   If you clear this check box, you must manually install the AGS workflow plug-in. For more information, see [Introduction to AGS CLI](/intl.en-US/User Guide for Genomics Service/AGS workflow/Introduction to AGS CLI.md). |

8.  Click **Next:Confirm Order**.

9.  Select **Terms of Service** and click **Create Cluster**.

    **Note:** It takes approximately 10 minutes for the system to create a Kubernetes cluster that consists of multiple nodes.


-   After the cluster is created, you can find the created cluster on the Clusters page in the console.

    ![Clusters](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/3565359951/p62541.png)

-   Click **View Logs** in the **Actions** column. On the Log Information page, you can view cluster logs. To view detailed log information, click **Stack events**.

    ![Cluster logs](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/3565359951/p21426.png)

-   Click **Details** in the **Actions** column. On the details page, click the **Basic Information** tab to view basic information about the cluster and click the **Connection Information** tab to view information about connections to the cluster.

    The following information is displayed.

    -   **API Server Public Endpoint**: the IP address and port that the Kubernetes API server uses to provide services over the Internet. It allows you to manage the cluster by using kubectl or other tools on the client.

        **Bind EIP** and **Unbind EIP**: These options are available to only managed Kubernetes clusters.

        -   Bind EIP: You can select an existing EIP or create one.

            The API server restarts after you bind an EIP to it. We recommend that you do not perform operations on the cluster during the restart process.

        -   Unbind EIP: You cannot access the API server over the Internet after you unbind the EIP.

            The API server restarts after you unbind the EIP from it. We recommend that you do not perform operations on the cluster during the restart process.

    -   **API Server Internal Endpoint**: the IP address and port that the Kubernetes API server uses to provide services within the cluster.
    -   **Testing Domain**: the domain name that is used for service testing. The suffix of the domain name is `<cluster_id>.<region_id>.alicontainer.com`.

        **Note:** To rebind the domain name, click **Rebind Domain Name**.

-   You can use kubectl to connect to the cluster and run the `kubectl get node` command to query information about the nodes in the cluster. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md).

