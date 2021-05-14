---
keyword: [deploy serverless application with a few clicks, Knative]
---

# Use Knative to deploy serverless applications

You can use Knative to deploy serverless applications with a few clicks. In the following example, a "Hello, World!" application is deployed.

-   [Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md).
-   [Deploy Knative](/intl.en-US/User Guide for Kubernetes Clusters/Knative/Manage Knative components/Deploy Knative.md).

## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Applications** \> **Knative**.

5.  In the upper-right corner of the Services tab, click **Create Service**.

6.  On the Create Service page, set the **Cluster**, **Namespace**, and **Service Name** parameters. Then, select an image and image version.

    |Parameter|Description|
    |---------|-----------|
    |**Service Name**|Enter a name for the Service. helloworld-go is used in this example.|
    |**Image Name**|To select an image, click Select Image. In the dialog box that appears, select an image and click **OK**. You can also enter the address of an image in a private image registry. The image address must be in the following format: domainname/namespace/imagename:tag. In this example, registry.cn-hangzhou.aliyuncs.com/knative-sample/helloworld-go is used.|
    |**Image Version**|Click Select Image Version to select an image version. If you do not set this parameter, the latest version is used. In this example, 73fbdd56 is selected.|
    |**Environment Variables**|Set environment variables in key-value pairs. In this example, `TARGET=Knative` is entered.|

    For more information about how to set other parameters, see [Parameter descriptions](/intl.en-US/User Guide for Kubernetes Clusters/Knative/Manage Knative services/Create a Knative Service.md).

7.  Click **Create**.

    After you create the Service, the Service is displayed on the **Services** tab.


## Access the Knative Service

After the Knative Service is deployed, you can point its domain name to the IP address of the gateway that is associated with the Service. This allows you to access the Knative Service through its URL.

1.  In the left-side navigation pane of the ACK console, choose **Knative** \> **Components** to go to the Knative Components page. On the Knative Components page, you can view the gateway of the Knative Service.

2.  Add the following information to the hosts file to point the domain name of the Service to the IP address of the gateway:

    ```
    Gateway IP address + Domain name
    ```

    Example:

    ```
    47.95.XX.XX helloworld-go.default.example.com
    ```

3.  After you modify the hosts file, enter http://helloworld-go.default.example.com into the address bar of the browser to access the Service.

    ![Access the Service](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6065359951/p52568.png)


