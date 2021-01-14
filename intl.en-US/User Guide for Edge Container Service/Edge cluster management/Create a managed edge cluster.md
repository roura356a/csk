---
keyword: [managed edge cluster, create a managed edge cluster]
---

# Create a managed edge cluster

This topic describes how to create a managed edge cluster in the Container Service for Kubernetes \(ACK\) console. Managed edge clusters are designed to bring cloud computing to edges \(clients\). Managed edge clusters can be created, managed, and maintained in the ACK console. ACK is a platform that is developed based on the edge computing infrastructure and integrated with cloud computing and edge computing.

ACK, Auto Scaling \(ESS\), and Resource Access Management \(RAM\) are activated.

You can log on to the [ACK console](https://cs.console.aliyun.com/), [RAM console](https://ram.console.aliyun.com/), and [ESS console](https://essnew.console.aliyun.com) to activate these services.

**Note:**

-   SLB instances that are created along with the ACK cluster support only the pay-as-you-go billing method.
-   ACK clusters support only virtual private clouds \(VPCs\).
-   Each account can consume only a limited amount of computing resources. Clusters cannot be created if your computing resources are insufficient. When you create clusters, make sure that you have sufficient resources. To increase the quota limit of computing resources for your account, submit a ticket.
    -   You can create up to 100 security groups under each account.
    -   You can create up to 60 pay-as-you-go SLB instances under each account.
    -   You can create up to 20 elastic IP addresses \(EIPs\) under each account.

The sharp growth of smart devices on the Internet and the needs for deploying business and processing data at edges has a significant impact on edge computing services. Based on these requirements, a variety of new edge computing services are designed, such as edge intelligence, real-time edge computing, and edge analytics. Traditional cloud computing platforms provide computing and storage services on the cloud. However, this no longer meets the requirements from edge devices for time-efficient computing, larger storage capacity, and enhanced computing capacity. To fix these issues, ACK provides managed edge clusters to coordinate services on the cloud and edges. A managed edge cluster is a standard, secure, and highly-available Kubernetes cluster deployed on the cloud. This type of cluster is integrated with features of Alibaba Cloud, such as virtualization, storage, networking, and security. This simplifies the management and maintenance of clusters and allows you to focus on your business development. In addition, quick access to a variety of heterogeneous edge computing services is supported at the edges. The cloud control center allows you to manage edge devices, such as IoT gateway devices, terminals, Content Delivery Network \(CDN\) resources, and on-premises data centers. The X86 and ARM architectures are supported. Managed edge clusters have been widely used in various industries, such as edge intelligence, intelligent buildings, intelligent factories, audio and video live streaming, online education, and CDN.

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  In the upper-right corner of the Clusters page, click **Create Kubernetes cluster**.

4.  In the Select Cluster Template dialog box, find Edge Cluster and click **Create** in the **Managed Clusters** section.

    The **Managed Edge Kubernetes** page appears.

5.  Configure the cluster.

    Configure basic settings of the cluster.

    |Parameter|Description|
    |---------|-----------|
    |**Cluster Name**|Enter a name for the ACK cluster.

**Note:** The name must be 1 to 63 characters in length. It can contain digits, letters, and hyphens \(-\). |
    |**Region**|Select a region to deploy the ACK cluster. |
    |**Resource Group**|Move the pointer over **All Resources** at the top of the page and select the resource group to which the ACK cluster belongs. The name of the selected resource group appears on the page.

![Resource Group](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9688404061/p127165.png) |
    |**Kubernetes Version**|The supported Kubernetes versions.|
    |**VPC**|Select a virtual private cloud \(VPC\) to deploy the ACK cluster. Shared VPCs and standard VPCs are supported.

    -   Shared VPC: The owner of a VPC \(resource owner\) can share vSwitches in the VPC under the account of the owner with other accounts in the same organization.
    -   Standard VPC: The owner of a VPC \(resource owner\) cannot share vSwitches in the VPC under the account of the owner with other accounts.
**Note:** ACK clusters support only VPCs. You can select a VPC from the drop-down list. If no VPC is available, click **Create VPC** to create one. For more information, see [Create a VPC](/intl.en-US/VPCs and VSwitches/VPC management/Create a VPC.md). |
    |**VSwitch**|Select vSwitches.

You can select up to three vSwitches that are deployed in different **zones**. If no vSwitch is available, click **Create VSwitch** to create one. For more information, see [Create a VSwitch](/intl.en-US/VPCs and VSwitches/VSwitch management/Create a VSwitch.md). |
    |**Pod CIDR Block**|If you select **Flannel**, you must set **Pod CIDR Block**.

The CIDR blocks specified by **Pod CIDR Block** and **Service CIDR** cannot overlap with that of the VPC or existing ACK clusters in the VPC. The CIDR Block cannot be modified after it is specified. The Service CIDR block cannot overlap with the Pod CIDR block. For more information about network segmentation of ACK clusters, see [Assign CIDR blocks to resources in a Kubernetes cluster under a VPC](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Assign CIDR blocks to resources in a Kubernetes cluster under a VPC.md). |
    |**Service CIDR**|
    |**IP Addresses per Node**|If you select **Flannel**, you must set the number of **IP addresses per node**.

**Note:** **IP Addresses per Node** specifies the maximum number of IP addresses that can be assigned to each node. We recommend that you use the default value. |
    |**Configure SNAT**|By default, an ACK cluster cannot be accessed over the Internet. If the VPC that you select for the ACK cluster cannot access the Internet, you can select **Configure SNAT for VPC**. Then, ACK creates a Network Address Translation \(NAT\) gateway and configures Source Network Address Translation \(SNAT\) entries to enable Internet access for the VPC. |
    |**Public Access**|By default, an internal-facing SLB instance is created for the API server. You can modify the specifications of the SLB instance. For more information, see [Specification](/intl.en-US/Classic Load Balancer/User Guide/Instance/SLB instance overview.md).

**Note:** If you delete the SLB instance, you cannot access the API server.

Select or clear **Expose API Server with EIP**. The ACK API server provides multiple HTTP-based RESTful APIs, which can be used to create, delete, modify, query, and monitor resources such as pods and Services.

    -   If you select this check box, an elastic IP address \(EIP\) is created and attached to a public-facing Server Load Balancer \(SLB\) instance. Port 6443 used by the API server is opened on master nodes. You can connect to and manage the ACK cluster by using kubeconfig over the Internet.
    -   If you clear this check box, no EIP is created. You can connect to and manage the ACK cluster only by using kubeconfig from within the VPC.
**Note:** Edge nodes need to call the API over the Internet. If you clear **Expose API Server with EIP**, the edge nodes cannot be connected to the edge cluster. As a result, the created cluster cannot be used in edge computing scenarios. |
    |**RDS Whitelist**|Set the Relational Database Service \(RDS\) whitelist. Add the IP addresses of nodes in the ACK cluster to the RDS whitelist.

**Note:** To enable an RDS instance to access the ACK cluster, you must deploy the RDS instance in the VPC where the ACK cluster is deployed. |
    |**Security Group**|You can select **Create Basic Security Group**, **Create Advanced Security Group**, or **Select Existing Security Group**. For more information, see [Overview](/intl.en-US/Security/Security groups/Overview.md). |

    Advanced settings

    |Parameter|Description|
    |---------|-----------|
    |**Labels**|Add labels to nodes. Enter a key and a value, and then click **Add**.

**Note:**

    -   Key is required. Value is optional.
    -   Keys are not case-sensitive. A key must be 1 to 64 characters in length, and cannot start with aliyun, http://, or https://.
    -   Values are not case-sensitive. A value must be 1 to 128 characters in length and cannot start with http:// or https://. This parameter can be empty.
    -   The keys of labels that are added to the same resource must be unique. If you add a label with a used key, the label overwrites the one that uses the same key.
    -   You can add up to 20 labels to each resource. If you add more than 20 labels to a resource, all labels become invalid. You must remove unused labels for the remaining labels to take effect. |
    |**Deletion Protection**|Specify whether to enable deletion protection. If you select this check box, the ACK cluster cannot be deleted in the console or by calling the API. This prevents user errors. |

6.  Click **Next:Worker Configurations** to configure worker nodes.

    **Note:** In a managed edge cluster, you must configure at least one worker node to deploy components.

    |Parameter|Description|
    |---------|-----------|
    |**Instance Type**|You can select multiple instance types. For more information, see [Instance families](/intl.en-US/Instance/Instance families.md).

**Note:** To use advanced features such as logging, monitoring, and reverse tunneling, you must deploy the related components in the cloud. Therefore, you must create at least one ECS instance-based worker node to deploy these components. |
    |**Selected Types**|The selected instance types are displayed. |
    |**Quantity**|Specify the number of worker nodes to be created. |
    |**System Disk**|Set the system disks of worker nodes. SSDs and ultra disks are supported.|
    |**Mount Data Disk**|**Enhanced SSDs**, **SSDs**, and **ultra disks** are supported.

**Note:** You can select **Enable Backup** to back up disk data. |
    |**Logon Type**|**Note:** You must set the logon type if you select **Install the CloudMonitor Agent on ECS Nodes** or **Enable Log Service**. |
    |**Key Pair**|

7.  Click **Next:Component Configurations** to configure components.

    |Parameter|Description|
    |---------|-----------|
    |**CloudMonitor Agent**|Specify whether to install the Cloud Monitor agent. **After the Cloud Monitor agent is installed on ECS nodes**, you can view monitoring information about the nodes in the Cloud Monitor console.|
    |**Log Service**|Specify whether to enable Log Service. You can select an existing Log Service project or create a new one. By default, **Enable Log Service** is selected. When you create an application, you can perform a few steps to enable Log Service. For more information, see [Use Log Service to collect container logs](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Log management/Use Log Service to collect container logs.md).

You can also select or clear **Collect Logs of Control Plane Components**. If you select this check box, logs of components on the ACK control plane are collected to the Log Service project under your account. For more information, see [t2020982.md\#]().

**Note:** By default, **Collect Logs of Control Plane Components** is selected for a professional managed Kubernetes cluster. |
    |**Workflow Engine**|Specify whether to enable Alibaba Cloud Genomics Compute Service \(AGS\).

    -   If you select this check box, the system automatically installs the AGS workflow plug-in when the system creates the ACK cluster.
    -   If you clear this check box, you must manually install the AGS workflow plug-in. For more information, see [Introduction to AGS CLI](/intl.en-US/User Guide for Genomics Service/AGS workflow/Introduction to AGS CLI.md). |

8.  Click **Next:Confirm Order**.

9.  Read and select Terms of Service and click **Create Cluster**.

    **Note:** It takes about 10 minutes to create a managed edge cluster.


After the cluster is created, you can find the newly created cluster on the Clusters page in the ACK console.

![Clusters](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6036089951/p51372.png)

Click **View Logs** in the Actions column. On the Log Information page, you can view cluster logs. To view detailed log information, click **Stack events**.

![View cluster details](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2883068951/p51373.png)

On the Clusters page, find the newly created cluster and click **Details** in the **Actions** column. On the details page of the cluster, you can click the **Basic Information** tab to view the basic information about the cluster and click the **Connection Information** tab to view the information about how to connect to the cluster.

The following information is displayed.

-   **API Server Public Endpoint**: the IP address and port that the Kubernetes API server uses to provide services over the Internet. It allows you to manage the cluster by using kubectl or other tools on the client.
-   **API Server Internal Endpoint**: the IP address and port that the Kubernetes API Server uses to provide services within the cluster.
-   **Testing Domain**: the domain name that is used for service testing. The suffix of the domain is `<cluster_id>.<region_id>.alicontainer.com`.

    **Note:** To rebind the domain name, click **Rebind Domain Name**.


You can use kubectl and run the `kubectl get node` command to connect to the cluster and view the information about the nodes in the cluster. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md).

![Check available GPU-based devices](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2883068951/p51375.png)

**Related topics**  


[ACK@Edge overview](/intl.en-US/User Guide for Edge Container Service/ACK@Edge overview.md)

[Upgrade an edge cluster](/intl.en-US/User Guide for Edge Container Service/Edge cluster management/Upgrade an edge cluster.md)

[Add an edge node](/intl.en-US/User Guide for Edge Container Service/Node management/Add an edge node.md)

[Enhance the monitoring on edge Kubernetes clusters](/intl.en-US/User Guide for Edge Container Service/Application management/Enhance the monitoring on edge Kubernetes clusters.md)

