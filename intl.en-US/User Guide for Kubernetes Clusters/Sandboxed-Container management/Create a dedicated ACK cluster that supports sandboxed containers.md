# Create a dedicated ACK cluster that supports sandboxed containers

This topic describes how to create an Alibaba Cloud Container Service for Kubernetes \(ACK\) cluster that supports sandboxed containers in the ACK console.

[Enable Container Service for Kubernetes](/intl.en-US/Quick Start/Enable Container Service for Kubernetes.md)

## Limits

-   SLB instances that are created along with the cluster support only the pay-as-you-go billing method.
-   Kubernetes clusters support only Virtual Private Cloud \(VPC\) networks.
-   Each account can consume a limited amount of computing resources. If you do not have sufficient computing resources, you cannot create clusters. When you create clusters, make sure that you have sufficient resources. To increase the quota of computing resources for your account, submit a ticket.
    -   You can create up to five clusters across regions for each account. You can deploy up to 40 nodes in each cluster. To increase the quota of clusters or nodes, [submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).

        **Note:** In an ACK cluster, you can create up to 48 route entries for each Virtual Private Network \(VPC\) network. This means that you can configure up to 48 route entries for ACK clusters deployed in a VPC network. To increase the quota of route entries for a VPC network, [submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).

    -   You can create up to 100 security groups for each account.
    -   You can create up to 60 pay-as-you-go SLB instances for each account.
    -   You can create up to 20 Elastic IP addresses for each account.
-   To create an ACK cluster that supports sandboxed containers, you must set the parameters as required in the following table.

    |Parameter|Description|
    |---------|-----------|
    |Zone|Select a zone where Elastics Compute Service \(ECS\) Bare Metal instances are deployed. Sandboxed containers support only ECS Bare Metal instances.|
    |Kubernetes Version|Select 1.14.6-aliyun.1 or later.|
    |Container Runtime|Select Sandboxed-Container.|
    |Worker Instance|Add worker nodes by creating new ECS instances.|
    |Billing Method|Select the subscription billing method.|
    |Instance Type|Select ECS Bare Metal instances.|
    |Mount Data Disk|Mount a data disk of 200 GiB at least. We recommend that you mount a data disk of 1 TB or larger.|
    |Operating System|By default, the AliyunLinux operating system is used. You cannot not change the operating system.|


## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click **Create Kubernetes Cluster**. On the Select Cluster Template page, select **Standard Dedicated Cluster**. Click **create** to go to the cluster configurations page.

4.  Configure the cluster.

    1.  Configure basic settings.

        |Parameter|Description|
        |---------|-----------|
        |**Cluster Name**|Enter the name of the cluster. **Note:** The name must be 1 to 63 characters in length and can contain digits, letters, and hyphens \(-\). |
        |**Region**|Select the region where the cluster is deployed.|
        |**Resource Group**|Move the pointer over **Account's all Resources** at the top of the page and select the resource group where the cluster is deployed. The name of the selected resource group is displayed.|
        |**Kubernetes Version**|Select 1.14.6-aliyun.1 or a later version.|
        |**Container Runtime**|Select **Sandboxed-Container**.|
        |**VPC**|Set a Virtual Private Cloud \(VPC\) network for the cluster. **Note:** Kubernetes clusters support only VPC networks. Select a VPC network from the drop-down list. If no VPC network is available, click **Create VPC**. For more information, see [Create a VPC](/intl.en-US/VPCs and VSwitches/VPC management/Create a VPC.md). |
        |**VSwitch**|Select one or more VSwitches for the cluster. You can select up to three VSwitches deployed in different **zones**. If no VSwitch is available, click **Create VSwitch**. For more information, see [t2436.md\#](/intl.en-US/VPCs and VSwitches/VSwitch management/Create a VSwitch.md). |
        |**Network plug-in**|Select the network plug-in. Both Flannel and Terway are supported. For more information, see [Flannel and Terway](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Use Terway.md).         -   Flannel: a simple and stable Container Network Interface \(CNI\) plug-in developed by the Kubernetes community. Flannel does not support standard Kubernetes network policies.
        -   Terway: a network plug-in developed by Alibaba Cloud Container Service. Terway allows you to assign Alibaba Cloud Elastic Network Interfaces \(ENIs\) to containers. It also allows you to customize network policies of Kubernetes to control intercommunication among containers, and implement bandwidth throttling on individual containers.
            -   The number of pods that can be deployed in a node depends on the number of ENIs that are attached to the node and the maximum number of secondary IP addresses provided by these ENIs.
            -   If you select **Terway**, an ENI is shared among multiple pods. A secondary IP address of the ENI is assigned to each pod. |
        |**Pod CIDR Block**|If you select **Flannel**, you must specify **Pod CIDR Block**. The CIDR blocks specified by **Pod CIDR Block** cannot overlap with that of the VPC network or existing ACK clusters in the VPC network. The CIDR Block cannot be modified after it is specified. The Service CIDR block cannot overlap with the Pod CIDR block. For more information about network segmentation of ACK clusters, see [Plan Kubernetes CIDR blocks under a VPC](/intl.en-US/Best Practices/Cluster/Plan Kubernetes CIDR blocks under a VPC.md). |
        |**Pod VSwitch**|If you select **Terway**, you must select **Pod VSwitch**. **Pod VSwitch** specifies a VSwitch for the pods if you select Terway as the network plug-in. ENIs must be deployed in the zone where the ENI devices are located. Therefore, you must select another VSwitch for the pods in the cluster. When the cluster provides services, the VSwitch assigns IP addresses to the pods. To ensure that there are sufficient IP addresses for all pods, we recommend that you set the mask length of the CIDR block to a value no greater than 19 for the VSwitch.|
        |**Service CIDR**|Specify **Service CIDR**. The CIDR block specified by **Service CIDR** cannot overlap with that of the VPC network or existing ACK clusters in the VPC network. The CIDR Block cannot be modified after it is specified. The Service CIDR block cannot overlap with the Pod CIDR block. For more information about network segmentation of ACK clusters, see[Plan Kubernetes CIDR blocks under a VPC](/intl.en-US/Best Practices/Cluster/Plan Kubernetes CIDR blocks under a VPC.md).|
        |**IP Addresses per Node**|If you select **Flannel**, you must select the number of **IP addresses per node**. **Note:** **IP Addresses per Node** specifies the maximum number of IP addresses that can be assigned to each node. We recommend that you use the default value. |
        |**Configure SNAT**|Specify whether to configure Source Network Address Translation \(SNAT\) rules for the VPC network.         -   ACK automatically selects an existing NAT gateway in the VPC network.
        -   If the VPC network does not have a NAT gateway, the system automatically creates one. If you do not need the system to create a NAT gateway, clear the **Configure SNAT for VPC** check box. Then, you must manually create a NAT gateway or configure SNAT rules to enable instances in the VPC network to access the Internet. Otherwise, the system fails to create the ACK cluster. |
        |**Public Access**|Specify whether to **expose API server with EIP**. The ACK API Server provides multiple HTTP-based RESTful APIs, which can be used to create, delete, modify, query, and monitor resources such as pods and services.

        -   If you select this check box, an Elastic IP address is created and attached to an internal SLB instance. Port 6443 used by the API Server is opened on master nodes. You can connect and manage the cluster by using kubeconfig over the Internet.
        -   If you clear this check box, no Elastic IP address is created. You can only connect to and manage the cluster through kubeconfig from within the VPC network. |
        |**SSH Logon**|To enable SSH logon, you must first select the **Expose API Server with EIP** check box.

        -   If you select this check box, you can use SSH to access the cluster.
        -   If you clear this check box, you cannot use SSH or kubectl to connect to the cluster. If you want to access an Elastic Compute Service \(ECS\) instance in the cluster through SSH, you must manually attach an EIastic IP address to the instance and configure security group rules to open SSH port 22. For more information, see[t16642.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Manage and access clusters/Use SSH to connect to a cluster.md). |
        |**RDS Whitelist**|Configure the RDS whitelist of the cluster. Add the IP addresses of nodes to the RDS whitelist.|
        |**Security Group**|You can **create basic security groups**, **advanced security groups**, or **select existing security groups**. For more information about security groups, see [Overview](/intl.en-US/Security/Security groups/Overview.md). |

    2.  Configure advanced settings.

        |Parameter|Description|
        |---------|-----------|
        |**Kube-proxy Mode**|iptables and IPVS are supported.

        -   iptables is a kube-proxy mode. It uses iptables rules to conduct service discovery and load balancing. The performance of this mode is restricted by the size of the cluster. This mode is suitable for clusters that run a small number of services.
        -   IPVS is a high-performance kube-proxy mode. It uses Linux Virtual Server \(LVS\) to conduct service discovery and load balancing. This mode is suitable for clusters that run a large number of services. We recommend that you use this mode in scenarios where high-performance load balancing is required. |
        |**Labels**|Attach labels to the nodes. Enter keys and values, and click **Add**.

**Note:**

        -   Key is required. Value is optional.
        -   Keys are not case-sensitive. A key must be 1 to 64 characters in length and cannot start with aliyun, http://, or https://.
        -   Values are not case-sensitive. A value must be 1 to 128 characters in length and cannot start with http:// or https://.
        -   The keys of labels attached to the same resource must be unique. If you add a label with a used key, the label overwrites the one that uses the same key.
        -   You can attach up to 20 labels to each resource. If you attach more than 20 labels to a resource, all labels become invalid. You must detach unused labels for the remaining labels to take effect. |
        |**Cluster Domain**|Enter the cluster domain. **Note:** The default cluster domain is **cluster.local**. Custom domains are supported. A domain consists of two parts. Each part must be 1 to 63 characters in length and can contain only letters and digits. |
        |**Custom Certificate SANs**|You can enter custom subject alternative names \(SANs\) for the API servers of the cluster. Separate multiple IP addresses or domains with commas \(,\). |
        |**Service Account Token Volume Projection**|Enable **Service Account Token Volume Projection** to enhance security when you use service accounts. For more information, see [Deploy service account token volume projection](/intl.en-US/User Guide for Kubernetes Clusters/Security management/Use service account token volume projection.md). |
        |**Cluster CA**|If you select this check box, upload a CA certificate for the ACK cluster to protect data transmission between the server and client.|
        |**Deletion Protection**|Specify whether to enable Deletion Protection. If you select this check box, the cluster cannot be deleted in the console or by API operations. This allows you to avoid user errors.|

5.  Click **Next:Master Configurations** to configure master nodes.

    |Parameter|Description|
    |---------|-----------|
    |**Billing Method**|Select **Subscription**. **Note:** Worker instances support only ECS Bare Metal instances. ECS Bare Metal instances support only the **Subscription** billing method. The **pay-as-you-go** billing method is not supported by ECS Bare Metal instances. |
    |**Duration**|Select the duration of the subscription.|
    |**Auto Renewal**|Specify whether to enable **Auto Renewal**.|
    |**Master Node Quantity**|Select the number of master nodes. You can create three or five master nodes.|
    |**Instance Type**|Select the instance type of the master nodes. For more information, see[Instance families](/intl.en-US/Instance/Instance families.md) .|
    |**System Disk**|By default, system disks are mounted to master nodes. **SSDs** and **ultra disks** are supported. **Note:** You can select the **Enable Backup** check box to back up disk data. |

6.  Click **Next:Worker Configurations** to configure worker nodes.

    1.  Configure basic settings for worker nodes.

        |Parameter|Description|
        |---------|-----------|
        |**Worker Instance**|By default, the **Create Instance** check box is selected. You cannot select the **Add Existing Instance** check box.|
        |**Instance Type**|Select **ECS Bare Metal Instance**. Only ECS Bare Metal instances are supported.|
        |**Selected Types**|The selected instance types are displayed. You can select only ECS Bare Metal Instance as the instance type.|
        |**Quantity**|Set the number of worker nodes.|
        |**System Disk**|**Enhanced SSDs**, **SSDs**, and **ultra disks** are supported. **Note:** You can select the **Enable Backup** check box to back up disk data. |
        |**Mount Data Disk**|**Enhanced SSDs**, **SSDs**, and **ultra disks** are supported. You can enable **disk encryption** and **data backup** when you select disks.

**Notice:** The data disk is used to store the root file system of all containers on the node. Therefore, you must mount a data disk of 200 GiB at least. We recommend that you mount a data disk of 1 TB or larger. |
        |**Operating System**|By default, the AliyunLinux operating system is used. You cannot change the operating system.|
        |**Logon Type**|        -   Key Pair:

If you want to use a key pair, click **create a key pair** to create one in the ECS console. For more information, see [Create an SSH key pair](/intl.en-US/Security/Key pairs/Use an SSH key pair/Create an SSH key pair.md). After the key pair is created, set it as the credential for logging on to the cluster.

        -   Password:
            -   **Password**: Enter the password.
            -   **Confirm Password**: Enter the password again. |
        |**Key Pair**|

    2.  Configure advanced settings.

        |Parameter|Description|
        |---------|-----------|
        |**Node Protection**|Specify whether to enable Node Protection. **Note:** By default, this check box is selected. Cluster nodes cannot be deleted in the console or by using API operations. This avoids user errors. |
        |**User Data**|For more information, see [Prepare user data](/intl.en-US/Instance/Manage instances/User data/Prepare user data.md).|
        |**Custom Node Name**|Specify whether to enable **Custom Node Name**. A node name consists of a prefix, an IP substring, and a suffix.

        -   Both the prefix and suffix can contain one or more parts that are separated with periods \(**.**\). Both the prefix and suffix can contain one or more parts that are separated with periods \(**-**\), and must start and end with a lowercase letter or digit.
        -   The IP substring length specifies the number of digits to be truncated from the end of the node IP address. Valid values: 5 to 12.
For example, if the node IP address is 192.168.0.55, the prefix is aliyun.com, the IP substring length is 5, and the suffix is test, the node name will be aliyun.com00055test. |
        |**Node Port Range**|Specify the node port range.|
        |**CPU Policy**|Select the CPU policy.         -   none: This is the default policy. This indicates that the default CPU affinity is used.
        -   static: This policy allows pods with certain resource characteristics to be granted with enhanced CPU affinity and exclusivity on the node. |
        |**Taints**|Add taints to worker nodes.|

7.  Click **Next:Component Configurations** to configure components.

    |Parameter|Description|
    |---------|-----------|
    |**Ingress**|Specify whether to install Ingress controllers. By default, the **Install Ingress Controllers** check box is selected. For more information, see [Configure an Ingress](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Ingress management/Configure an Ingress.md).|
    |**Volume Plug-in**|Select **CSI**. Only CSI is supported by ACK clusters that support sandboxed containers. ACK clusters can be automatically bound with disks, Network Attached Storage \(NAS\), and Object Storage Service \(OSS\) provided by Alibaba Cloud through pods. For more information, see [Storage management-CSI](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/Overview.md).|
    |**Monitoring Agents**|Specify whether to install the CloudMonitor agent. **After the CloudMonitor agent is installed on ECS nodes**, you can view monitoring information about the nodes in the CloudMonitor console. **Prometheus monitoring service** is available for users on the whitelist. |
    |**Log Service**|Specify whether to enable Log Service. You can select an existing project or create a new project.

If you select the **Enable Log Service** check box, the Log Service plug-in is automatically installed in the cluster. For more information about how to set up Log Service when you create an application, see [Use Log Service to collect Kubernetes logs](/intl.en-US/User Guide for Kubernetes Clusters/Log management/Use Log Service to collect Kubernetes cluster logs.md).

If you select the **Enable Log Service** check box, you can specify whether to **create an Ingress Dashboard** or **create an Event Center** in the Log Service console. |
    |**Workflow Engine**|Specify whether to enable Alibaba Cloud Genomics Compute Service \(AGS\).     -   If you select this check box, the system automatically installs the AGS workflow plug-in during cluster creation.
    -   If you clear this check box, you must manually install the AGS workflow plug-in. For more information, see [Introduction to AGS CLI](/intl.en-US/User Guide for Genomics Service/AGS workflow/Introduction to AGS CLI.md). |

8.  Click Next:Confirm Order.

9.  Select **Terms of Service** and click **Create Cluster**.

    **Note:** It takes approximately 10 minutes for the system to create a Kubernetes cluster that consists of multiple nodes.


-   After the cluster is created, you can find the created cluster on the Clusters page in the console.

    ![Clusters](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/3565359951/p62541.png)

-   Click **View Logs** in the Actions column. On the Log Information page, you can view cluster logs. To view log details, click **Stack events**.

    ![Cluster logs](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/3565359951/p21426.png)

-   On the Clusters page, find the newly created cluster and click **Manage** in the Actions column. On the page that appears, you can view basic information about the cluster and corresponding connections.

    ![Basic cluster information](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/3565359951/p21427.png)

    The following information is displayed:

    -   **API Server Public Endpoint**: the IP address and port that the Kubernetes API Server uses to provide services over the Internet. It allows you to manage the cluster by using kubectl or other tools on the client.
    -   **API Server Internal Endpoint**: the IP address and port that the Kubernetes API Server uses to provide services within the cluster. The endpoint belongs to the SLB instance bound to the cluster.
    -   **Pod CIDR Block**: the CIDR block of the pods in the cluster.
    -   **Service CIDR**: the CIDR block assigned to services in the cluster for users to access the services over the Internet.
    -   **Testing Domain**: the domain that is used for service tests. The suffix of the domain is `<cluster_id>.<region_id>.alicontainer.com`.
    -   **Kube-proxy Mode**: the proxy mode that is used to conduct service discovery and load balancing. iptables and IPVS modes are supported.
    -   **Pods on Each Node**: the maximum number of pods that can run on each node. Default value: 128.
-   You can use kubectl and run the `kubectl get node` command to connect to the cluster and view the information about the nodes in the cluster. For more information, see [Use kubectl to connect to a cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Manage and access clusters/Use kubectl to connect to a cluster.md).

    ![1](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0170510061/p166525.png)


