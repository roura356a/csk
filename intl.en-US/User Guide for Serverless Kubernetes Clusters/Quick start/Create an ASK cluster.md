---
keyword: [serverless cluster, create an ASK cluster]
---

# Create an ASK cluster

This topic describes how to create a serverless Kubernetes \(ASK\) cluster in the Container Service for Kubernetes \(ACK\) console.

[Resource Access Management \(RAM\) is activated](/intl.en-US/Pricing/Billing.md).

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  In the upper-right corner of the Clusters page, click **Create Kubernetes Cluster**.

4.  On the page that appears, click the **Serverless Kubernetes** tab and set the parameters.

    |Parameter|Description|
    |---------|-----------|
    |**Cluster Name**|Enter a name for the cluster. **Note:** The name must be 1 to 63 characters in length, and can contain digits, letters, and hyphens \(-\). |
    |**Resource Group**|Move the pointer over **All Resources** at the top of the page and select the resource group that you want to use. After you select a resource group, virtual private clouds \(VPCs\) and vSwitches are filtered based on the selected resource group. When you create a cluster, only the VPCs and vSwitches that belong to the selected resource group are displayed in the console.

![Resource Groups](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/9150488261/p127165.png)

Move the pointer over **All Resources** at the top of the page and select the resource group to which the cluster belongs. The name of the selected resource group appears.|
    |**Region**|Select the region where you want to deploy the ASK cluster.|
    |**Time Zone**|Select a time zone for the ACK cluster. By default, the time zone configured for your browser is selected. |
    |**Kubernetes Version**|The Kubernetes versions that are supported by ACK. |
    |**VPC**|Set the virtual private cloud \(VPC\) where you want to deploy the ASK cluster. Kubernetes clusters support only VPCs. You can select **Create VPC** or **Select Existing VPC**.     -   **Create VPC**: If you select this option, ACK automatically creates a VPC and a Network Address Translation \(NAT\) gateway in the VPC. ACK also configures Source Network Address Translation \(SNAT\) entries for the NAT gateway.
    -   **Select Existing VPC**: If you select this option, you must select a VPC from the VPC drop-down list and select vSwitches in the VSwitch section. If you want to enable Internet access, for example, to download container images from the Internet, you must configure a NAT gateway. We recommend that you upload container images to a Container Registry instance in the region where the cluster is deployed. This way, you can pull the images through the VPC.
For more information, see [Create a VPC](/intl.en-US/VPCs and vSwitchs/Work with VPCs.md). |
    |**Zone**|Select a zone to deploy the cluster.|
    |**NAT Gateway**|Specify whether to automatically create a NAT gateway and configure SNAT entries for the NAT gateway. This parameter is required only when you set **VPC** to **Create VPC**.

**Note:** After you select **Create VPC**, you can specify whether to automatically create a NAT gateway and configure SNAT entries for the NAT gateway. If you clear this check box, you must manually create a NAT gateway and configure SNAT entries to enable Internet access for the cluster. Otherwise, the cluster cannot access the Internet.

For more information, see [Create NAT gateways](/intl.en-US/User Guide/Create NAT gateways.md). |
    |**Service CIDR**|Set **Service CIDR**. The CIDR block specified by **Service CIDR** cannot overlap with that of the VPC or those of the existing clusters in the VPC. The CIDR block cannot be modified after the cluster is created. The Service CIDR block cannot overlap with the pod CIDR block. For more information about subnetting for ACK clusters, see [Plan CIDR blocks for an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Network/Plan CIDR blocks for an ACK cluster.md). |
    |**Access to API Server**|By default, an internal-facing Server Load Balancer \(SLB\) instance is created for the cluster API server. You can modify the specification of the SLB instance. For more information, see [Instance specifications](/intl.en-US/Classic Load Balancer/User Guide/Instance/SLB instance overview.md).

**Note:** If you delete the SLB instance, you cannot access the cluster API server.

Select or clear **Expose API Server with EIP**. The ACK API server provides multiple HTTP-based RESTful APIs, which can be used to create, delete, modify, query, and monitor resources, such as pods and Services.

    -   If you select this check box, an elastic IP address \(EIP\) is created and associated with an Internet-facing SLB instance. Port 6443 used by the API server is opened on master nodes. You can connect to and manage the ACK cluster by using kubeconfig over the Internet.
    -   If you clear this check box, no EIP is created. You can connect to and manage the ACK cluster by using kubeconfig only within the VPC.
For more information, see [What is Elastic IP Address?](/intl.en-US/.md). |
    |**Service Discovery**|Configure the Service discovery feature for the cluster. You can select **Disable**, **PrivateZone**, or **CoreDNS**.**Note:**

    -   PrivateZone is a DNS resolution service for private domain names within VPCs. You can use PrivateZone to resolve private domain names to IP addresses in one or more specific VPCs.
    -   CoreDNS is a flexible and scalable Domain Name System \(DNS\) server that is used as a standard Service discovery component in ACK. |
    |**Ingress**|Specify whether to install Ingress controllers. By default, **Install Ingress Controllers** is selected. For more information, see [Advanced Ingress configurations](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Advanced Ingress configurations.md). |
    |**Log Service**|Specify whether to enable Log Service. You can select an existing Log Service project or create a project. If Log Service is not enabled, you cannot use the cluster auditing feature. For more information about Log Service, see [Quick Start](/intl.en-US/.md). |
    |**Knative**|Specify whether to enable Knative. Knative is a Kubernetes-based serverless framework. Knative is intended for developing a cloud-native and cross-platform orchestration standard for serverless applications. For more information, see [Overview](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Knative/Overview.md).|
    |**Deletion Protection**|Specify whether to enable deletion protection. If you select this check box, the cluster cannot be deleted in the console or by calling the API. This avoids user errors.|
    |**Labels**|Add labels to nodes. Enter keys and values, and then click **Add**.

**Note:**

    -   Key is required. Value is optional.
    -   Keys are not case-sensitive. A key must not exceed 64 characters in length, and cannot start with aliyun, http://, or https://.
    -   Values are not case-sensitive. A value must not exceed 128 characters in length, and cannot start with http:// or https://.
    -   The keys of labels that are added to the same resource must be unique. If you add a label with a used key, the label overwrites the one that uses the same key.
    -   You can add up to 20 labels to each resource. If you add more than 20 labels to a resource, all labels become invalid. You must remove unused labels for the remaining labels to take effect. |
    |**Terms of Service**|You must read and select **Terms of Service for Serverless Kubernetes** before the cluster is created.|

5.  On the right side of the page, click **Create Cluster**. In the Confirm message, click **OK** to start the deployment.


-   After the cluster is created, you can view the created ASK cluster on the Clusters page in the ACK console.

![Create an ASK cluster](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/4750488261/p70347.png)

-   On the Clusters page, find the newly created cluster and click **Details** in the Actions column. On the details page, click the **Basic Information** tab to view basic information about the cluster and click the **Connection Information** tab to view information about how to connect to the cluster.


