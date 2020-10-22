# Create a serverless Kubernetes cluster

This topic describes how to create a serverless Kubernetes cluster in the Container Service console.

Container Service and Resource Access Management \(RAM\) are activated. You can activate these services in the [Container Service console](https://cs.console.aliyun.com/) and [RAM console](https://ram.console.aliyun.com/).

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Serverless Clusters**.

3.  On the Clusters page, click **Create Serverless Kubernetes Cluster** in the upper-right corner of the page.

4.  Configure the cluster.

    |Parameter|Description|
    |---------|-----------|
    |**Cluster Name**|Enter the name of the cluster. **Note:** The name must be 1 to 63 characters in length and can contain digits, Chinese characters, letters, and hyphens \(-\). |
    |**Resource Group**|Move the pointer over **Account's all Resources** at the top of the page and select the resource group where the cluster resides. The name of the selected resource group appears here.|
    |**Region**|Select the region where the cluster resides.|
    |**Zone**|Select the zone where the cluster resides.|
    |**VPC**|Set the Virtual Private Cloud \(VPC\) network of the cluster. Kubernetes clusters only support VPC networks. You can select either **Create VPC** or **Select Existing VPC**.     -   **Create VPC**: If you select this option, Container Service automatically creates a VPC, creates a network address translation \(NAT\) gateway in the VPC, and configures source network address translation \(SNAT\) rules.
    -   **Select Existing VPC**: If you select this option, you need to select an existing VPC and a VSwitch from the corresponding drop-down lists. If the cluster needs to access the Internet, for example, to download container images from the Internet, you must configure a NAT gateway. We recommend that you upload required container images to an Alibaba Cloud Container Registry instance in the region where the cluster resides. In this case, the cluster can pull the container images through VPC addresses. |
    |**NAT Gateway**|Select whether to automatically create a NAT gateway and configure SNAT rules for the VPC network. You need to set this parameter only when you select **Create VPC** for **VPC**.

**Note:** After you select **Create VPC**, you can select or clear Automatically Create a NAT Gateway and Configure SNAT Rules for VPC. If you clear this check box, you need to create a NAT gateway or configure SNAT rules to enable Internet access to the VPC network. Otherwise, the cluster cannot be created. |
    |**VSwitch**|Set the VSwitch. You need to set this parameter only when you select **Select Existing VPC** for **VPC**. Select one to three VSwitches. We recommend that you select VSwitches in different **zones**. If no VSwitch is available, click **Create VSwitch** to create one. For more information, see [Create a VSwitch](/intl.en-US/VPCs and VSwitches/VSwitch management/Create a VSwitch.md). |
    |**Public Access**|Select whether to **Expose API Server with EIP**. **Note:** Generally, edge nodes communicate with the API server on the cloud through the Internet. If you clear the **Expose API Server with EIP** check box, edge nodes cannot communicate with the API server on the cloud, and the created cluster cannot be used for edge computing.

The Kubernetes API server provides multiple HTTP-based RESTful APIs, which can be used to create, delete, modify, query, and watch resource objects such as pods and services.

    -   If you select this check box, an Elastic IP Address \(EIP\) is created and port 6443 used by the API server is enabled on master nodes. You can connect to and manage the cluster by using kubeconfig through the Internet.
    -   If you clear this check box, no EIP is created. You can use kubeconfig to configure access to the cluster from within the VPC network only. |
    |**PrivateZone**|Select whether to enable PrivateZone-based service discovery, which allows you to access a service through the service domain name within the VPC of the cluster. **Note:** Before selecting this check box, make sure that you have activated the PrivateZone service. For more information, see [Use the service discovery feature based on Alibaba Cloud DNS PrivateZone in serverless Kubernetes clusters](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Application management/Serverless clusters support the service discovery based on Alibaba Cloud DNS PrivateZone.md). |
    |**Log Service**|Select whether to enable Log Service. You can select an existing project or create a project. If you clear this check box, you cannot use cluster auditing features. |
    |**Deletion Protection**|Select whether to enable Deletion Protection. If you select this check box, the cluster cannot be deleted through the console or API operations.|
    |**Labels**|Attach labels to the cluster. Enter the key and value, and click **Add**.

**Note:**

    -   The key is required whereas the value is optional.
    -   The key is case insensitive and can be up to 64 characters in length. It cannot start with any of the following strings: aliyun, http://, and https://.
    -   The value is optional, case insensitive, and can be up to 128 characters in length. It cannot start with string http:// or https://.
    -   The key must be unique among the labels attached to the same resource. If you specify a duplicate key when you create a label, the existing label will be overwritten.
    -   You can attach up to 20 labels to each resource. To attach more labels, you must remove existing labels first. |
    |**Terms of Service**|You must select **Terms of Service for Serverless Kubernetes** before creating a cluster.|

5.  Click **Create Cluster** on the right of the page. In the Confirm dialog box that appears, click **OK** to start the deployment.


-   After the cluster is created, you can find the cluster on the Clusters page in the console.

![Create a Kubernetes cluster](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5197297951/p70347.png)

-   You can click **Manage** in the Actions column of the created cluster to view basic information about the cluster.

![Manage the cluster](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5197297951/p70348.png)


