---
keyword: [ASK clusters, create an ASK cluster]
---

# Create an ASK cluster

This topic describes how to create a serverless Kubernetes \(ASK\) cluster in the Container Service for Kubernetes \(ACK\) console.

[Resource Access Management \(RAM\) is activated](/intl.en-US/Pricing/Billing.md)

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Serverless Clusters**.

3.  In Clusters, click **Create Serverless Kubernetes Cluster**.

4.  Configure the cluster.

    |Parameter|Description|
    |---------|-----------|
    |**Cluster Name**|Enter a name for the cluster. **Note:** The name must be 1 to 63 characters in length, and can contain digits, letters, and hyphens \(-\). |
    |**Resource Group**|Move the pointer over **All Resources** at the top of the page and select the resource group to which the cluster belongs. The name of the selected resource group appears on the page.|
    |**Region**|Select a region to deploy the cluster.|
    |**Zone**|Select a zone to deploy the cluster.|
    |**VPC**|Set a virtual private cloud \(VPC\) to deploy the cluster. ASK clusters support only VPCs. You can select an **existing VPC** or **create a VPC**.     -   **Create VPC**: If you select this option, ACK automatically creates a VPC and a network address translation \(NAT\) gateway in the VPC. ACK also configures source network address translation \(SNAT\) entries for the NAT gateway.
    -   **Select Existing VPC**: If you select this option, you need to select a VPC from the VPC drop-down list and select VSwitches from the VSwitch drop-down list. If you want to enable Internet access, for example, to download container images from the Internet, you must configure a NAT gateway. We recommend that you upload container images to an Container Registry \(ACR\) instance in the region where the cluster is deployed. This way, you can pull the images through the VPC. |
    |**NAT Gateway**|Specify whether to automatically create a NAT gateway and configure SNAT entries for the VPC. This parameter is available only when you select **Create VPC** for **VPC**.

**Note:** After you select **Create VPC**, you can select or clear Automatically Create a NAT Gateway and Configure SNAT Rules for VPC. If you clear this check box, you must manually create a NAT gateway and configure SNAT entries to enable Internet access for the cluster. Otherwise, the cluster cannot access the Internet. |
    |**VSwitch**|Select VSwitches. This parameter is available only when you select **Select Existing VPC** for **VPC**. You can select up to three VSwitches that are deployed in different **zones** from the VSwitch drop-down list. If no VSwitch is available, click **Create VSwitch** to create one. For more information, see [Create a VSwitch](/intl.en-US/VPCs and VSwitches/VSwitch management/Create a VSwitch.md). |
    |**Public Access**|Specify whether to **expose the API server with an elastic IP address \(EIP\)**. **Note:** Edge nodes need to interact with the API server over the Internet. If you clear **Expose API Server with EIP**, the edge nodes cannot be connected to the edge cluster. As a result, the created cluster cannot be used in edge computing scenarios.

The ACK API server provides multiple HTTP-based RESTful APIs, which can be used to create, delete, modify, query, and monitor resources such as pods and Services.

    -   If you select this check box, an EIP is created, and port 6443 used by the API server is opened on master nodes. You can connect to and manage the cluster by using kubeconfig over the Internet.
    -   If you clear this check box, no EIP is created. You can connect to and manage the cluster only by using kubeconfig from within the VPC. |
    |**PrivateZone**|Specify whether to enable PrivateZone-based service discovery. This allows you to access a Service through the private domain of the Service from within the VPC where the cluster is deployed. **Note:** Before you select this check box, make sure that PrivateZone is activated. For more information, see [Use the service discovery feature based on Alibaba Cloud DNS PrivateZone in serverless Kubernetes clusters](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Application management/Serverless clusters support the service discovery based on Alibaba Cloud DNS PrivateZone.md). |
    |**Log Service**|Specify whether to enable Log Service. You can select an existing Log Service project or create one. If Log Service is not enabled, you cannot use the cluster auditing feature. |
    |**Deletion Protection**|Specify whether to enable deletion protection. If you select this check box, the cluster cannot be deleted in the console or by calling API operations. This avoids user errors.|
    |**Labels**|Attach labels to nodes. Enter keys and values, and then click **Add**.

**Note:**

    -   Key is required. Value is optional.
    -   Keys are not case-sensitive. A key must be 1 to 64 characters in length, and cannot start with aliyun, http://, or https://.
    -   Values are not case-sensitive. A value must be 1 to 128 characters in length, and cannot start with http:// or https://.
    -   The keys of labels attached to the same resource must be unique. If you add a label with a used key, the label overwrites the one using the same key.
    -   You can attach up to 20 labels to each resource. If you attach more than 20 labels to a resource, all labels become invalid. You must detach unused labels for the remaining labels to take effect. |
    |**Terms of Service**|You must read and select **Terms of Service for Serverless Kubernetes** before the cluster is created.|

5.  Click **Create Cluster** on the right side of the page. In the Confirm message, click **OK** to start the deployment.


-   After the cluster is created, you can find the created cluster on the Clusters page in the console.

![Create an ASK cluster](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5197297951/p70347.png)

-   On the Clusters page, find the created cluster and click **Details** in the Actions column. On the details page, click the **Basic Information** tab to view the basic information about the cluster and click the **Connection Information** tab to view the connections to the cluster.


