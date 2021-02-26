# Create a dedicated GPU cluster for heterogeneous computing

This topic describes how to create a dedicated GPU cluster for heterogeneous computing in the Container Service for Kubernetes \(ACK\) console.

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

4.  On the Clusters page, click **Create Kubernetes Cluster**. In the **Select Cluster Template** dialog box, click **Create** on the Dedicated Cluster for Heterogeneous Computing card. The Dedicated Kubernetes tab appears by default.

5.  In the upper-right corner of the Clusters page, click **Create Kubernetes Cluster**.

6.  7.  Set the cluster parameters.

    1.  Configure basic settings.

        |Parameter|Description|
        |---------|-----------|
        |**Cluster Name**|Enter a name for the cluster. **Note:** The name must be 1 to 63 characters in length, and can contain digits, letters, hyphens \(-\), and Chinese characters. |
        |**Region**|Select the region where the cluster is deployed.|
        |**Resource Group**|Move the pointer over **All Resources** on the top of the page and select the resource group to which the cluster belongs. The name of the selected resource group appears in the Resource Group field.|
        |**Kubernetes Version**|Select a Kubernetes version.|
        |**Container Runtime**|Dedicated Kubernetes clusters support only Docker.|
        |**VPC**|Select the Virtual Private Cloud \(VPC\) network where the cluster is deployed. Shared VPC networks and standard VPC networks are supported.         -   Shared VPC network: The owner of a VPC network \(resource owner\) can share VSwitches in the VPC network under the account of the owner with other accounts in the same organization.
        -   Standard VPC network: The owner of a VPC network \(resource owner\) cannot share VSwitches in the VPC network under the account of the owner with other accounts.
**Note:** Kubernetes clusters support only VPC networks. You can select a VPC network from the drop-down list. If no VPC network is available, you can click **Create VPC** to create one. For more information, see [Create a VPC](/intl.en-US/VPCs and vSwitchs/Create a VPC.md). |
        |**VSwitch**|Set the VSwitch. You can select up to three VSwitches that are deployed in different **zones**. If no VSwitch is available, click **Create VSwitch** to create one. For more information, see [t2436.md\#](/intl.en-US/VPCs and vSwitchs/Create a VSwitch.md). |
        |**Network Plug-in**|Select a network plug-in. Flannel and Terway are available. For more information, see [t64408.md\#section\_k1z\_f1n\_lmh](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Container network/Work with Terway.md).         -   Flannel: A simple and stable CNI plug-in developed by the community. Flannel offers a few simple features but does not support standard Kubernetes network policies.
        -   Terway: a network plug-in developed by ACK. Terway allows you to assign Alibaba Cloud Elastic Network Interfaces \(ENIs\) to containers. It also allows you to customize Kubernetes network policies to control intercommunication among containers, and implement bandwidth throttling on individual containers.

**Note:**

            -   The number of pods that can be deployed on a node depends on the number of ENIs that are attached to the node and the maximum number of secondary IP addresses provided by these ENIs.
            -   If you select a shared VPC network for a cluster, you must select the Terway network plug-in. |
        |**Pod CIDR Block**|If you set Network Plug-in to **Flannel**, the **Pod CIDR Block** parameter is available. The CIDR block specified by **Pod CIDR Block** cannot overlap with the CIDR blocks that are used by the VPC network or existing clusters in the VPC network. After you create the cluster, you cannot modify the pod CIDR block. In addition, the service CIDR block cannot overlap with the pod CIDR block. For more information, see [Plan CIDR blocks for an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Plan CIDR blocks for an ACK cluster.md). |
        |**Terway Mode**|If you set Network Plug-in to **Terway**, the **Terway Mode** parameter is available. When you set **Terway Mode**, you can select or clear **Assign One ENI to Each Pod**.

        -   If you select this check box, an ENI will be assigned to each pod.
        -   If you clear this check box, an ENI will be shared among multiple pods. A secondary IP address of the ENI will be assigned to each pod.
**Note:** This feature is available to only users in the whitelist. If you are not in the whitelist, [submit a ticket](https://selfservice.console.aliyun.com/ticket/scene/ecs/%E4%BA%91%E6%9C%8D%E5%8A%A1%E5%99%A8%20ECS/detail). |
        |**Pod VSwitch**|If you set Network Plug-in to **Terway**, the **Pod VSwitch** parameter is available. **Pod VSwitch** specifies VSwitches for pods. The ENIs that are assigned to pods must be in the same zone as the nodes that host the pods. For each VSwitch that has been assigned to nodes, select a VSwitch for pods in the same zone. Pod VSwitches will assign IP addresses to pods when the cluster is started. We recommend that you select VSwitches whose prefix length is no greater than 19 bits. This ensures that the number of pods is sufficient.|
        |**Service CIDR**|Set the **Service CIDR** parameter. The CIDR block specified by **Service CIDR** cannot overlap with the CIDR blocks that are used by the VPC network or existing clusters in the VPC network. After you create the cluster, you cannot modify the service CIDR block. In addition, the service CIDR block cannot overlap with the pod CIDR block. For more information, see [Plan CIDR blocks for an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Plan CIDR blocks for an ACK cluster.md).|
        |**IP Addresses per Node**|If you set Network Plug-in to **Flannel**, the **IP Addresses per Node** parameter is available. **Note:** **IP Addresses per Node** specifies the maximum number of IP addresses that can be assigned to each node. We recommend that you use the default value. |
        |**Configure SNAT**|Specify whether to configure Source Network Address Translation \(SNAT\) rules for the VPC network.         -   If the specified VPC network has a Network Address Translation \(NAT\) gateway, Container Service for Kubernetes uses this NAT gateway.
        -   Otherwise, the system automatically creates a NAT gateway. If you do not want the system to create a NAT gateway, clear **Configure SNAT for VPC**. In this case, you must manually create a NAT gateway and configure SNAT rules to enable Internet access to the VPC network. Otherwise, the cluster cannot be created. |
        |**Public Access**|Select or clear **Expose API Server with EIP**. The Kubernetes API server provides multiple HTTP-based RESTful APIs that can be used to create, delete, modify, query, and watch resource objects such as pods and services.

        -   If you select this check box, an Elastic IP address \(EIP\) is created and associated with the internal SLB instance. Port 6443 used by the API Server is opened on master nodes. You can connect to and manage the cluster by using kubeconfig over the Internet.
        -   If you clear this check box, no EIP is created. You can only connect to and manage the cluster by using kubeconfig within the VPC network. |
        |**SSH Logon**|Before you enable SSH, you must select **Expose API Server with EIP**.

        -   If you select this check box, you can access the cluster by using SSH.
        -   If you clear this check box, you cannot access the cluster by using SSH or kubectl. If you want to access an ECS instance in the cluster through SSH, you must manually bind an EIP to the instance and configure security group rules to open SSH port 22. For more information, see [t16642.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use SSH to connect to an ACK cluster.md). |
        |**RDS Whitelist**|Set the Relational Database Service \(RDS\) whitelist. Add the IP addresses of cluster nodes to the RDS whitelist.|
        |**Security Group**|**Create Basic Security Group**, **Create Advanced Security Group**, and **Select Existing Security Group** are available. For more information, see [Overview](/intl.en-US/Security/Security groups/Overview.md). |

    2.  Configure advanced settings.

        |Parameter|Description|
        |---------|-----------|
        |**Kube-proxy Mode**|iptables and IPVS are supported.

        -   iptables is a kube-proxy mode. It uses iptables rules to conduct service discovery and load balancing. The performance of this mode is restricted by the size of the cluster. This mode is suitable for clusters that run a small number of services.
        -   IPVS is a high-performance kube-proxy mode. It uses Linux IP Virtual Server \(IPVS\) to conduct service discovery and load balancing. This mode is suitable for clusters that run a large number of services. We recommend that you use this mode in scenarios where high-performance load balancing is required. |
        |**Labels**|Attach labels to the cluster. Enter keys and values, and click **Add**.

**Note:**

        -   The key field is required and the value field is optional.
        -   Keys are not case-sensitive. A key must be 1 to 64 characters in length and cannot start with aliyun, http://, or https://.
        -   Values are not case-sensitive. A value must be 1 to 128 characters in length and cannot start with http:// or https://.
        -   The keys of labels attached to the same resource must be unique. If you add a label with a used key, the label overwrites the one that uses the same key.
        -   You can attach up to 20 labels to each resource. If you attach more than 20 labels to a resource, all labels become invalid. You must detach unused labels for the other labels to take effect. |
        |**Custom Image**|If you select a custom image, the default image will be replaced. |
        |**Cluster Domain**|Set the cluster domain. **Note:** The default cluster domain is **cluster.local**. You can enter a custom domain. A domain consists of two parts. Each part must be 1 to 63 characters in length and can only contain letters and digits. |
        |**Custom Certificate SANs**|Specify the Subject Alternative Names \(SANs\) included on the API server certificate. Separate multiple IP addresses or domains with commas \(,\). |
        |**Service Account Token Volume Projection**|Enable **Service Account Token Volume Projection** to enhance security when you use service accounts. For more information, see [Deploy service account token volume projection](/intl.en-US/User Guide for Kubernetes Clusters/Security management/Use service account token volume projection.md). |
        |**Cluster CA**|If you select Custom Cluster CA, upload a CA certificate for the Kubernetes cluster to protect data transmission between the server and client.|
        |**Deletion Protection**|Specify whether to enable deletion protection. If you select this check box, the cluster cannot be deleted in the console or by calling API operations. This allows you to avoid user errors.|

8.  Click **Next:Master Configurations** to configure master nodes.

    |Parameter|Description|
    |---------|-----------|
    |**Billing Method**|The **Pay-As-You-Go** and **Subscription** billing methods are supported.|
    |**Duration**|If you select **Subscription**, you must set the duration of the subscription.|
    |**Auto Renewal**|If you select **Subscription**, you must specify whether to enable **Auto Renewal**.|
    |**Master Node Quantity**|Set the number of master nodes. You can create three or five master nodes.|
    |**Instance Type**|Select an instance type for each master node. For more information, see [Instance families](/intl.en-US/Instance/Instance families.md).**Note:** If no instance type is available, you can change VSwitches on the **Cluster Configurations** wizard page. |
    |**System Disk**|By default, system disks are mounted to master nodes. Enhanced \(ESSDs\), standard SSDs, and ultra disks are supported.**Note:** You can select **Enable Backup** to back up disk data. |

9.  Click **Next:Worker Configurations** to configure worker nodes.

    1.  Set worker instances.

        -   If you select **Create Instance**, you must set the parameters listed in the following table.

            |Parameter|Description|
            |---------|-----------|
            |**Instance Type**|Choose **Heterogeneous Computing** \> **Compute Optimized Type with GPU** to show a list of available instance types, and select one or more required instance types from the list. For more information, see [Instance families](/intl.en-US/Instance/Instance families.md). **Note:** If no instance type is available, you can change VSwitches on the **Cluster Configurations** wizard page. |
            |**Selected Types**|The specified instance types appear here.|
            |**Quantity**|Set the number of worker nodes.|
            |**System Disk**|ESSDs, standard SSDs, and ultra disks are supported.**Note:** You can select **Enable Backup** to back up disk data. |
            |**Mount Data Disk**|ESSDs, standard SSDs, and ultra disks are supported. You can enable disk encryption and backup when you add data disks. |
            |**Operating System**|The CentOS and Aliyun Linux operating systems are supported.|
            |Logon Type|            -   Key pair logon.
                -   **Key Pair**: Select an SSH key pair from the drop-down list.
                -   **create a key pair**: Create an SSH key pair if no SSH key pair is available. For more information about how to create an SSH key pair, see [Create an SSH key pair](/intl.en-US/Security/Key pairs/Use an SSH key pair/Create an SSH key pair.md). After the key pair is created, set it as the credential that is used to log on to the cluster.
            -   Set the password.
                -   **Password**: Enter the password that is used to log on to the nodes.
                -   **Confirm Password**: Enter the password again. |

        -   If you select **Add Existing Instance**, make sure that you have created ECS instances that are deployed in the specified region. Then, set **Operating System**, **Logon Type**, and **Key Pair** based on the preceding settings.
    2.  Configure advanced settings.

        |Parameter|Description|
        |---------|-----------|
        |**Node Protection**|Specify whether to enable node protection. **Note:** By default, this check box is selected. Cluster nodes cannot be deleted in the console or by calling API operations. This avoids user errors. |
        |**User Data**|For more information, see [Prepare user data](/intl.en-US/Instance/Manage instances/User data/Prepare user data.md).|
        |**Custom Node Name**|Specify whether to use a custom node name.A node name consists of a prefix, an IP substring, and a suffix.

        -   Both the prefix and suffix can contain one or more parts that are separated with periods \(**.**\). These parts can contain lowercase letters, digits, and hyphens \(**-**\), and must start and end with a digit or lowercase letter.
        -   The IP substring length specifies the number of digits at the end of the returned node IP address. Valid values: 5 to 12.
For example, if the node IP address is 192.168.0.55, the prefix is aliyun.com, the IP substring length is 5, and the suffix is test, the node name is aliyun.com00055test. |
        |**Node Port Range**|Set the node port range. The default port range is 30000 to 32767.|
        |**CPU Policy**|Set the CPU policy.         -   none: This policy indicates that the default CPU affinity is used. This is the default policy.
        -   static: This policy allows pods with certain resource characteristics on the node to be granted with enhanced CPU affinity and exclusivity. |
        |**Taints**|Add taints to worker nodes in the cluster.|

10. Click **Next:Component Configuration** to configure components.

    |Parameter|Description|
    |---------|-----------|
    |**Ingress**|Specify whether to install Ingress controllers. By default, **Install Ingress Controllers** is selected. For more information, see [Configure an Ingress](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Ingress management/Configure an Ingress.md).|
    |**Volume Plug-in**|Select a storage plug-in. Flexvolume and CSI are supported. Kubernetes clusters can be automatically bound to Alibaba Cloud disks, Network Attached Storage \(NAS\) file systems, and Object Storage Service \(OSS\) buckets through pods. For more information, see [Storage management - Flexvolume](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/Overview.md) and [Storage management - CSI](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/Overview.md).|
    |**Monitoring Agents**|Specify whether to install the Cloud Monitor agent. After the Cloud Monitor agent is installed on ECS instances, you can view monitoring information about the nodes in the Cloud Monitor console. |
    |**Log Service**|Specify whether to enable Log Service. You can select an existing project or create a project.

If you select **Enable Log Service**, the Log Service plug-in is automatically installed in the cluster. When you create an application, you can activate Log Service through a few steps. For more information, see [Use Log Service to collect container logs](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Log management/Use Log Service to collect container logs.md).

After you select **Enable Log Service**, you can specify whether to **create Ingress dashboards** and whether to **install node-problem-detector and create event centers**. |
    |**Workflow Engine**|Specify whether to activate Alibaba Cloud Genomics Compute Service \(AGS\).     -   If you select this check box, the system automatically installs the AGS workflow plug-in when it creates the cluster.
    -   If you clear this check box, you must manually install the AGS workflow plug-in. For more information, see [Introduction to AGS CLI](/intl.en-US/User Guide for Genomics Service/AGS workflow/Introduction to AGS CLI.md). |

11. Click **Next:Confirm Order**.

12. Select **Terms of Service** and click **Create Cluster**.

    **Note:** It takes approximately 10 minutes for the system to create a Kubernetes cluster that consists of multiple nodes.


## Related operations

After the cluster is created, go to the Clusters page, find the new cluster, and then click the cluster name or click **Details** in the **Actions** column of the cluster. The Cluster Information page appears. In the left-side navigation pane, click **Nodes** to go to the Nodes page. Select the worker node that is configured when you create the cluster, and choose **More** \> **Details** in the **Actions** column of the worker node. Then, you can view the GPU devices that are associated with the node.

