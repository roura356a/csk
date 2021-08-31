---
keyword: [serverless cluster, create a serverless cluster, ASK, Serverless Kubernetes]
---

# Create an ASK cluster

This topic describes how to create a serverless Kubernetes \(ASK\) cluster in the Container Service for Kubernetes \(ACK\) console.

-   ACK and related cloud services are activated. Required permissions are granted. For more information, see [Quick start for first-time users](/intl.en-US/Quick Start/Quick start for first-time users.md).
-   Elastic Container Instance is activated in the [Elastic Container Instance](https://eci.console.aliyun.com) console.


## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  In the upper-right corner of the Clusters page, click **Create Kubernetes Cluster**.

4.  On the page that appears, click the **Serverless Kubernetes** tab and set the parameters.

    |Parameter|Description|
    |---------|-----------|
    |**Cluster Name**|Enter a name for the cluster. **Note:** The name must be 1 to 63 characters in length, and can contain letters, digits, and hyphens \(-\). |
    |**Region**|Select the region where you want to deploy the cluster.|
    |**Kubernetes Version**|The Kubernetes versions that are supported by ASK. |
    |**VPC**|Set the virtual private cloud \(VPC\) where you want to deploy the cluster. Kubernetes clusters support only VPCs. You can select **Create VPC** or **Select Existing VPC**.     -   **Create VPC**: If you select this option, ACK automatically creates a VPC and a Network Address Translation \(NAT\) gateway in the VPC. ACK also configures Source Network Address Translation \(SNAT\) rules on the NAT gateway.
    -   **Select Existing VPC**: If you select this option, you must select a VPC from the VPC drop-down list and select vSwitches in the vSwitch section. If you want to enable Internet access, for example, to download container images, you must configure a NAT gateway. We recommend that you upload container images to a Container Registry instance in the region where the cluster is deployed. This way, you can pull images through the VPC.
For more information, see [Create a VPC](/intl.en-US/VPCs and vSwitchs/Work with VPCs.md). |
    |**Zone**|Select the zone where you want to deploy the cluster.|
    |**Configure SNAT**|Specifies whether to automatically create a NAT gateway and configure SNAT rules on the NAT gateway. This parameter is required only when you set **VPC** to **Create VPC**.

**Note:** After you select **Create VPC**, you can specify whether to automatically create a NAT gateway and configure SNAT rules on the NAT gateway. If you clear this check box, you must manually create a NAT gateway and configure SNAT rules on the VPC. Otherwise, the cluster deployed in the VPC cannot access the Internet.

For more information, see [Create NAT gateways](/intl.en-US/User guide for Public NAT gateways/Create NAT gateways.md). |
    |**Service CIDR**|Set **Service CIDR**. The CIDR block specified by **Service CIDR** cannot overlap with that of the VPC or those of the existing clusters in the VPC. The CIDR block cannot be modified after the cluster is created. The Service CIDR block cannot overlap with the pod CIDR block. For more information about subnetting for ACK clusters, see [Plan CIDR blocks for an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Network/Plan CIDR blocks for an ACK cluster.md). |
    |**Access to API Server**|By default, an internal-facing Server Load Balancer \(SLB\) instance is created for the Kubernetes API server. You can modify the specification of the SLB instance. For more information, see [Instance specifications](/intl.en-US/Classic Load Balancer/User Guide/Instance/SLB instance overview.md).

**Note:** If you delete the SLB instance, you cannot access the Kubernetes API server.

Select or clear **Expose API Server with EIP**. The Kubernetes API server provides various HTTP-based RESTful APIs, which can be used to create, delete, modify, query, and monitor resources, such as pods and Services.

    -   If you select this check box, an elastic IP address \(EIP\) is created and associated with an SLB instance. In this case, the Kubernetes API server is publicly exposed through port 6443 of the EIP. You can use kubeconfig to connect to and manage the cluster over the Internet.
    -   If you clear this check box, no EIP is created. You can connect to and manage the cluster only by using kubeconfig from within the VPC.
For more information, see [Control public access to the API server of a cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Cluster access control/Control public access to the API server of a cluster.md). |
    |**Service Discovery**|Configure service discovery for the cluster. You can select **Disable**, **PrivateZone**, or **CoreDNS**. **Note:**

    -   PrivateZone is a DNS resolution service for private domain names within VPCs. You can use PrivateZone to resolve private domain names to IP addresses in one or more VPCs.
    -   CoreDNS is a flexible and scalable DNS server that serves as a standard service discovery component in ACK. |
    |**Ingress**|Select whether to install Ingress controllers. You can select **Do Not Install**, **Nginx Ingress**, or **ALB Ingress**.

    -   NGINX Ingresses are optimized based on the open source ingress-nginx and provide flexible and reliable routing services. For more information, see [Overview](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Network management/NGINX Ingress management/Overview.md).
    -   Application Load Balancer \(ALB\) Ingresses are compatible with NGINX Ingresses, and provide improved traffic routing capabilities based on ALB instances. ALB Ingresses support complex routing, automatic certificate discovery, and the HTTP, HTTPS, and QUIC protocols. ALB Ingresses meet the requirements of cloud-native applications for ultra-high elasticity and balancing of heavy traffic loads at Layer 7. For more information, see [ALB Ingress overview](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Network management/ALB Ingress Management/ALB Ingress overview.md). |
    |**Monitoring Service**|Specify whether to install metrics-server to monitor cluster resource usage. The metrics-server component is a resource monitoring tool built based on the open source Metrics Server. The component collects resource usage metrics for all pods in your cluster and enables Horizontal Pod Autoscaler \(HPA\). You can call the Metrics API to retrieve monitoring data.|
    |**Log Service**|Specify whether to enable Log Service. You can select an existing project or create a project. If Log Service is disabled, you cannot use the cluster auditing feature. For more information about Log Service, see [Quick Start](/intl.en-US/.md). |
    |**Knative**|Specify whether to enable Knative. Knative is a Kubernetes-based serverless framework. Knative is intended for developing a cloud-native, cross-platform orchestration standard for serverless applications. For more information, see [Overview](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Knative/Overview.md).|
    |**Time Zone**|Select a time zone for the cluster. By default, the time zone configured for your browser is selected. |
    |**Deletion Protection**|Specify whether to enable deletion protection. If you select this check box, the cluster cannot be deleted in the console or by calling API operations. This protects the cluster from being accidentally deleted.|
    |**Resource Group**|Move the pointer over **All Resources** at the top of the page and select the resource group that you want to use. After you select a resource group, virtual private clouds \(VPCs\) and vSwitches are filtered based on the selected resource group. When you create a cluster, only the VPCs and vSwitches that belong to the selected resource group are displayed in the console.

![Resource Groups](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/9150488261/p127165.png)

Move the pointer over **All Resources** at the top of the page and select the resource group to which the cluster belongs. The name of the selected resource group is displayed in the Resource Group field.|
    |**Labels**|Add labels to the nodes in the cluster. Enter a key and a value, and click **Add**.

**Note:**

    -   Key is required. Value is optional.
    -   Keys are not case-sensitive. A key can contain at most 64 characters and must not start with aliyun, http://, or https://.
    -   Values are not case-sensitive. A value can be empty and can contain at most 128 characters. It cannot be http:// or https://.
    -   The keys of labels that are added to the same resource must be unique. If you add a label with a used key, the label overwrites the one that uses the same key.
    -   You can add at most 20 labels to a resource. If you add more than 20 labels to a resource, all labels become invalid. You must remove excess labels for the remaining labels to take effect. |
    |**Terms of Service**|You must read and select **Terms of Service for Serverless Kubernetes** before you create the cluster.|

5.  On the right side of the page, click **Create Cluster**. In the Confirm message, click **OK** to start the deployment.


-   After the cluster is created, you can find the ASK cluster on the Clusters page in the console.

![Create cluster](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/4750488261/p70347.png)

-   On the Clusters page, find the new cluster and click **Details** in the Actions column. On the cluster details page, click the **Basic Information** tab to view basic information about the cluster and click the **Connection Information** tab to view information about how to connect to the cluster.

    ![Basic information](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/5087830361/p135811.png)


