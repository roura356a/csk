# Deploy a Hello World application

This topic describes how to deploy a Knative service. The Hello World application is used as an example.

-   [Create a managed kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed kubernetes cluster.md)
-   [Deploy a Knative component](/intl.en-US/User Guide for Kubernetes Clusters/Knative management/Manage Knative components/Deploy a Knative component.md)
-   The Serving component is deployed in a Container Service for Kubernetes \(ACK\) cluster. For more information, see [Deploy a Knative component](/intl.en-US/User Guide for Kubernetes Clusters/Knative management/Manage Knative components/Deploy a Knative component.md).

## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

3.  On the Knative Services page, click **Create Service** in the upper-right corner of the page.

4.  On the Create Service page, set the **Cluster**, **Namespace**, and **Service Name** parameters. Then, select an image and image version.

    |Parameter|Description|
    |---------|-----------|
    |**Service Name**|The name of the Knative service. The name is helloworld-go in this example.|
    |**Image Name**|Click Select Image. In the dialog box that appears, select an image and click **OK**. You can also enter the address of a private image registry. The registry address must be in the domainname/namespace/imagename:tag format. This parameter is set to registry.cn-hangzhou.aliyuncs.com/knative-sample/helloworld-go in this example.|
    |**Image Version**|Click Select Image Version and select an image version. If you do not set this parameter, the latest version is used. The image version is 73fbdd56 in this example.|
    |**Environment Variables**|Set environment variables in key-value pairs. `TARGET=Knative` is entered in this example.|

    For more information about how to set other parameters, see [Parameter descriptions](/intl.en-US/User Guide for Kubernetes Clusters/Knative management/Manage Knative services/Create a Knative service.md).

5.  Click **Create**.

    After the Knative service is created, you can view the service on the Knative Services page.


## Access the Knative service

After the Knative service is deployed, you can point its domain name to the IP address of the gateway that is associated with the service. This allows you to access the Knative service through its URL.

1.  In the left-side navigation pane of the ACK console, choose **Knative** \> **Components** to go to the Knative Components page. On the Knative Components page, you can view the gateway of the Knative service.

2.  Add the following information to the hosts file to point the domain name of the service to the IP address of the gateway:

    ```
    IP address of the gateway + Domain name
    ```

    Example:

    ```
    47.95.XX.XX helloworld-go.default.example.com
    ```

3.  After you modify the hosts file, enter http://helloworld-go.default.example.com into the address bar of the browser to access the service.

    ![Access the Knative service](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6065359951/p52568.png)


