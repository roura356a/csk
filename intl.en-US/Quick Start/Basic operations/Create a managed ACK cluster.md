---
keyword: [Kubernetes, , create a cluster]
---

# Create a managed ACK cluster

This topic describes how to create a managed Container Service for Kubernetes \(ACK\) cluster in the ACK console.

-   The ACK default roles are assigned to your service account. For more information, see [Use ACK for the first time](/intl.en-US/Quick Start/Use ACK for the first time.md).
-   Resource Access Management \(RAM\) is activated in the [RAM console](https://ram.console.aliyun.com/).
-   Auto Scaling is enabled. For more information, see [Enable Auto Scaling]().

The following steps describe how to create a managed cluster. Default or minimum configurations are used as examples.

For more information about the limits of ACK clusters, see [Create a managed kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed kubernetes cluster.md).

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  In the upper-right corner of the Clusters page, click **Create Kubernetes Cluster**. In the Select Cluster Template dialog box, find Standard Managed Cluster and click **Create**.

4.  Configure the cluster parameters.

    Most parameters retain the default values. The following table describes the parameters.

    1.  Configure parameters in the **Cluster Configurations** step.

        |Parameter|Description|
        |---------|-----------|
        |**Cluster Name**|Enter a name for the ACK cluster.

**Note:** The name must be 1 to 63 characters in length. It can contain digits, letters, and hyphens \(-\). |
        |**Cluster specifications**|Select a cluster type. **Standard edition** and **Professional \(Preview\)** are supported. |
        |**Region**|Select a region to deploy the ACK cluster. |
        |**Resource Group**|Move the pointer over **All Resources** at the top of the page and select the resource group to which the ACK cluster belongs. The name of the selected resource group appears on the page.

![Resource Groups](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/9688404061/p127165.png)

By default, **All Resources** is selected. |
        |**Kubernetes Version**|Use the default version. |
        |Container Runtime

|Use the default Docker runtime. For more information about the differences between Docker and Sandboxed-Container, see [How do I select between Docker and Sandboxed-Container?](/intl.en-US/User Guide for Kubernetes Clusters/Sandboxed-Container management/How do I select between Docker and Sandboxed-Container?.md). |
        |**VPC**|Select a virtual private cloud \(VPC\) to deploy the cluster.

Select a virtual private cloud \(VPC\) to deploy the ACK cluster. Shared VPCs and standard VPCs are supported.

        -   Shared VPC: The owner of a VPC \(resource owner\) can share VSwitches in the VPC under the account of the owner with other accounts in the same organization.
        -   Standard VPC: The owner of a VPC \(resource owner\) cannot share VSwitches in the VPC under the account of the owner with other accounts.
**Note:** ACK clusters support only VPCs. You can select a VPC from the drop-down list. If no VPC is available, click **Create VPC** to create one. For more information, see [Create a VPC](/intl.en-US/VPCs and VSwitches/VPC management/Create a VPC.md). |
        |**VSwitch**|Select VSwitches.

You can select up to three VSwitches deployed in different **zones**. If no VSwitch is available, click **Create VSwitch** to create one. For more information, see [Create a VSwitch](/intl.en-US/VPCs and VSwitches/VSwitch management/Create a VSwitch.md). |
        |**Network Plug-in**|By default, Flannel is selected. For more information about Flannel and Terway, see [Flannel and Terway](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Use Terway.md). |
        |**Pod CIDR Block**|If you select **Flannel**, you must set **Pod CIDR Block**.

The CIDR block specified by **Pod CIDR Block** cannot overlap with that of the VPC or existing ACK clusters in the VPC. The CIDR Block cannot be modified after it is specified. The Service CIDR block cannot overlap with the Pod CIDR block. For more information about subnetting for ACK clusters, see [Assign CIDR blocks to resources in a Kubernetes cluster under a VPC](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Assign CIDR blocks to resources in a Kubernetes cluster under a VPC.md). |
        |**Service CIDR**|Set **Service CIDR**. The CIDR block specified by **Service CIDR** cannot overlap with that of the VPC or existing ACK clusters in the VPC. The CIDR block cannot be modified after it is specified. The Service CIDR block cannot overlap with the Pod CIDR block. For more information about subnetting for ACK clusters, see [Assign CIDR blocks to resources in a Kubernetes cluster under a VPC](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Assign CIDR blocks to resources in a Kubernetes cluster under a VPC.md). |
        |**IP Addresses per Node**|By default, 64 is selected. **IP Addresses per Node** specifies the number of IP addresses that can be assigned to a node. |
        |**Configure SNAT**|By default, **Configure SNAT for VPC** is selected.

By default, an ACK cluster cannot be accessed over the Internet. If the VPC that you select for the ACK cluster cannot access the Internet, you can select **Configure SNAT for VPC**. Then, ACK creates a Network Address Translation \(NAT\) gateway and configures Source Network Address Translation \(SNAT\) entries to enable Internet access for the VPC. |
        |**Public Access**|By default, **Expose API Server with EIP** is cleared.

The ACK API server provides multiple HTTP-based RESTful APIs, which can be used to create, delete, modify, query, and monitor resources such as pods and Services.

        -   If you select this check box, an EIP is created and attached to a public-facing Server Load Balancer \(SLB\) instance. Port 6443 used by the API server is opened on master nodes. You can connect to and manage the ACK cluster by using kubeconfig over the Internet.
        -   If you clear this check box, no EIP is created. You can connect to and manage the ACK cluster only by using kubeconfig from within the VPC. |
        |**RDS Whitelist**|Set the Relational Database Service \(RDS\) whitelist. Add the IP addresses of nodes in the ACK cluster to the RDS whitelist.

**Note:** To enable an RDS instance to access the ACK cluster, you must deploy the RDS instance in the same VPC as the ACK cluster.

By default, RDS Whitelist is not configured. |
        |**Security Group**|You can select **Create Basic Security Group**, **Create Advanced Security Group**, or **Select Existing Security Group**. For more information, see [Overview](/intl.en-US/Security/Security groups/Overview.md).

By default, **Create Advanced Security Group** is selected. |
        |Show Advanced Options

|The advanced parameters of the cluster are not configured in this example. For more information about how to configure advanced parameters, see [Configure advanced parameters of a cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed kubernetes cluster.md).|

    2.  Click **Next:Worker Configurations** to go to the **Worker Configurations** step.

        |Parameter|Description|
        |---------|-----------|
        |Worker Instance

|Select Create Instance or Add Existing Instance. By default, **Create Instance** is selected. |
        |**Billing Method**|Select **Pay-As-You-Go** or **Subscription**. By default, **Pay-As-You-Go** is selected. |
        |**Instance Type**|You can select multiple instance types. For more information, see [Instance families](/intl.en-US/Instance/Instance families.md). |
        |**Selected Types**|The selected instance types are displayed. |
        |**Quantity**|Specify the number of worker nodes to be created. |
        |**System Disk**|By default, **Ultra Disk** is selected. The default storage capacity is **120 GiB**.|
        |**Mount Data Disk**|By default, this parameter is not configured.

**ESSDs**, **SSDs**, and **ultra disks** are supported. You can enable disk **encryption** and **backup** when you mount data disks. |
        |**Operating System**|By default, Alibaba Cloud Linux 2.1903 is selected.|
        |**Logon Type**|        -   Set the key pair.
            -   **Key Pair**: Select an SSH key pair from the drop-down list.
            -   **create a key pair**: Create an SSH key pair if no SSH key pair is available. For more information about how to create an SSH key pair, see [Create an SSH key pair](/intl.en-US/Security/Key pairs/Use an SSH key pair/Create an SSH key pair.md). After the key pair is created, set it as the credential that is used to log on to the ACK cluster.
        -   Password:
            -   **Password**: Enter the password that is used to log on to the nodes.
            -   **Confirm Password**: Enter the password again. |
        |Show Advanced Options

|The advanced parameters of the cluster are not configured in this example. For more information about how to configure advanced parameters, see Configure [advanced parameters of a cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed kubernetes cluster.md).|

    3.  Click **Next:Component Configurations** to go to the **Component Configurations** step.

        |Parameter|Description|
        |---------|-----------|
        |**Ingress**|By default, **Install Ingress Controllers** is selected, and **SLB Network Type** is set to **Public Network**. |
        |**Volume Plug-in**|By default, **CSI** is selected. For more information about FlexVolume and CSI, see [Volume plug-ins](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/Volume plug-ins.md). |
        |**Monitoring Agents**|Specify whether to install the Cloud Monitor agent. By default, **Install CloudMonitor Agent on ECS Instance** and **Enable Prometheus Monitoring** are selected. After the Cloud Monitor agent is installed on ECS nodes, you can view monitoring data about the nodes in the Cloud Monitor console.

The Cloud Monitor agent is not installed in this example. |
        |**Log Service**|Specify whether to activate Log Service. You can select an existing Log Service project or create a new one.

By default, **Enable Log Service** is selected. When you create an application, you can perform a few steps to enable Log Service. For more information, see [Use Log Service to collect container logs](/intl.en-US/User Guide for Kubernetes Clusters/Log management/Use Log Service to collect container logs.md).

By default, **Install node-problem-detector and Create Event Center** is selected. You can also specify whether to **create Ingress dashboard** in the Log Service console.

Log Service is disabled in this example. |
        |**Workflow Engine**|Specify whether to activate Alibaba Cloud Genomics Compute Service \(AGS\).

        -   If you select this check box, the system automatically installs the AGS workflow plug-in when the system creates the ACK cluster.
        -   If you clear this check box, you must manually install the AGS workflow plug-in. For more information, see [Introduction to AGS CLI](/intl.en-US/User Guide for Genomics Service/AGS workflow/Introduction to AGS CLI.md).
**AGS** is cleared in this example. |

    4.  Click **Next:Confirm Order**.

    5.  Read and agree to **Terms of Service**, and click **Create Cluster**.

        **Note:** It takes about 10 minutes to create an ACK cluster that contains multiple nodes.


