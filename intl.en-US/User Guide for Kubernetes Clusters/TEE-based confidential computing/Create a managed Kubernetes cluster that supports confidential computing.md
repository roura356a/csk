---
keyword: [managed Kubernetes cluster that supports confidential computing, ECS Bare Metal instance, Intel \(R\) SGX application]
---

# Create a managed Kubernetes cluster that supports confidential computing

This topic describes how to create a managed Kubernetes cluster that supports confidential computing in the Container Service for Kubernetes \(ACK\) console.

ACK and Resource Access Management \(RAM\) are activated.

You can log on to the [ACK](https://cs.console.aliyun.com/) to activate ACK and the [RAM console](https://ram.console.aliyun.com/) to activate RAM.

**Note:**

Note the following limits when you use ACK:

-   SLB instances that are created together with the ACK cluster support only the pay-as-you-go billing method.
-   ACK clusters support only Virtual Private Cloud \(VPC\) networks.
-   Each account can consume only a limited amount of computing resources. You fail to create clusters if you do not have sufficient computing resources. When you create clusters, make sure that you have sufficient resources. To increase the quota of computing resources for your account, [submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).
    -   You can create up to 50 clusters across regions for each account. You can deploy up to 100 nodes in each cluster. To increase the quota of clusters or nodes for your account, [submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).

        **Note:** By default, you can add up to 48 route entries to each VPC network in an ACK cluster. This means that you can deploy up to 48 nodes in an ACK cluster that uses Flannel. An ACK cluster that uses Terway is not subject to this limit. To increase the quota of route entries for a VPC network, [submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).

    -   You can create up to 100 security groups under each account.
    -   You can create up to 60 pay-as-you-go SLB instances under each account.
    -   You can create up to 20 elastic IP addresses \(EIPs\) under each account.
-   To create a confidential computing cluster, you must set the parameters based on the information in the following table. Otherwise, you cannot run Intel \(R\) Software Guard Extensions \(SGX\) applications in the cluster.

    |Parameter|Description|
    |---------|-----------|
    |**Zone**|Only ECS Bare Metal instances of the ecs.ebmhfg5.2xlarge type support confidential computing clusters. Make sure that this instance type is available in the selected zone.|
    |**Container Runtime**|**Docker 18.09.2**|
    |**Instance Type**|Select ecs.ebmhfg5.2xlarge of the **ECS Bare Metal instance family**.|
    |**Operating System**|**AliyunLinux 2.xxxx**|
    |**Network Plug-in**|**Flannel**|
    |**Custom Image**|Do not use **custom images**. Otherwise, confidential computing may be unavailable.|


1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click **Create Kubernetes Cluster** in the upper-right corner of the page.

4.  In the Select Cluster Template dialog box, find Confidential Computing Cluster in the **Managed Clusters** section and click **Create**.

5.  On the **ACK managed edition** tab, configure the cluster.

    1.  Configure basic settings of the cluster.

        |Parameter|Description|
        |---------|-----------|
        |**Cluster Name**|Enter a name for the cluster of Container Service for Kubernetes \(ACK\).

**Note:** The name must be 1 to 63 characters in length, and can contain digits, letters, and hyphens \(-\). |
        |**Cluster specifications**|Select a cluster type. **Standard edition** and **Professional \(Preview\)** are supported. |
        |**Region**|Select a region to deploy the cluster. |
        |**Resource Group**|Move the pointer over **All Resources** at the top of the page and select the resource group to which the cluster belongs. The name of the selected resource group appears on the page.

![Resource Groups](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/9688404061/p127165.png) |
        |**Kubernetes Version**|Select a Kubernetes version. |
        |**Confidential Computing**|Select **Enable**.|
        |**Container Runtime**|You must select Docker.|
        |**VPC**|Select a Virtual Private Cloud \(VPC\) network to deploy the cluster. Shared VPC networks and standard VPC networks are supported.

        -   Shared VPC network: The owner of a VPC network \(resource owner\) can share VSwitches in the VPC network under the account of the owner with other accounts in the same organization.
        -   Standard VPC network: The owner of a VPC network \(resource owner\) cannot share VSwitches in the VPC network under the account of the owner with other accounts.
**Note:** ACK clusters support only VPC networks. You can select a VPC network from the drop-down list. If no VPC network is available, click **Create VPC** to create one. For more information, see [Create a VPC](/intl.en-US/VPCs and VSwitches/VPC management/Create a VPC.md). |
        |**VSwitch**|Select VSwitches.

You can select up to three VSwitches deployed in different **zones**. If no VSwitch is available, click **Create VSwitch** to create one. For more information, see [Create a VSwitch](/intl.en-US/VPCs and VSwitches/VSwitch management/Create a VSwitch.md). |
        |**Network Plug-in**|Confidential computing clusters support only the **Flannel** plug-in.|
        |**Pod CIDR Block**|If you select **Flannel**, you must set **Pod CIDR Block**.

The CIDR block specified by **Pod CIDR Block** cannot overlap with that of the VPC network or existing ACK clusters in the VPC network. The CIDR Block cannot be modified after it is specified. The Service CIDR block cannot overlap with the Pod CIDR block. For more information about subnetting for ACK clusters, see [Assign CIDR blocks to resources in a Kubernetes cluster under a VPC](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Assign CIDR blocks to resources in a Kubernetes cluster under a VPC.md). |
        |**Service CIDR**|Set **Service CIDR**. The CIDR block specified by **Service CIDR** cannot overlap with that of the VPC network or existing ACK clusters in the VPC network. The CIDR block cannot be modified after it is specified. The Service CIDR block cannot overlap with the Pod CIDR block. For more information about subnetting for ACK clusters, see [Assign CIDR blocks to resources in a Kubernetes cluster under a VPC](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Assign CIDR blocks to resources in a Kubernetes cluster under a VPC.md). |
        |**IP Addresses per Node**|If you select **Flannel**, you must set the number of **IP addresses per node**.

**Note:** **IP Addresses per Node** specifies the maximum number of IP addresses that can be assigned to each node. We recommend that you use the default value. |
        |**Configure SNAT**|By default, a cluster is not accessible over the Internet. If the VPC network that you select for the cluster cannot access the Internet, you can select **Configure SNAT for VPC**. Then, ACK creates a Network Address Translation \(NAT\) gateway and configures Source Network Address Translation \(SNAT\) entries to enable Internet access for the VPC network. |
        |**Public Access**|Specify whether to **expose the API server with an elastic IP address \(EIP\)**. |
        |**RDS Whitelist**|Set the Relational Database Service \(RDS\) whitelist. Add the IP addresses of cluster nodes to the RDS whitelist.

**Note:** To enable an RDS instance to access an ACK cluster, you must deploy the RDS instance in the same VPC network as the ACK cluster. |
        |**Security Group**|You can select **Create Basic Security Group**, **Create Advanced Security Group**, and **Select Existing Security Group**. For more information, see [Overview](/intl.en-US/Security/Security groups/Overview.md). |
        |**Secret Encryption**|Specify whether to enable **Key Management Service \(KMS\)** based on your requirements.|

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
        |**Custom Certificate SANs**|You can enter custom subject alternative names \(SANs\) for the API server of the cluster to accept requests from specified IP addresses or domains. |
        |**Service Account Token Volume Projection**|**Service account token volume projection** reduces security risks when pods use service accounts to access the API server. This feature enables kubelet to request and store the token on behalf of the pod. This feature also allows you to configure token properties, such as the audience and validity duration. For more information, see [Deploy service account token volume projection](/intl.en-US/User Guide for Kubernetes Clusters/Security management/Use service account token volume projection.md). |
        |**Deletion Protection**|Specify whether to enable deletion protection. If you select this check box, the cluster cannot be deleted in the console or by calling the API. This allows you to avoid user errors. |

6.  Click **Next:Worker Configurations** to configure worker nodes.

    1.  Select instances as worker nodes.

        **Note:** Only ECS Bare Metal instances can be used as worker nodes for confidential computing clusters. The instance type is ecs.ebmhfg5.2xlarge.

        -   If you select **Create Instance**, you must set the following parameters.

            |Parameter|Description|
            |---------|-----------|
            |**Billing Method**|The **pay-as-you-go** and **subscription** billing methods are supported. If you select **pay-as-you-go**, you must set the following parameters:

            -   **Duration**: You can select 1, 2, 3, or 6 months. If you require a longer duration, you can select 1 to 5 years.
            -   **Auto Renewal**: Specify whether to enable auto-renewal.
The **pay-as-you-go** and **subscription** billing methods are supported.|
            |**Instance Type**|Only **ECS Bare Metal** instances support confidential computing. The instance type ecs.ebmhfg5.2xlarge supports Intel \(R\) SGX applications.|
            |**Selected Types**|The selected instance types. You can select multiple instance types.|
            |**Quantity**|Specify the number of worker nodes to be created. |
            |**System Disk**|**ESSDs**, **SSDs**, and **ultra disks** are supported.

**Note:** You can select **Enable Backup** to back up disk data. |
            |**Mount Data Disk**|**ESSDs**, **SSDs**, and **ultra disks** are supported. You can enable disk **encryption** and **backup** when you add data disks. |
            |**Operating System**|Only the AliyunLinux 2.xxxx operating systems are supported.|
            |**Logon Type**|            -   Key Pair:
                -   **Key Pair**: Select an SSH key pair from the drop-down list.
                -   **create a key pair**: Create a key pair if no SSH key pair is available. For more information about how to create an SSH key pair, see [Create an SSH key pair](/intl.en-US/Security/Key pairs/Use an SSH key pair/Create an SSH key pair.md). After the key pair is created, set it as the credential for logging on to the cluster.
            -   Password:
                -   **Password**: Enter a password for logging on to the nodes.
                -   **Confirm Password**: Enter the password again. |
            |**Key Pair**|

        -   If you select **Add Existing Instance**, you must add at least two worker nodes. You must also set **Duration**, **Auto Renewal**, **Operating System**, **Logon Type**, and **Key Pair** based on the preceding settings.
    2.  Configure advanced settings of the worker nodes

        |Parameter|Description|
        |---------|-----------|
        |**Node Protection**|Specify whether to enable node protection.

**Note:** By default, this check box is selected. This way, cluster nodes cannot be deleted in the console or by calling the API. This allows you to avoid user errors. |
        |**User Data**|You can enter custom scripts. Custom scripts are automatically run after the nodes are initialized. **Note:** Nodes that run Windows support Batch and PowerShell scripts. Before you encode the content in Base64, make sure that the first line includes \[bat\] or \[powershell\]. Nodes that run Linux support shell scripts. For more information about the supported formats, see [cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/format.html) and [Prepare user data](/intl.en-US/Instance/Manage instances/User data/Prepare user data.md).

If your script file is larger than 1 KB, we recommend that you upload the script to an Object Storage Service \(OSS\) bucket and pull the script from the internal endpoint of the OSS bucket. |
        |**Custom Image**|**Note:** Do not use custom images. Otherwise, confidential computing may be unavailable. |
        |**Custom Node Name**|Specify whether to use a **custom node name**.

A node name consists of a prefix, an IP substring, and a suffix.

        -   Both the prefix and suffix can contain one or more parts that are separated with periods \(**.**\) and must start and end with a lowercase letter or digit. These parts can contain lowercase letters, digits, and hyphens \(-\), and must start and end with a lowercase letter or digit.
        -   The IP substring length specifies the number of digits to be truncated from the end of the returned node IP address. Valid values: 5 to 12. |
        |**CPU Policy**|Set the CPU policy.

        -   none: This policy indicates that the default CPU affinity is used. This is the default policy.
        -   static: This policy allows pods with certain resource characteristics on the node to be granted with enhanced CPU affinity and exclusivity. |
        |**Taints**|Add taints to worker nodes in the cluster. |

7.  Click **Next:Component Configurations** to configure components.

    |Parameter|Description|
    |---------|-----------|
    |**Ingress**|Specify whether to install Ingress controllers. By default, **Install Ingress Controllers** is selected. For more information, see [Configure an Ingress](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Ingress management/Configure an Ingress.md).

**Note:** If you want to **create Ingress dashboard**, you must first activate Log Service. |
    |**Cloud Monitor Agent**|Select whether to install the Cloud Monitor agent. After the Cloud Monitor agent is installed on ECS instances, you can view monitoring information about the instances in the Cloud Monitor console.|
    |**Log Service**|Specify whether to activate Log Service. You can select an existing Log Service project or create a Log Service project.

By default, **Enable Log Service** is selected. When you create an application, you can activate Log Service through a few steps. For more information, see [Use Log Service to collect container logs](/intl.en-US/User Guide for Kubernetes Clusters/Log management/Use Log Service to collect container logs.md). |
    |**Workflow Engine**|Specify whether to activate Alibaba Cloud Genomics Compute Service \(AGS\).

    -   If you select this check box, the system automatically installs the AGS workflow plug-in when it creates the cluster.
    -   If you clear this check box, you must manually install the AGS workflow plug-in. For more information, see [Introduction to AGS CLI](/intl.en-US/User Guide for Genomics Service/AGS workflow/Introduction to AGS CLI.md). |

8.  Click **Next:Confirm Order** to confirm the cluster configurations and select **terms of service**.

9.  Click **Create Cluster** to start the deployment.

    You can also view logs that record cluster creation events.

    **Note:** It takes about 10 minutes to create an ACK cluster that consists of multiple nodes.


-   After you create the cluster, you can view the created cluster on the Clusters page in the console.

    ![Clusters](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/6765359951/p77641.png)

-   Click **View Logs** in the **Actions** column. On the Log information page, you can view cluster logs. To view detailed log information, click **Stack events**.

    ![Cluster logs](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/6765359951/p77637.png)

-   On the Clusters page, find the created cluster and click **Details** in the Actions column. You can click the **Basic Information** tab to view the basic information about the cluster and click the **Connection Information** tab to view the information about how to connect to the cluster.

    The following information is displayed.

    -   **API Server Public Endpoint**: the IP address and port that the Kubernetes API Server uses to provide services over the Internet. It allows you to manage the cluster by using kubectl or other tools on the client.

        **Bind EIP** and **Unbind EIP**: These options are available to only managed Kubernetes clusters.

        -   Bind EIP: You can select an existing EIP or create one.

            The API server restarts after you bind an EIP to the API server. We recommend that you do not perform operations on the cluster during the restart process.

        -   Unbind EIP: You cannot access the API server over the Internet after you unbind the EIP.

            The API server restarts after you unbind the EIP from the API server. We recommend that you do not perform operations on the cluster during the restart process.

    -   **API Server Internal Endpoint**: the IP address and port that the Kubernetes API server uses to provide services within the cluster.
    -   **Testing Domain**: the domain name that is used for service testing. The suffix of the domain is `<cluster_id>.<region_id>.alicontainer.com`.

        **Note:** To rebind the domain name, click **Rebind Domain Name**.

-   You can use kubectl and run the `kubectl get node` command to connect to the cluster and view the information about the nodes in the cluster. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md).

    ![View node information](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/6765359951/p77634.png)


**Related topics**  


[TEE-based confidential computing](/intl.en-US/User Guide for Kubernetes Clusters/TEE-based confidential computing/TEE-based confidential computing.md)

[Use the Intel SGX SDK to develop and build an application](/intl.en-US/User Guide for Kubernetes Clusters/TEE-based confidential computing/Use the Intel SGX SDK to develop and build an application.md)

