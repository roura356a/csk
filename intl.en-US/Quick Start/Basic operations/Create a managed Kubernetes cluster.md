---
keyword: [Kubernetes, K8s, create]
---

# Create a managed Kubernetes cluster

This topic describes how to create a managed Kubernetes cluster in the Container Service for Kubernetes \(ACK\) console.

-   ACK must be authorized to access other cloud services before you start. For more information, see [Quick start for first-time users](/intl.en-US/Quick Start/Quick start for first-time users.md).
-   Resource Access Management \(RAM\) is activated in the [RAM console](https://ram.console.aliyun.com/).
-   [Auto Scaling \(ESS\) is activated]().

The following example shows how to create a managed Kubernetes cluster. Default values and minimal configurations are used in specific settings.

For more information about the limits of ACK clusters, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  In the upper-right corner of the Clusters page, click **Create Kubernetes Cluster**.

4.  Set the parameters.

    Default values are used for most parameters in this example. The following table describes the parameters.

    1.  Set the parameters on the **Cluster Configurations** wizard page.

        |Parameter|Description|
        |---------|-----------|
        |**Cluster Name**|Enter a name for the ACK cluster.

**Note:** The name must be 1 to 63 characters in length, and can contain digits, letters, and hyphens \(-\). |
        |**Cluster Specification**|Select a cluster type. You can select **Standard edition** or **Professional**. |
        |**Region**|Select a region to deploy the cluster. |
        |**Resource Group**|Move the pointer over **All Resources** at the top of the page and select the resource group to which the cluster belongs. The name of the selected resource group is displayed, as shown in the following figure.

![Resource Group](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9688404061/p127165.png)

**All Resources** is selected in this example. |
        |**Time Zone**|Select a time zone for the ACK cluster. By default, the time zone configured for your browser is selected. |
        |**Kubernetes Version**|Use the default setting. |
        |Container Runtime

|The Docker runtime is selected in this example. For more information about the differences among Docker, containerd, and Sandboxed-Container, see [How do I choose between Docker and Sandboxed-Container?](/intl.en-US/User Guide for Kubernetes Clusters/Sandboxed-Container management/Comparison of Docker, containerd, and Sandboxed-Container.md). |
        |**VPC**|Select a virtual private cloud \(VPC\) to deploy the cluster.

Select a virtual private cloud \(VPC\) to deploy the cluster. Standard VPCs and shared VPCs are supported.

        -   Shared VPC: The owner of a VPC \(resource owner\) can share the vSwitches in the VPC with other accounts in the same organization.
        -   Standard VPC: The owner of a VPC \(resource owner\) cannot share vSwitches in the VPC with other accounts.
**Note:** ACK clusters support only VPCs. You can select a VPC from the drop-down list. If no VPC is available, click **Create VPC** to create one. For more information, see [Work with VPCs](/intl.en-US/VPCs and vSwitchs/Work with VPCs.md). |
        |**VSwitch**|Select vSwitches.

You can select up to three vSwitches that are deployed in different **zones**. If no vSwitch is available, click **Create VSwitch** to create one. For more information, see [Create a vSwitch](/intl.en-US/VPCs and vSwitchs/Work with vSwitches.md). |
        |**Network Plug-in**|By default, Flannel is selected. For more information about Flannel and Terway, see [t64408.md\#section\_k1z\_f1n\_lmh](/intl.en-US/User Guide for Kubernetes Clusters/Network/Container network/Work with Terway.md). |
        |**Pod CIDR Block**|If you select **Flannel** as the network plug-in, you must set **Pod CIDR Block**.

The CIDR block specified by **Pod CIDR Block** cannot overlap with that of the VPC or those of the existing clusters in the VPC. The CIDR block cannot be modified after the cluster is created. The Service CIDR block cannot overlap with the pod CIDR block. For more information about subnetting for ACK clusters, see [Plan CIDR blocks for an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Network/Plan CIDR blocks for an ACK cluster.md). |
        |**Service CIDR**|Set **Service CIDR**. The CIDR block specified by **Service CIDR** cannot overlap with that of the VPC or those of the existing clusters in the VPC. The CIDR block cannot be modified after the cluster is created. The Service CIDR block cannot overlap with the pod CIDR block. For more information about subnetting for ACK clusters, see [Plan CIDR blocks for an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Network/Plan CIDR blocks for an ACK cluster.md). |
        |**IP Addresses per Node**|The default value is 64. **IP Addresses per Node** specifies the maximum number of IP addresses that can be assigned to each node. |
        |**Configure SNAT**|By default, **Configure SNAT for VPC** is selected.

By default, an ACK cluster cannot access the Internet. If the VPC that you select for the ACK cluster cannot access the Internet, you can select **Configure SNAT for VPC**. This way, ACK will create a NAT gateway and configure Source Network Address Translation \(SNAT\) entries to enable Internet access for the VPC. |
        |**Access to API Server**|By default, an internal-facing Server Load Balancer \(SLB\) instance is created for the cluster API server. You can modify the specifications of the SLB instance. For more information, see [Instance types and specifications](/intl.en-US/Classic Load Balancer/User Guide/Instance/SLB instance overview.md).

**Note:** If you delete the SLB instance, you cannot access the API server.

By default, **Expose API Server with EIP** is not selected. The default setting is used in this example.

Select or clear **Expose API Server with EIP**. The ACK API server provides multiple HTTP-based RESTful APIs, which can be used to create, delete, modify, query, and monitor resources, such as pods and Services.

        -   If you select this check box, an elastic IP address \(EIP\) is created and associated with an Internet-facing SLB instance. Port 6443 used by the API server is opened on master nodes. You can connect to and manage the ACK cluster by using kubeconfig over the Internet.
        -   If you clear this check box, no EIP is created. You can connect to and manage the ACK cluster only by using kubeconfig from within the VPC. |
        |**RDS Whitelist**|Set the Relational Database Service \(RDS\) whitelist. Add the IP addresses of the nodes in the cluster to the RDS whitelist.

**Note:** To enable an RDS instance to access the cluster, you must deploy the RDS instance in the VPC where the cluster is deployed.

By default, the Relational Database Service \(RDS\) whitelist is not set. |
        |**Security Group**|You can select **Create Basic Security Group**, **Create Advanced Security Group**, or **Select Existing Security Group**. For more information, see [Overview](/intl.en-US/Security/Security groups/Overview.md).

By default, **Create Advanced Security Group** is selected. The default setting is used in this example. |
        |Show Advanced Options

|The advanced settings of the cluster are not configured in this example. For more information about the advanced settings of an ACK cluster, see [Configure advanced settings for a cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).|

    2.  Click **Next:Worker Configurations** to configure worker nodes on the **Worker Configurations** wizard page.

        |Parameter|Description|
        |---------|-----------|
        |Worker Instance

|Specify whether to use existing Elastic Compute Service \(ECS\) instances or create ECS instances. By default, **Create Instance** is selected. |
        |**Billing Method**|By default, **Pay-As-You-Go** is selected. The **pay-as-you-go** and **subscription** billing methods are supported. |
        |**Instance Type**|You can select multiple instance types. For more information, see [Instance families](/intl.en-US/Instance/Instance families.md). |
        |**Selected Types**|The selected instance types are displayed. |
        |**Quantity**|Specify the number of worker nodes \(ECS instances\) to be created. |
        |**System Disk**|By default, **Ultra Disk** is selected. The default storage capacity is **120 GiB**.|
        |**Mount Data Disk**|By default, this option is not selected.

**Enhanced SSDs**, **SSDs**, and **ultra disks** are supported. You can enable **disk encryption** and **disk backup** when you mount a data disk. |
        |**Operating System**|By default, Alibaba Cloud Linux 2.1903 is selected.|
        |**Logon Type**|        -   Key pair logon.
            -   **Key Pair**: Select an SSH key pair from the drop-down list.
            -   **create a key pair**: Create an SSH key pair if none is available. For more information about how to create an SSH key pair, see [Create an SSH key pair](/intl.en-US/Security/Key pairs/Use an SSH key pair/Create an SSH key pair.md). After the key pair is created, set it as the credential that is used to log on to the cluster.
        -   Password logon.
            -   **Password**: Enter the password that is used to log on to the nodes.
            -   **Confirm Password**: Enter the password again. |
        |Show Advanced Options

|The advanced settings of the cluster are not configured in this example. For more information about the advanced settings of an ACK cluster, see [Configure advanced settings for a cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).|

    3.  Click **Next:Component Configurations** to configure components on the **Component Configurations** wizard page.

        |Parameter|Description|
        |---------|-----------|
        |**Ingress**|By default, **Install Ingress Controllers** is selected and **SLB Network Type** is set to **Public Network**. |
        |**Volume Plug-in**|By default, **CSI** is selected. For more information about the FlexVolume and CSI plug-ins, see [Overview](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/Overview.md). |
        |**Monitoring Agents**|Specify whether to install the Cloud Monitor agent. By default, **Install CloudMonitor Agent on ECS Instance** and **Enable Prometheus Monitoring** are selected. After the Cloud Monitor agent is installed on ECS instance-based nodes, you can view monitoring data about the nodes in the Cloud Monitor console.

The Cloud Monitor agent is not installed in this example. |
        |**Log Service**|Specify whether to enable Log Service. You can select an existing Log Service project or create a new one. By default, **Enable Log Service** is selected. When you create an application, you can activate Log Service through a few steps. For more information, see [Use Log Service to collect container logs](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Log management/Use Log Service to collect container logs.md).

By default, **Install node-problem-detector and Create Event Center** is selected. You can also specify whether to **create Ingress dashboards** in the Log Service console.

Log Service is set up in this example. |
        |**Workflow Engine**|Specify whether to enable Alibaba Cloud Genomics Compute Service \(AGS\).

**Note:** To use this feature, submit a ticket.

        -   If you select this check box, the system automatically installs the AGS workflow plug-in when the system creates the ACK cluster.
        -   If you clear this check box, you must manually install the AGS workflow plug-in. For more information, see [Introduction to AGS CLI](/intl.en-US/User Guide for Genomics Service/AGS workflow/Introduction to AGS CLI.md).
**AGS** is not selected in this example. |

    4.  Click **Next:Confirm Order**.

    5.  Read **Terms of Service**, select the check box, and then click **Create Cluster**.

        **Note:** It requires about 10 minutes to create an ACK cluster that contains multiple nodes.


