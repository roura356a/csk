# Create a managed GPU cluster

This topic describes how to create a managed GPU cluster for heterogeneous computing in the Container Service for Kubernetes \(ACK\) console.

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

    **Note:** After an ECS instance is created, you can change its billing method from pay-as-you-go to subscription in the ECS console. For more information, see [Change the billing method of an instance from pay-as-you-go to subscription](/intl.en-US/Pricing/Change the billing method/Switch the billing method from pay-as-you-go to subscription.md).


## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click **Create Kubernetes Cluster** in the upper-right corner of the page.

4.  In the **Select Cluster Template** dialog box, click **Create** on the **Managed Cluster for Heterogeneous Computing** card.

5.  Set the cluster parameters.

    1.  Configure basic settings.

        |Parameter|Description|
        |---------|-----------|
        |**Cluster Name**|Enter a name for the cluster. **Note:** The name must be 1 to 63 characters in length, and can contain digits, letters, hyphens \(-\), and Chinese characters. |
        |**Cluster specifications**|Select the required edition. Standard edition is selected by default.|
        |**Region**|Select the region where you want to create the cluster.|
        |**Resource Group**|Move the pointer over **All Resources** on the top of the page and select the resource group to which the cluster belongs. The name of the selected resource group is displayed in the Resource Group field.|
        |**Kubernetes Version**|Select a Kubernetes version. These versions are supported: 1.14.8-aliyun.1 and 1.16.9-aliyun.1.|
        |**Container Runtime**|Docker containers and sandboxed containers are supported.|
        |**VPC**|Select the Virtual Private Cloud \(VPC\) network where the cluster is deployed. Shared VPC networks and standard VPC networks are supported.         -   Shared VPC network: The owner of a VPC network \(resource owner\) can share VSwitches in the VPC network under the account of the owner with other accounts in the same organization.
        -   Standard VPC network: The owner of a VPC network \(resource owner\) cannot share VSwitches in the VPC network under the account of the owner with other accounts.
**Note:** Kubernetes clusters support only VPC networks. You can select a VPC network from the drop-down list. If no VPC network is available, you can click **Create VPC** to create one. For more information, see [Create a VPC](/intl.en-US/VPCs and VSwitches/VPC management/Create a VPC.md). |
        |**VSwitch**|Set the VSwitch. You can select up to three VSwitches that are deployed in different **zones**. If no VSwitch is available, click **Create VSwitch** to create one. For more information, see [Create a VSwitch](/intl.en-US/VPCs and VSwitches/VSwitch management/Create a VSwitch.md). |
        |**Network Plug-in**|Select a network plug-in. Flannel and Terway are available. For more information, see [Flannel and Terway](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Use Terway.md).         -   Flannel: a simple and stable Container Network Interface \(CNI\) plug-in developed by the Kubernetes community. Flannel offers a few simple features but does not support standard Kubernetes network policies.
        -   Terway: a network plug-in developed by ACK. Terway allows you to assign elastic network interfaces \(ENIs\) to containers. It also allows you to customize Kubernetes network policies to control intercommunication among containers, and implement bandwidth throttling on individual containers.

**Note:**

            -   The number of pods that can be deployed on a node depends on the number of ENIs that are attached to the node and the maximum number of secondary IP addresses provided by these ENIs.
            -   If you select a shared VPC network for a cluster, you must select the Terway network plug-in. |
        |**Pod CIDR Block**|If you select **Flannel**, you must set **Pod CIDR Block**. The CIDR block specified by **Pod CIDR Block** cannot overlap with the CIDR blocks that are used by the VPC network or existing clusters in the VPC network. After you create the cluster, you cannot modify the pod CIDR block. In addition, the service CIDR block cannot overlap with the pod CIDR block. For more information, see [Plan Kubernetes CIDR blocks under a VPC](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Plan Kubernetes CIDR blocks under a VPC.md). |
        |**Terway Mode**|If you set Network Plug-in to **Terway**, the **Terway Mode** parameter is available. When you set **Terway Mode**, you can select or clear **Assign One ENI to Each Pod**.

        -   If you select this check box, an ENI will be assigned to each pod.****
        -   If you clear this check box, an ENI will be shared among multiple pods. A secondary IP address of the ENI will be assigned to each pod.****
**Note:** This feature is available to only users in the whitelist. If you are not in the whitelist, to use this feature, [submit a ticket](https://selfservice.console.aliyun.com/ticket/scene/ecs/%E4%BA%91%E6%9C%8D%E5%8A%A1%E5%99%A8%20ECS/detail). |
        |**Service CIDR**|Set the **Service CIDR** parameter. The CIDR block specified by **Service CIDR** cannot overlap with the CIDR blocks that are used by the VPC network or existing clusters in the VPC network. After you create the cluster, you cannot modify the service CIDR block. In addition, the service CIDR block cannot overlap with the pod CIDR block. For more information, see [Plan Kubernetes CIDR blocks under a VPC](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Plan Kubernetes CIDR blocks under a VPC.md).|
        |**IP Addresses per Node**|If you set Network Plug-in to **Flannel**, the **IP Addresses per Node** parameter is available. **Note:** **IP Addresses per Node** specifies the maximum number of IP addresses that can be assigned to each node. We recommend that you use the default value. |
        |**Configure SNAT**|Specify whether to configure Source Network Address Translation \(SNAT\) rules for the VPC network.         -   If the specified VPC network has a Network Address Translation \(NAT\) gateway, Container Service for Kubernetes uses this NAT gateway.
        -   Otherwise, the system automatically creates a NAT gateway. If you do not want the system to create a NAT gateway, clear **Configure SNAT for VPC**. In this case, you must manually create a NAT gateway and configure SNAT rules to enable Internet access to the VPC network. Otherwise, the cluster cannot be created. |
        |**Public Access**|Select or clear **Expose API Server with EIP**. The Kubernetes API server provides multiple HTTP-based RESTful APIs that can be used to create, delete, modify, query, and watch resource objects such as pods and services.

        -   If you select this check box, an elastic IP address \(EIP\) is created and associated with the internal Server Load Balancer \(SLB\) instance. Port 6443 used by the API Server is opened on master nodes. You can connect to and manage the cluster by using kubeconfig over the Internet.
        -   If you clear this check box, no EIP is created. You can only connect to and manage the cluster by using kubeconfig within the VPC network. |
        |**RDS Whitelist**|Set the Relational Database Service \(RDS\) whitelist. Add the IP addresses of cluster nodes to the RDS whitelist.|
        |**Security Group**|**Create Basic Security Group**, **Create Advanced Security Group**, and **Select Existing Security Group** are available. For more information, see [Overview](/intl.en-US/Security/Security groups/Overview.md). |

    2.  Configure advanced settings.

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
        |**Custom Certificate SANs**|You can enter custom subject alternative names \(SANs\) for the API server of the cluster to accept requests from specified IP addresses or domains. |
        |**Service Account Token Volume Projection**|**Service account token volume projection** reduces security risks when pods use service accounts to access the API server. This feature enables kubelet to request and store the token on behalf of the pod. This feature also allows you to configure token properties, such as the audience and validity duration. For more information, see [Deploy service account token volume projection](/intl.en-US/User Guide for Kubernetes Clusters/Security management/Use service account token volume projection.md). |
        |**Deletion Protection**|Specify whether to enable deletion protection. If you select this check box, the cluster cannot be deleted in the console or by calling the API. This allows you to avoid user errors. |

6.  Click **Next:Worker Configurations** to configure worker nodes.

    1.  Set worker instances.

        -   If you select **Create Instance**, you must set the parameters listed in the following table.

            |Parameter|Description|
            |---------|-----------|
            |**Billing Method**|These billing methods are supported: **Pay-As-You-Go** and **Subscription**.|
            |**Duration**|If you select **Subscription**, you must set the duration of the subscription. Valid values: 1 Month, 2 Months, 3 Months, 6 Months, 1 Year, 2 Years, 3 Years, 4 Years, and 5 Years.|
            |**Auto Renewal**|If you select **Subscription**, you must specify whether to enable **Auto Renewal**.|
            |**Instance Type**|Choose **Heterogeneous Computing** \> **Compute Optimized Type with GPU** to show a list of available instance types, and select one or more required instance types from the list. For more information, see [Instance families](/intl.en-US/Instance/Instance families.md). **Note:** If no instance type is available, you can change VSwitches on the **Cluster Configurations** wizard page. |
            |**Selected Types**|The specified instance types appear here. |
            |**Quantity**|Set the number of worker nodes. |
            |**System Disk**|Enhanced SSDs \(ESSDs\), standard SSDs, and ultra disks are supported.**Note:** You can select **Enable Backup** to back up disk data. |
            |**Mount Data Disk**|ESSDs, standard SSDs, and ultra disks are supported. You can enable disk encryption and backup when you add data disks. |
            |**Operating System**|The CentOS and Aliyun Linux operating systems are supported.|
            |**Logon Type**|            -   Key Pair:

If no key pair is available, you can click **create a key pair** to create one in the Elastic Compute Service \(ECS\) console. For more information, see [Create an SSH key pair](/intl.en-US/Security/Key pairs/Use an SSH key pair/Create an SSH key pair.md). After the key pair is created, set it as the credential to log on to the cluster.

            -   Password:
                -   **Password**: Enter the password.
                -   **Confirm Password**: Enter the password again. |
            |**Key Pair**|

        -   If you select **Add Existing Instance**, you must select ECS instances that are deployed in the specified region. Then, set the **Operating System**, **Logon Type**, and **Key Pair** parameters in the same way as you create ECS instances.
    2.  Configure advanced settings.

        |Parameter|Description|
        |---------|-----------|
        |**Node Protection**|Specify whether to enable node protection.

**Note:** By default, this check box is selected. This way, cluster nodes cannot be deleted in the console or by calling the API. This allows you to avoid user errors. |
        |**User Data**|For more information, see [Prepare user data](/intl.en-US/Instance/Manage instances/User data/Prepare user data.md). |
        |**Custom Image**|You can select a custom image, and then use the image to deploy all nodes in the cluster. For more information about how to create a custom image, see [Create a Kubernetes cluster by using a custom image](/intl.en-US/Best Practices/Cluster/Create a Kubernetes cluster by using a custom image.md).

**Note:** This feature is available to only users in the whitelist. If you are not in the whitelist, [submit a ticket](https://selfservice.console.aliyun.com/ticket/scene/ecs/%E4%BA%91%E6%9C%8D%E5%8A%A1%E5%99%A8%20ECS/detail). |
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
    |**Ingress**|Specify whether to install Ingress controllers. By default, **Install Ingress Controllers** is selected. For more information, see [t16679.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Ingress management/Configure an Ingress.md). **Note:** If you select **Create Ingress Dashboard**, you must enable Log Service. |
    |**Volume Plug-in**|Select a volume plug-in. Flexvolume and CSI are supported. Kubernetes clusters can be automatically bound to Alibaba Cloud disks, Network Attached Storage \(NAS\) file systems, and Object Storage Service \(OSS\) buckets through pods. For more information, see [Storage management-Flexvolume](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/Overview.md) and [Storage management-CSI](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/Overview.md).|
    |**Monitoring Agents**|Specify whether to install the Cloud Monitor agent. By default, **Install CloudMonitor Agent on ECS Instance** and **Enable Prometheus Monitoring** are selected. After the Cloud Monitor agent is installed on ECS nodes, you can view monitoring information about the nodes in the Cloud Monitor console. |
    |**Log Service**|Specify whether to activate Log Service. You can select an existing Log Service project or create a Log Service project.

After you select **Enable Log Service**, you can specify whether to **create Ingress dashboards** and whether to **install node-problem-detector and create event centers**. |
    |**Workflow Engine**|Specify whether to activate Alibaba Cloud Genomics Compute Service \(AGS\).     -   If you select this check box, the system automatically installs the AGS workflow plug-in when it creates the cluster.
    -   If you clear this check box, you must manually install the AGS workflow plug-in. For more information, see [Introduction to AGS CLI](/intl.en-US/User Guide for Genomics Service/AGS workflow/Introduction to AGS CLI.md). |

8.  Click **Next:Confirm Order**.

9.  Select **Terms of Service** and click **Create Cluster**.

    **Note:** It takes approximately 10 minutes for the system to create a Kubernetes cluster that consists of multiple nodes.


## Related operations

After the cluster is created, go to the Clusters page, find the new cluster, and then click the cluster name or click **Details** in the **Actions** column of the cluster. The Cluster Information page appears. In the left-side navigation pane, click **Nodes** to go to the Nodes page. Select the worker node that is configured when you create the cluster, and choose **More** \> **Details** in the **Actions** column of the worker node. Then, you can view the GPU devices that are associated with the node.

