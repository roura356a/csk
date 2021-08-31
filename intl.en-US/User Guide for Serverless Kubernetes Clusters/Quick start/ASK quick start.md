---
keyword: [serverless cluster, create a serverless cluster, ASK, Serverless Kubernetes]
---

# ASK quick start

This topic describes how to create a serverless Kubernetes \(ASK\) cluster in the Container Service for Kubernetes \(ACK\) console. This topic also describes how to create an application from an image and view containers in the ASK cluster.

-   ACK and related cloud services are activated. Required permissions are granted. For more information, see [Quick start for first-time users](/intl.en-US/Quick Start/Quick start for first-time users.md).
-   Elastic Container Instance is activated in the [Elastic Container Instance](https://eci.console.aliyun.com) console.


## Step 1: Create an ASK cluster

**Note:** This section describes only the key parameters that are required to create an ASK cluster. In practical scenarios, you can set the parameters and install components based on your business requirements. For more information, see [Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Cluster/Create an ASK cluster.md).

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  In the upper-right corner of the Clusters page, click **Create Kubernetes Cluster**.

4.  On the page that appears, click the **Serverless Kubernetes** tab and set the parameters.

    |Parameter|Description|
    |---------|-----------|
    |**Cluster Name**|Enter a name for the cluster. In this example, ask-hangzhou is used. **Note:** The name must be 1 to 63 characters in length, and can contain letters, digits, and hyphens \(-\). |
    |**Region**|Select the region where you want to deploy the cluster. In this example, **China \(Hangzhou\)** is selected.|
    |**VPC**|Set the virtual private cloud \(VPC\) where you want to deploy the cluster. Kubernetes clusters support only VPCs. You can select **Create VPC** or **Select Existing VPC**.     -   **Create VPC**: If you select this option, ACK automatically creates a VPC and a Network Address Translation \(NAT\) gateway in the VPC. ACK also configures Source Network Address Translation \(SNAT\) rules on the NAT gateway.
    -   **Select Existing VPC**: If you select this option, you must select a VPC from the VPC drop-down list and select vSwitches in the vSwitch section. If you want to enable Internet access, for example, to download container images, you must configure a NAT gateway. We recommend that you upload container images to a Container Registry instance in the region where the cluster is deployed. This way, you can pull images through the VPC.
For more information, see [Create a VPC](/intl.en-US/VPCs and vSwitchs/Work with VPCs.md).

In this example, an existing VPC is selected. |
    |**Configure SNAT**|Specifies whether to automatically create a NAT gateway and configure SNAT rules on the NAT gateway. This parameter is required only when you set **VPC** to **Create VPC**.

**Note:** After you select **Create VPC**, you can specify whether to automatically create a NAT gateway and configure SNAT rules on the NAT gateway. If you clear this check box, you must manually create a NAT gateway and configure SNAT rules on the VPC. Otherwise, the cluster deployed in the VPC cannot access the Internet.

For more information, see [Create NAT gateways](/intl.en-US/User guide for Public NAT gateways/Create NAT gateways.md).

In this example, **Configure SNAT for VPC** is selected. |
    |**Service CIDR**|Set **Service CIDR**. The CIDR block specified by **Service CIDR** cannot overlap with that of the VPC or those of the existing clusters in the VPC. The CIDR block cannot be modified after the cluster is created. The Service CIDR block cannot overlap with the pod CIDR block. For more information about subnetting for ACK clusters, see [Plan CIDR blocks for an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Network/Plan CIDR blocks for an ACK cluster.md).

In this example, **172.21.0.0/20** is used. |
    |**Access to API Server**|By default, an internal-facing Server Load Balancer \(SLB\) instance is created for the Kubernetes API server. You can modify the specification of the SLB instance. For more information, see [Instance specifications](/intl.en-US/Classic Load Balancer/User Guide/Instance/SLB instance overview.md).

**Note:** If you delete the SLB instance, you cannot access the Kubernetes API server.

Select or clear **Expose API Server with EIP**. The Kubernetes API server provides various HTTP-based RESTful APIs, which can be used to create, delete, modify, query, and monitor resources, such as pods and Services.

    -   If you select this check box, an elastic IP address \(EIP\) is created and associated with an SLB instance. In this case, the Kubernetes API server is publicly exposed through port 6443 of the EIP. You can use kubeconfig to connect to and manage the cluster over the Internet.
    -   If you clear this check box, no EIP is created. You can connect to and manage the cluster only by using kubeconfig from within the VPC.
For more information, see [Control public access to the API server of a cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Cluster access control/Control public access to the API server of a cluster.md).

In this example, **Expose API Server with EIP** is selected. |
    |**Terms of Service**|You must read and select **Terms of Service for Serverless Kubernetes** before you create the cluster.|

5.  On the right side of the **Serverless Kubernetes** tab, click **Create Cluster**. In the Confirm message, click **OK**.


**What to do next**

-   After the cluster is created, you can find the new cluster on the Clusters page in the console.

![Create cluster](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/4750488261/p70347.png)

-   On the Clusters page, find the new cluster and click **Details** in the Actions column. On the cluster details page, click the **Basic Information** tab to view basic information about the cluster and click the **Connection Information** tab to view information about how to connect to the cluster.

    ![Basic information](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/5087830361/p135811.png)


## Step 2: Create an application from an image

**Note:** This section describes only the key parameters that are required to create an application from an image. For more information, see [Create an application from an image](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Application/Create an application from an image.md).

**1. Configure basic information**

1.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

2.  In the upper-right corner of the **Deployments** page, click **Create from Image**.

3.  On the Basic Information wizard page, configure the basic settings of the application.

    |Parameter|Description|
    |---------|-----------|
    |Application|Enter a name for the application. In this example, serverless-app-deployment is used.|
    |Type|Select the type of the resource object. Valid values include **Deployment** and **StatefulSet**. In this example, **Deployment** is selected.|

4.  Click **Next**.


**2. Configure container settings**

1.  In the **General** section of the **Container** wizard page, configure the basic settings of the container.

    |Parameter|Description|
    |---------|-----------|
    |Image Name|You can click **Select Image**. In the dialog box that appears, select an image and click **OK**.You can also enter the address of an image in a private registry. The address must be in the `domainname/namespace/imagename:tag` format.

In this example, an image that belongs to a Container Registry Personal Edition instance is used. The instance is deployed in the **China \(Hangzhou\)** region. |
    |Image Version|You can click **Select Image Version** to select an image version. If you do not specify an image version, the latest image version is used. In this example, no image version is specified. |

2.  In the **Ports** section, click **Add** to configure one or more container ports.

    -   Name: Enter a name for the container port. In this example, nginx is used.
    -   Container Port: Enter the port number that you want to open. Valid values: 1 to 65535. In this example, the port number is set to 80.
    -   Protocol: TCP and UDP are supported.
3.  Click **Next**.


**3. Configure advanced settings**

1.  In the **Access Control** section of the **Advanced** wizard page, configure a Service to expose the backend pod.

    Click **Create** on the right side of **Services**. In the Create Service dialog box, set the parameters.

    |Parameter|Description|
    |---------|-----------|
    |Name|Enter a name for the Service. In this example, **serverless-app-svc** is used.|
    |Port Mapping|Specify a Service port and a container port. The container port must be the same as the one that is exposed in the backend pod. In this example, the Service port is set to 8080 and the container port is set to 80. |

    You can find the created Service in the **Access Control** section. You can click **Update** or **Delete** to change the configurations.

2.  Click **Create**.


**4. Access the NGINX application**

1.  On the **Complete** wizard page, click **View Details**. You can find **serverless-app-deployment** on the Deployments page.

    ![Deployments](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/3950488261/p10257.png)

2.  Access the NGINX application.

    1.  In the left-side navigation pane of the cluster details page, choose **Network** \> **Services**. On the Services page, you can find the **serverless-app-svc** Service.

        ![Services](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/3950488261/p10258.png)

    2.  Click the address in the **External Endpoint** column to access the NGINX application through a browser.

        ![nginx](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/9399397161/p146837.png)

        **Note:** For more information about how to access an application through an Ingress, see [Access Services by using an ALB Ingress](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Network management/ALB Ingress Management/Access Services by using an ALB Ingress.md).


## Step 3: View the pod

1.  In the left-side navigation pane of the details page, choose **Workloads** \> **Pods**.

2.  Select the pod that you want to view and click **Details** in the **Actions** column.

    **Note:** You can edit or delete the pod based on your needs. We recommend that you use templates to manage pods that are created by using templates. Do not directly modify or delete these pods.

3.  On the details page of the pod, you can view detailed information about the pod.

    ![2](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/5087830361/p302646.png)


