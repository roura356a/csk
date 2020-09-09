---
keyword: [Kubernetes, create Kubernetes clusters, clusters of ACK Proprietary Edition]
---

# Create a cluster of ACK Proprietary Edition

A cluster of Alibaba Cloud Container Service for Kubernetes \(ACK\) Proprietary Edition contains at least three master nodes. This ensures high availability and provides fine-grained management on clusters. You must manually size, maintain, and upgrade clusters of ACK Proprietary Edition. This topic describes how to create a cluster of ACK Proprietary Edition in the ACK console.

Log on to the [RAM console](https://ram.console.aliyun.com/) to activate Resource Access Management \(RAM\). Log on to the [ESS console](https://essnew.console.aliyun.com) to activate Auto Scaling \(ESS\).

**Note:**

When you create an ACK cluster, note the following limits:

-   SLB instances created together with the ACK cluster support only the pay-as-you-go billing method.
-   ACK clusters support only Virtual Private Cloud \(VPC\) networks.
-   Each account can consume only a limited amount of computing resources. You fail to create clusters if you do not have sufficient computing resources. To increase the quota of computing resources for your account, submit a ticket.
    -   You can create up to 50 clusters across regions for each account. You can deploy up to 100 nodes in each cluster. To increase the quota of clusters or nodes for your account, [submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).

        **Note:** In an ACK cluster, you can add up to 48 route entries to each VPC network by default. This means that you can configure up to 48 route entries for ACK clusters deployed in a VPC network. To increase the quota of route entries for a VPC network, submit a ticket.

    -   You can create up to 100 security groups under each account.
    -   You can create up to 60 pay-as-you-go SLB instances under each account.
    -   You can create up to 20 elastic IP addresses \(EIPs\) under each account.
-   Limits on ECS instances:

    The pay-as-you-go and subscription billing methods are supported.


1.  Log on to the [Container Service console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, choose **Clusters** \> **Clusters**.

3.  On the Clusters page, click **Create Kubernetes Cluster**. On the Select Cluster Template page, select **Standard Dedicated Cluster**. Click **Create**. The **ACK Proprietary Edition** configuration page appears.

4.  Configure the cluster.

    1.  Configure basic settings.

        |Parameter|Description|
        |---------|-----------|
        |**Cluster Name**|Enter the name of a cluster of Alibaba Cloud Container Service for Kubernetes \(ACK\).

**Note:** The name must be 1 to 63 characters in length and can contain digits, letters, and hyphens \(-\). |
        |**Region**|Select the region where the cluster is deployed. |
        |**Resource Group**|Move the pointer over **All Resources** on the top of the page and select the resource group to which the cluster belongs. The name of the selected resource group appears on the page.

![Resource Groups](../images/p127165.png) |
        |**Kubernetes Version**|Select a Kubernetes version. |
        |**Container Runtime**|**Docker** and **Sandboxed-Container** are supported. |
        |**VPC**|Select the Virtual Private Cloud \(VPC\) network where the cluster is deployed. Shared VPC networks and standard VPC networks are supported.

        -   Shared VPC network: The owner of a VPC network \(resource owner\) can share VSwitches in the VPC network under the account of the owner with other accounts in the same organization.
        -   Standard VPC network: The owner of a VPC network \(resource owner\) cannot share VSwitches in the VPC network under the account of the owner with other accounts.
**Note:** Kubernetes clusters support only VPC networks. You can select a VPC network from the drop-down list. If no VPC network is available, you can click **Create VPC** to create one. For more information, see [Create a VPC](/intl.en-US/VPCs and VSwitches/VPC management/Create a VPC.md). |
        |**VSwitch**|Select VSwitches.

You can select up to three VSwitches deployed in different **zones**. If no VSwitch is available, click **Create VSwitch** to create one. For more information, see [Create a VSwitch](/intl.en-US/VPCs and VSwitches/VSwitch management/Create a VSwitch.md). |
        |**Network Plug-in**|Select a network plug-in. Both Flannel and Terway are supported. For more information, see [Flannel and Terway](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Use Terway.md).

        -   Flannel: a simple and stable Container Network Interface \(CNI\) plug-in developed by the Kubernetes community. Flannel offers a few simple features and does not support standard Kubernetes network policies.
        -   Terway: a network plug-in developed by ACK. Terway allows you to assign Alibaba Cloud Elastic Network Interfaces \(ENIs\) to containers. It also allows you to customize Kubernetes network policies to control intercommunication among containers, and implement bandwidth throttling on individual containers.

**Note:**

            -   The number of pods that can be deployed on a node depends on the number of ENIs that are attached to the node and the maximum number of secondary IP addresses provided by these ENIs.
            -   If you select a shared VPC network for a cluster, you must select the Terway network plug-in.
            -   If you select **Terway**, an ENI is shared among multiple pods. A secondary IP address of the ENI is assigned to each pod. |
        |**Pod CIDR Block**|If you select **Flannel**, you must set **Pod CIDR Block**.

The CIDR block specified by **Pod CIDR Block** cannot overlap with that of the VPC network or existing ACK clusters in the VPC network. The CIDR Block cannot be modified after it is specified. The Service CIDR block cannot overlap with the Pod CIDR block. For more information about subnetting for ACK clusters, see [Plan Kubernetes CIDR blocks under a VPC](/intl.en-US/Best Practices/Cluster/Plan Kubernetes CIDR blocks under a VPC.md). |
        |**Terway Mode**|If you select **Terway**, you must set **Terway Mode**.

Select or clear the **Assign One ENI to Each Pod** check box.

        -   If you select the check box, an ENI is assigned to each pod.
        -   If you clear the check box, an ENI is shared among multiple pods. A secondary IP address provided by the ENI is assigned to each pod.
Select or clear the **IPVLAN** check box.

        -   This option is available only when you clear the Assign One ENI to Each Pod check box.
        -   If you select IPVLAN, IPVLAN and the extended Berkeley Packet Filter \(eBPF\) are used for network virtualization when an ENI is shared among multiple pods. This improves network performance. Only the Aliyun Linux 2 operating system is supported.
        -   If you clear IPVLAN, policy-based routes are used for network virtualization when an ENI is shared among multiple pods. The CentOS 7 and Aliyun Linux 2 operating systems are supported. This is the default setting.
**Note:** The preceding two features are available to only users in the whitelist. If you are not in the whitelist, [submit a ticket](https://workorder-intl.console.aliyun.com/console.htm). |
        |**Pod VSwitch**|If you select Terway, you must allocate VSwitches to pods. Each pod VSwitch must correspond to a worker VSwitch.

Elastic Network Interfaces \(ENIs\) must be deployed in the zone where the ENI devices are located. Therefore, you must select another VSwitch for the pods in the cluster. When the cluster provides services, the VSwitch assigns IP addresses to the pods. To ensure that there are sufficient IP addresses for all pods, we recommend that you set the mask length of the CIDR block to a value no greater than 19 for the VSwitch. |
        |**Service CIDR**|Set **Service CIDR**. The CIDR block specified by **Service CIDR** cannot overlap with that of the VPC network or existing ACK clusters in the VPC network. The CIDR block cannot be modified after it is specified. The Service CIDR block cannot overlap with the Pod CIDR block. For more information about subnetting for ACK clusters, see [Plan Kubernetes CIDR blocks under a VPC](/intl.en-US/Best Practices/Cluster/Plan Kubernetes CIDR blocks under a VPC.md). |
        |**IP Addresses per Node**|If you select **Flannel**, you must set the number of **IP addresses per node**.

**Note:** **IP Addresses per Node** specifies the maximum number of IP addresses that can be assigned to each node. We recommend that you use the default value. |
        |**Configure SNAT**|By default, a cluster is not accessible from the Internet. If the VPC network that you select for the cluster cannot access the Internet, you can select the **Configure SNAT for VPC** check box. Then, ACK creates a Network Address Translation \(NAT\) gateway and Source Network Address Translation \(SNAT\) entries to enable Internet access for the VPC network. |
        |**Public Access**|Specify whether to **expose the API server with an elastic IP address \(EIP\)**.

The ACK API server provides multiple HTTP-based RESTful APIs, which can be used to create, delete, modify, query, and monitor resources such as pods and services.

        -   If you select this check box, an elastic IP address is created and attached to an internal Server Load Balancer \(SLB\) instance. Port 6443 used by the API server is opened on master nodes. You can connect to and manage the cluster by using kubeconfig over the Internet.
        -   If you clear this check box, no elastic IP address is created. You can connect to and manage the cluster only through kubeconfig from within the VPC network. |
        |**SSH Logon**|To enable SSH logon, you must first select the **Expose API Server with EIP** check box.

        -   If you select this check box, you can use SSH to access the cluster.
        -   If you clear this check box, you cannot use SSH or kubectl to connect to the cluster. If you want to access an ECS instance in the cluster through SSH, you must manually attach an EIP to the instance and configure security group rules to open SSH port 22. For more information, see [Use SSH to connect to a cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Manage and access clusters/Use SSH to connect to a cluster.md). |
        |**RDS Whitelist**|Set the Relational Database Service \(RDS\) whitelist. Add the IP addresses of cluster nodes to the RDS whitelist.

**Note:** To enable an RDS instance to access an ACK cluster, you must deploy the RDS instance in the same VPC network as the ACK cluster. |
        |**Security Group**|You can select **Create Basic Security Group**, **Create Advanced Security Group**, and **Select Existing Security Group**. For more information, see [Overview](/intl.en-US/Security/Security groups/Overview.md). |

    2.  Configure advanced settings.

        |Parameter|Description|
        |---------|-----------|
        |**Kube-proxy Mode**|iptables and IPVS are supported.

        -   iptables is a kube-proxy mode. It uses iptables rules to conduct service discovery and load balancing. The performance of this mode is restricted by the size of the cluster. This mode is suitable for clusters that run a small number of services.
        -   IPVS is a high-performance kube-proxy mode. It uses Linux Virtual Server \(LVS\) to conduct service discovery and load balancing. This mode is suitable for clusters that run a large number of services. We recommend that you use this mode in scenarios where high-performance load balancing is required. |
        |**Labels**|Attach labels to nodes. Enter keys and values, and click **Add**.

**Note:**

        -   Key is required. Value is optional.
        -   Keys are not case-sensitive. A key must be 1 to 64 characters in length and cannot start with aliyun, http://, or https://.
        -   Values are not case-sensitive. A value must be 1 to 128 characters in length and cannot start with http:// or https://.
        -   The keys of labels attached to the same resource must be unique. If you add a label with a used key, the label overwrites the one using the same key.
        -   You can attach up to 20 labels to each resource. If you attach more than 20 labels to a resource, all labels become invalid. You must detach unused labels for the remaining labels to take effect. |
        |**Custom Image**|You can select a custom image, and then use the image to deploy all nodes in the cluster. For more information about how to create a custom image, see [Create a cluster by using a custom image](/intl.en-US/Best Practices/Cluster/Create a cluster by using a custom image.md).

**Note:** This feature is available to only users in the whitelist. If you are not in the whitelist, [submit a ticket](https://selfservice.console.aliyun.com/ticket/scene/ecs/%E4%BA%91%E6%9C%8D%E5%8A%A1%E5%99%A8%20ECS/detail). |
        |**Cluster Domain**|Set the cluster domain.

**Note:** The default cluster domain is **cluster.local**. You can enter a custom domain. A domain consists of two parts. Each part must be 1 to 63 characters in length and can only contain letters and digits. |
        |**Custom Certificate SANs**|You can enter custom subject alternative names \(SANs\) for the API server of the cluster to accept requests from specified IP addresses or domains. |
        |**Service Account Token Volume Projection**|**Service account token volume projection** reduces security risks when pods use service accounts to access the API server. This feature enables kubelet to request and store the token on behalf of the pod. This feature also allows you to configure token properties, such as the audience and validity duration. For more information, see [Deploy service account token volume projection](/intl.en-US/User Guide for Kubernetes Clusters/Security management/Use service account token volume projection.md). |
        |**Cluster CA**|If you select this check box, upload a CA certificate for the ACK cluster to ensure secure data transmission between the server and client.|
        |**Deletion Protection**|Specify whether to enable deletion protection. If you select this check box, the cluster cannot be deleted in the console or by calling API operations. This avoids user errors. |

5.  Click **Next:Master Configurations** to configure master nodes.

    |Parameter|Description|
    |---------|-----------|
    |**Billing Method**|The **pay-as-you-go** and **subscription** billing methods are supported. If you select **pay-as-you-go**, you must set the following parameters:

    -   **Duration**: You can select 1, 2, 3, or 6 months. If you require a longer duration, you can select 1 to 5 years.
    -   **Auto Renewal**: Specify whether to enable auto-renewal. |
    |**Master Node Quantity**|Select the number of master nodes. You can create three or five master nodes.|
    |**Instance Type**|Select the instance type for each master node. For more information, see [Instance families](/intl.en-US/Instance/Instance families.md).|
    |**System Disk**|By default, system disks are mounted to master nodes. SSDs and ultra disks are supported.**Note:** You can select the **Enable Backup** check box to back up data. |

6.  Click **Next:Worker Configurations** to configure worker nodes.

    1.  Select instances for worker nodes.

        -   If you select **Create Instance**, you must set the parameters listed in the following table.

            |Parameter|Description|
            |---------|-----------|
            |**Instance Type**|You can select multiple instance types. For more information, see [Instance families](/intl.en-US/Instance/Instance families.md). |
            |**Selected Types**|The selected instance types are displayed. |
            |**Quantity**|Specify the number of worker nodes to be created. |
            |**System Disk**|**ESSDs**, **SSDs**, and **ultra disks** are supported.

**Note:** You can select the **Enable Backup** check box to back up disk data. |
            |**Mount Data Disk**|**ESSDs**, **SSDs**, and **ultra disks** are supported. You can enable disk **encryption** and **backup** when you add data disks. |
            |**Operating System**|The CentOS and Aliyun Linux operating systems are supported. |
            |**Logon Type**|            -   Key Pair:

If you want to use a key pair, click **create a key pair** to create one in the Elastic Compute Service \(ECS\) console. For more information, see [Create an SSH key pair](/intl.en-US/Security/Key pairs/Use an SSH key pair/Create an SSH key pair.md). After the key pair is created, set it as the credential for logging on to the cluster.

            -   Password:
                -   **Password**: Enter the password.
                -   **Confirm Password**: Enter the password again. |
            |**Key Pair**|

        -   If you select **Add Existing Instance**, make sure that you have created ECS instances that are deployed in the specified region. Then, configure **Operating System**, **Logon Type**, and **Key Pair** based on the preceding settings.
    2.  Configure advanced settings.

        |Parameter|Description|
        |---------|-----------|
        |**Node Protection**|Specify whether to enable node protection.

**Note:** By default, this check box is selected. Cluster nodes cannot be deleted in the console or by calling API operations. This avoids user errors. |
        |**User Data**|For more information, see [Prepare user data](/intl.en-US/Instance/Manage instances/User data/Prepare user data.md). |
        |**Custom Node Name**|Specify whether to use a **custom node name**.

A node name consists of a prefix, an IP substring, and a suffix.

        -   Both the prefix and suffix can contain one or more parts that are separated with periods \(**.**\) and must start and end with a lowercase letter or digit.
        -   The IP substring length specifies the number of digits to be truncated from the end of the returned node IP address. Valid values: 5 to 12.
For example, if the node IP address is 192.1xx.x.xx, the prefix is aliyun.com, the IP substring length is 5, and the suffix is test, the node name will be aliyun.com00055test. |
        |**Node Port Range**|Specify the node port range. The default port range is 30000 to 32767.|
        |**CPU Policy**|Set the CPU policy.

        -   none: This policy indicates that the default CPU affinity is used. This is the default policy.
        -   static: This policy allows pods with certain resource characteristics on the node to be granted with enhanced CPU affinity and exclusivity. |
        |**Taints**|Add taints to worker nodes in the cluster. |

7.  Click **Next:Component Configurations** to configure components.

    |Parameter|Description|
    |---------|-----------|
    |**Ingress**|Specify whether to install Ingress controllers. By default, the **Install Ingress Controllers** check box is selected. For more information, see [Configure an Ingress](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Ingress management/Configure an Ingress.md). |
    |**Volume Plug-in**|Select a volume plug-in. Flexvolume and CSI are supported. ACK clusters can be automatically bound to cloud disks of Alibaba Cloud, Network Attached Storage \(NAS\) volumes, and Object Storage Service \(OSS\) volumes that are mounted to pods. For more information, see [Storage management-Flexvolume](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/Overview.md) and [Storage management-CSI](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/Overview.md). |
    |**Monitoring Agents**|Specify whether to install the Cloud Monitor agent. **After the Cloud Monitor agent is installed on ECS nodes**, you can view monitoring information about the nodes in the Cloud Monitor console. You can also enable **Prometheus Monitoring**. |
    |**Log Service**|Specify whether to enable Log Service. You can select an existing project or create a project.

If you select the **Enable Log Service** check box, the Log Service plug-in is automatically installed in the cluster. When you create an application, you can activate Log Service through a few steps. For more information, see [Use Log Service to collect Kubernetes logs](/intl.en-US/User Guide for Kubernetes Clusters/Log management/Use Log Service to collect Kubernetes cluster logs.md).

After you select the **Enable Log Service** check box, you can specify whether to **create Ingress dashboards** and whether to **install node-problem-detector and create event centers**. |
    |**Workflow Engine**|Specify whether to activate Alibaba Cloud Genomics Compute Service \(AGS\).

    -   If you select this check box, the system automatically installs the AGS workflow plug-in when it creates the cluster.
    -   If you clear this check box, you must manually install the AGS workflow plug-in. For more information, see [Introduction to AGS CLI](/intl.en-US/User Guide for Genomics Service/AGS workflow/Introduction to AGS CLI.md). |

8.  Click **Next:Confirm Order**.

9.  Select the Terms of Service check box and click **Create Cluster**.

    **Note:** It takes about 10 minutes to create an ACK cluster that consists of multiple nodes.


-   After the cluster is created, you can view the created cluster on the Clusters page in the console.

    ![Clusters](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/6565359951/p9047.png)

-   Click **View Logs** in the Actions column. On the log information page that appears, you can view cluster logs. To view detailed log information,, click **Stack events**.

    ![Cluster logs](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/6565359951/p9048.png)

-   On the Clusters page, find the created cluster and click **Manage** in the Actions column. You can click the **Basic Information** tab to view the basic information about the cluster and click the **Connection Information** tab to view the information about how to connect to the cluster.

    ![Basic cluster information](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/6565359951/p9049.png)

    The following information is displayed:

    -   **API Server Public Endpoint**: the IP address and port that the Kubernetes API Server uses to provide services over the Internet. It allows you to manage the cluster by using kubectl or other tools on the client.
    -   **API Server Internal Endpoint**: the IP address and port that the Kubernetes API Server uses to provide services within the cluster. The endpoint belongs to the SLB instance bound to the cluster. Three master nodes work as the backend servers of the SLB instance.
    -   **Testing Domain**: the domain name that is used for service testing. The suffix of the domain is `<cluster_id>.<region_id>.alicontainer.com`.

        **Note:** To rebind the domain name, click **Rebind Domain Name**.

-   You can use kubectl and run the `kubectl get node` command to connect to the cluster and view the information about the nodes in the cluster. For more information, see [Use kubectl to connect to a cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Manage and access clusters/Use kubectl to connect to a cluster.md).

![Nodes information](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/6565359951/p9050.png)


