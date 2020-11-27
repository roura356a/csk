# Deploy a Hello World application

This topic describes how to deploy a Hello World application by creating a Knative Service.

-   [Create a managed kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed kubernetes cluster.md)
-   [Deploy a Knative component](/intl.en-US/User Guide for Kubernetes Clusters/Knative management/Manage Knative components/Deploy Knative components in a Kubernetes cluster.md)
-   Knative Serving is deployed in your cluster of Container Service for Kubernetes \(ACK\). For more information, see [Deploy a Knative component](/intl.en-US/User Guide for Kubernetes Clusters/Knative management/Manage Knative components/Deploy a Knative component.md).

## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, choose **Knative** \> **Services**.

3.  In the upper-right corner of the Knative Services page, click **Create Service**.

4.  In the dialog box that appears, select the **cluster** and **namespace** to deploy the Knative Service, enter a **name** for the Service, and specify an image registry address and an image version.

    |Parameter|Description|
    |---------|-----------|
    |**Service Name**|The name of the Knative Service. In this example, the name is specified as helloworld-go.|
    |**Image Name**|To select an image, click Select Image. In the dialog box that appears, select an image and click **OK**. You can also enter the address of a private image registry. The registry address must be in the domainname/namespace/imagename:tag format. In this example, registry.cn-hangzhou.aliyuncs.com/knative-sample/helloworld-go is used.|
    |**Image Version**|Click Select Image Version to select an image version. If you do not specify an image version, the latest version is used. In this example, 73fbdd56 is selected.|
    |**Environment Variables**|Set environment variables in key-value pairs. In this example, `TARGET=Knative` is entered.|

    For more information about other parameters, see [Parameter description](/intl.en-US/User Guide for Kubernetes Clusters/Knative management/Manage Knative services/Create a Knative service.md).

5.  Click **Create**.

    On the Knative Services page, you can view the newly created Knative Service.


## Access a Knative Service.

To access the created Knative Service through its URL, perform the following steps:

1.  In the left-side navigation pane of the ACK console, choose **Knative** \> **Services**. The Knative Servicess page appears. On the Knative Services page, you can find the IP address of the gateway for the Knative Service.

2.  Add a mapping rule to the Hosts file on your on-premises machine in following format:

    ```
    IP address of the gateway + Domain name
    ```

    Example:

    ```
    47.95.XX.XX helloworld-go.default.example.com
    ```

3.  After you modify the Hosts file, enter http://helloworld-go.default.example.com into the address bar of your browser and press Enter to access the Knative Service.

    ![Access the Hello World Service](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6065359951/p52568.png)


