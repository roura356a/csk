---
keyword: [managed Kubernetes cluster for confidential computing, Intel SGX 2.0 applications]
---

# Create a managed Kubernetes cluster for confidential computing

This topic describes how to create a managed Kubernetes cluster for confidential computing in the Container Service for Kubernetes \(ACK\) console. Managed Kubernetes clusters for confidential computing run in Trusted Execution Environments \(TEEs\).

ACK and Resource Access Management \(RAM\) are activated.

ACK is activated in the [ACK console](https://cs.console.aliyun.com/). RAM is activated in the [RAM console](https://ram.console.aliyun.com/).

**Note:**

When you create a managed Kubernetes cluster for confidential computing, take note of the following limits:

-   Server Load Balancer \(SLB\) instances that are created along with an ACK cluster support only the pay-as-you-go billing method.
-   ACK clusters support only VPCs.
-   By default, each account has specific quotas on cloud resources that can be created. You cannot create clusters if the quota is reached. Make sure that you have sufficient quotas before you create a cluster. To increase quotas,[submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).
    -   By default, you can create up to 50 clusters across all regions with each account. Each cluster can contain up to 100 nodes. To increase the quota of clusters or nodes,[submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).

        By default, you can add up to 48 route entries to a virtual private cloud \(VPC\). This means that you can deploy up to 48 nodes in an ACK cluster that uses Flannel. An ACK cluster that uses Terway is not subject to this limit. To deploy more nodes in this case,[submit a ticket](https://workorder-intl.console.aliyun.com/console.htm) to apply for an increase on the quota of route entries in the VPC that you want to use.

    -   By default, you can create up to 100 security groups with each account.
    -   By default, you can create up to 60 pay-as-you-go SLB instances with each account.
    -   By default, you can create up to 20 elastic IP addresses \(EIPs\) with each account.
-   To create a managed Kubernetes cluster for confidential computing, you must set the parameters as described in the following table. Otherwise, you cannot run Intel Software Guard Extensions \(SGX\) applications in the cluster.

    |Parameter|Description|
    |---------|-----------|
    |**Zone**|Only Elastic Compute Service \(ECS\) instances of the **c7t security-enhanced compute optimized** **instance family** support clusters for confidential computing. Make sure that these instances are available in the selected zone.|
    |**Container Runtime**|Select containerd 1.4.4 or later.|
    |**Instance Type**|You must select ECS instances of the **c7t security-enhanced compute optimized** **instance family**. **Note:**

    -   The **security-enhanced compute optimized** instance family is in private preview. This instance family provides a limited stock of ECS instances and is not covered by terms of service level agreement \(SLA\). If you want to use ECS instances of this instance family, apply for ECS instances based on your minimum requirement.
    -   Intel Ice Lake supports the remote attestation service only based on Intel Software Guard Extensions Data Center Attestation Primitives \(SGX DCAP\). Remote attestation services based on Intel Enhanced Privacy Identification \(EPID\) are not supported. You must adapt your applications before you can use the remote attestation service. For more information about the remote attestation service, see [attestation-services](https://software.intel.com/content/www/us/en/develop/topics/software-guard-extensions/attestation-services.html). |
    |**Operating System**|Select **AliyunLinux 2.xxxx 64-bit \(UEFI\)**|
    |**Network Plug-in**|Select **Flannel**.|


1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  In the upper-right corner of the Clusters page, click **Cluster Template**.

4.  In the Select Cluster Template dialog box, find Confidential Computing Cluster in the **Managed Clusters** section and click **Create**.

5.  On the **Managed Kubernetes** tab, configure the cluster.

    1.  Configure basic settings for the cluster.

        |Parameter|Description|
        |---------|-----------|
        |**Cluster Name**|Enter a name for the ACK cluster.

**Note:** The name must be 1 to 63 characters in length, and can contain digits, letters, and hyphens \(-\). |
        |**Cluster Specification**|Select a cluster type. You can select **Standard edition** or **Professional**. |
        |**Region**|Select a region to deploy the cluster. |
        |**All Resources**|Move the pointer over **All Resources** at the top of the page and select the resource group that you want to use. After you select a resource group, virtual private clouds \(VPCs\) and vSwitches are filtered based on the selected resource group. When you create a cluster, only the VPCs and vSwitches that belong to the selected resource group are displayed in the console.

![Resource Groups](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/9150488261/p127165.png) |
        |**Kubernetes Version**|The Kubernetes versions that are supported by ACK. |
        |**Confidential Computing**|Select **Enable**.|
        |**Container Runtime**|Only the containerd runtime is supported. For more information, see [Comparison of Docker, containerd, and Sandboxed-Container](/intl.en-US/User Guide for Kubernetes Clusters/Sandboxed-Container management/Comparison of Docker, containerd, and Sandboxed-Container.md). |
        |**VPC**|Select a VPC to deploy the cluster. Standard VPCs and shared VPCs are supported.

        -   Shared VPC: The owner of a VPC \(resource owner\) can share the vSwitches in the VPC with other accounts in the same organization.
        -   Standard VPC: The owner of a VPC \(resource owner\) cannot share the vSwitches in the VPC with other accounts.
**Note:** ACK clusters support only VPCs. You can select a VPC from the drop-down list. If no VPC is available, click **Create VPC** to create one. For more information, see [Create a VPC](/intl.en-US/VPCs and vSwitchs/Work with VPCs.md). |
        |**VSwitch**|Select vSwitches.

You can select up to three vSwitches that are deployed in different **zones**. If no vSwitch is available, click **Create VSwitch** to create one. For more information, see [Create a vSwitch](/intl.en-US/VPCs and vSwitchs/Work with vSwitches.md). |
        |**Network Plug-in**|You must select the **Flannel** plug-in if you want to enable confidential computing.|
        |**Pod CIDR Block**|If you select **Flannel** as the network plug-in, you must set **Pod CIDR Block**.

The CIDR block specified by **Pod CIDR Block** cannot overlap with that of the VPC or those of the existing clusters in the VPC. The CIDR block cannot be modified after the cluster is created. The Service CIDR block cannot overlap with the pod CIDR block. For more information about subnetting for ACK clusters, see [Plan CIDR blocks for an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Network/Plan CIDR blocks for an ACK cluster.md). |
        |**Service CIDR**|Set **Service CIDR**. The CIDR block specified by **Service CIDR** cannot overlap with that of the VPC or those of the existing clusters in the VPC. The CIDR block cannot be modified after the cluster is created. The Service CIDR block cannot overlap with the pod CIDR block. For more information about subnetting for ACK clusters, see [Plan CIDR blocks for an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Network/Plan CIDR blocks for an ACK cluster.md). |
        |**IP Addresses per Node**|If you select **Flannel** as the network plug-in, you must set **IP Addresses per Node**.

**Note:**

        -   **IP Addresses per Node** specifies the maximum number of IP addresses that can be assigned to each node. We recommend that you use the default value.
        -   After you select the VPC and specify the number of IP addresses per node, recommended values are automatically generated for Pod CIDR block and Service CIDR block. The system also provides the maximum number of nodes that can be deployed in the cluster and the maximum number of pods that can be deployed on each node. You can modify the values based on your business requirements. |
        |**Configure SNAT**|By default, an ACK cluster cannot access the Internet. If the VPC that you select for the ACK cluster cannot access the Internet, you can select **Configure SNAT for VPC**. This way, ACK will create a NAT gateway and configure Source Network Address Translation \(SNAT\) rules to enable Internet access for the VPC. |
        |**Access to API Server**|By default, an internal-facing Server Load Balancer \(SLB\) instance is created for the cluster API server. You can modify the specification of the SLB instance. For more information, see [Instance specifications](/intl.en-US/Classic Load Balancer/User Guide/Instance/SLB instance overview.md).

**Note:** If you delete the SLB instance, you cannot access the cluster API server.

Select or clear **Expose API Server with EIP**. The ACK API server provides multiple HTTP-based RESTful APIs, which can be used to create, delete, modify, query, and monitor resources, such as pods and Services.

        -   If you select this check box, an elastic IP address \(EIP\) is created and associated with an Internet-facing SLB instance. Port 6443 used by the API server is opened on master nodes. You can connect to and manage the ACK cluster by using kubeconfig over the Internet.
        -   If you clear this check box, no EIP is created. You can connect to and manage the ACK cluster by using kubeconfig only within the VPC. |
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
        |**Cluster Domain**|Set the domain name of the cluster.

**Note:** The default domain name is **cluster.local**. You can enter a custom domain name. A domain name consists of two parts. Each part must be 1 to 63 characters in length and can contain only letters and digits. You cannot leave these parts empty. |
        |**Custom Certificate SANs**|You can enter custom subject alternative names \(SANs\) for the API server certificate of the cluster to accept requests from specified IP addresses or domain names.

For more information, see [Customize the SAN of the API server certificate for a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Security management/Infrastructure security/Customize the SAN of the API server certificate for a managed Kubernetes cluster.md). |
        |**Service Account Token Volume Projection**|**Service account token volume projection** reduces security risks when pods use service accounts to access the API server. This feature enables kubelet to request and store the token on behalf of the pod. This feature also allows you to configure token properties, such as the audience and validity duration. For more information, see [Enable service account token volume projection](/intl.en-US/User Guide for Kubernetes Clusters/Security management/Infrastructure security/Enable service account token volume projection.md). |
        |**Secret Encryption**|Select or clear **Select Key** based on your requirements.|

6.  Click **Next:Worker Configurations** to configure worker nodes.

    1.  Select instances as worker nodes.

        **Note:** You can select only ECS instances of the **c7t security-enhanced compute optimized** **instance family**. This enables the managed Kubernetes cluster for confidential computing to run Intel SGX 2.0 applications.

        -   If you select **Create Instance**, you must set the parameters that are listed in the following table.

            |Parameter|Description|
            |---------|-----------|
            |**Billing Method**|The **pay-as-you-go** and **subscription** billing methods are supported. If you select the **subscription** billing method, you must set the following parameters:

            -   **Duration**: You can select 1, 2, 3, or 6 months. If you require a longer duration, you can select 1 to 5 years.
            -   **Auto Renewal**: Specify whether to enable auto-renewal.
The following billing methods are supported: **Pay-As-You-Go** and **Subscription**.|
            |**Instance Type**|Only ECS instances of the **c7t security-enhanced compute optimized** **instance family** can run Intel SGX 2.0 applications.|
            |**Selected Types**|The selected instance types. You can select multiple instance types.|
            |**Quantity**|Specify the number of worker nodes \(ECS instances\) to be created. |
            |**System Disk**|**Enhanced SSDs**, **standard SSDs**, and **ultra disks** are supported.

**Note:**

            -   You can select **Enable Backup** to back up disk data.
            -   If you select **enhanced SSD** as the system disk type, you can set a custom **performance level** for the system disk.

You can select higher performance levels for enhanced SSDs with larger storage capacities. For example, you can select performance level 2 for an enhanced SSD with a storage capacity of more than 460 GiB. You can select performance level 3 for an enhanced SSD with a storage capacity of more than 1,260 GiB. For more information, see [Capacity and PLs](/intl.en-US/Block Storage/Block Storage overview/ESSDs.md). |
            |**Mount Data Disk**|**Enhanced SSDs**, **standard SSDs**, and **ultra disks** are supported. You can enable **disk encryption** and **disk backup** when you mount a data disk. |
            |**Operating System**|Only the AliyunLinux 2.xxxx 64-bit \(UEFI\) operating systems are supported.|
            |**Logon Type**|            -   Key pair logon
                -   **Key Pair**: Select an SSH key pair from the drop-down list.
                -   **create a key pair**: Create an SSH key pair if none is available. For more information about how to create an SSH key pair, see [Create an SSH key pair](/intl.en-US/Security/Key pairs/Use an SSH key pair/Create an SSH key pair.md). After the key pair is created, set it as the credential that is used to log on to the cluster.
            -   Password logon
                -   **Password**: Enter the password that is used to log on to the nodes.
                -   **Confirm Password**: Enter the password again. |
            |**Key Pair**|

        -   If you select **Add Existing Instance**, you must add at least two worker nodes. You must also set **Duration**, **Auto Renewal**, **Operating System**, **Logon Type**, and **Key Pair** based on the preceding settings.
    2.  Configure advanced settings of the worker nodes.

        |Parameter|Description|
        |---------|-----------|
        |**Node Protection**|Specify whether to enable node protection.

**Note:** By default, this check box is selected. Node protection prevents nodes from being accidentally deleted in the console or by calling the API. This prevents user errors. |
        |**User Data**|You can enter custom scripts. Custom scripts are automatically executed after the nodes are initialized. **Note:** Windows nodes support Batch and PowerShell scripts. Before you encode the content in Base64, make sure that the first line includes \[bat\] or \[powershell\]. Linux nodes support shell scripts. For more information about the supported formats, see [cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/format.html) and [Overview of ECS instance user data](/intl.en-US/Instance/Manage instance configurations/Manage instance user data/Overview of ECS instance user data.md).

If your script file is larger than 1 KB, we recommend that you upload the script to an Object Storage Service \(OSS\) bucket and pull the script from the internal endpoint of the OSS bucket. |
        |**Custom Image**|**Note:** Do not use custom images. Custom images may not support confidential computing. |
        |**Custom Node Name**|Specify whether to use a **custom node name**.

A node name consists of a prefix, an IP substring, and a suffix.

        -   Both the prefix and suffix can contain one or more parts that are separated by periods \(**.**\).These parts can contain lowercase letters, digits, and hyphens \(-\), and must start and end with a lowercase letter or digit.
        -   The IP substring length specifies the number of digits to be truncated from the end of the returned node IP address. Valid values: 5 to 12. |
        |**CPU Policy**|Set the CPU policy.

        -   none: This policy indicates that the default CPU affinity is used. This is the default policy.
        -   static: This policy allows pods with specific resource characteristics on the node to be granted with enhanced CPU affinity and exclusivity. |
        |**Taints**|Add taints to the worker nodes in the ACK cluster. |

7.  Click **Next:Component Configurations** to configure components.

    |Parameter|Description|
    |---------|-----------|
    |**Ingress**|Specify whether to install Ingress controllers. By default, **Install Ingress Controllers** is selected. For more information, see [Advanced Ingress configurations](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Advanced Ingress configurations.md).

**Note:** If you want to select **Create Ingress Dashboard**, you must first enable Log Service. |
    |**Monitoring Agents**|Select whether to install the CloudMonitor agent. After the CloudMonitor agent is installed on ECS instances, you can view monitoring information about the instances in the CloudMonitor console.|
    |**Log Service**|Specify whether to enable Log Service. You can select an existing Log Service project or create one. By default, **Enable Log Service** is selected. When you create an application, you can enable Log Service through a few steps. For more information, see [Collect log files from containers by using Log Service](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Log management/Collect log files from containers by using Log Service.md). |
    |**Workflow Engine**|Specify whether to enable Alibaba Cloud Genomics Service \(AGS\).

**Note:** To use this feature, submit a ticket to apply to be added to a whitelist.

    -   If you select this check box, the system automatically installs the AGS workflow plug-in when the system creates the cluster.
    -   If you clear this check box, you must manually install the AGS workflow plug-in. For more information, see [Introduction to AGS CLI](/intl.en-US/User Guide for Genomics Service/AGS workflow/Introduction to AGS CLI.md). |

8.  Click **Next:Confirm Order** to confirm the cluster configurations and select **Terms of Service**.

9.  Click **Create Cluster** to deploy the cluster.

    You can also view detailed information about how the cluster is created in the log.

    **Note:** It requires about 10 minutes to create a managed Kubernetes cluster that consists of multiple nodes.


-   After the cluster is created, you can view the created cluster on the Clusters page in the console.

    ![Clusters](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/6765359951/p77641.png)

-   Click **View Logs** in the **Actions** column. On the Log Information page, you can view the cluster log. To view detailed log data, click **Stack events**.

    ![View the cluster log](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/6765359951/p77637.png)

-   On the Clusters page, find the created cluster and click its name or click **Details** in the Actions column. On the details page, you can click the **Basic Information** tab to view basic information about the cluster and click the **Connection Information** tab to view information about how to connect to the cluster.

    The following information is displayed.

    -   **API Server Public Endpoint**: the IP address and port that the API server uses to provide services over the Internet. It allows you to manage the cluster by using kubectl or other tools on your terminal.

        **Bind EIP** and **Unbind EIP**: These options are available only to managed Kubernetes clusters.

        -   Bind EIP: You can select an existing elastic IP address \(EIP\) or create one.

            The API server restarts after you bind an EIP to the API server. We recommend that you do not perform operations on the cluster during the restart process.

        -   Unbind EIP: You cannot access the API server over the Internet after you unbind the EIP.

            The API server restarts after you unbind the EIP from the API server. We recommend that you do not perform operations on the cluster during the restart process.

    -   **API Server Internal Endpoint**: the IP address and port that the API server uses to provide services within the cluster. The IP address belongs to the Server Load Balancer \(SLB\) instance that is associated to the cluster.
    -   **Testing Domain**: the domain name that is used to test Services. The suffix of the domain name is `<cluster_id>.<region_id>.alicontainer.com`.

        **Note:** To rebind the domain name, click **Rebind Domain Name**.

-   You can [t16645.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to ACK clusters by using kubectl.md) and run the `kubectl get node` command to query information about the nodes in the cluster.

    ![View node information](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/6765359951/p77634.png)


**Related topics**  


[TEE-based confidential computing](/intl.en-US/User Guide for Kubernetes Clusters/TEE-based confidential computing/TEE-based confidential computing.md)

[Use TEE SDK to develop and build Intel SGX 2.0 applications](/intl.en-US/User Guide for Kubernetes Clusters/TEE-based confidential computing/Use TEE SDK to develop and build Intel SGX 2.0 applications.md)

