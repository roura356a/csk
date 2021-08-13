---
keyword: [deploy serverless application with a few clicks, Knative]
---

# Use Knative to deploy serverless applications

This topic describes how to use Knative to deploy a serverless application. In this topic, a Hello World application is used as an example.

-   [Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md).
-   [Deploy Knative](/intl.en-US/User Guide for Kubernetes Clusters/Knative/Manage Knative components/Deploy Knative.md).

## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Applications** \> **Knative**.

5.  In the upper-right corner of the Services tab, click **Create Service**.

6.  Set the **Cluster**, **Namespace**, and **Service Name** parameters. Then, select an image and an image version.

    |Parameter|Description|
    |---------|-----------|
    |**Service Name**|Enter a name for the Service. In this example, helloworld-go is used.|
    |**Image Name**|To select an image, click **Select Image**. In the Select Image dialog box, select an image and click **OK**. You can also enter the address of a private image registry. The address must be in the domainname/namespace/imagename:tag format. In this example, registry.cn-hangzhou.aliyuncs.com/knative-sample/helloworld-go is used.|
    |**Image Version**|To select an image version, click **Select Image Version**. If you do not set this parameter, the latest version is used. In this example, the image version is 73fbdd56.|
    |**Environment Variables**|Set environment variables in key-value pairs. In this example, `TARGET=Knative` is added as an environment variable.|

    For more information about how to set other parameters, see [Parameter descriptions](/intl.en-US/User Guide for Kubernetes Clusters/Knative/Manage Knative services/Use Knative to deploy serverless applications.md).

7.  Click **Create**.

    After the Service is created, it is displayed on the **Services** tab.


## Access the Knative Service

After the Knative Service is deployed, you can point its domain name to the IP address of a gateway to associate the Service with the gateway. This allows you to access the Knative Service by using its URL.

1.  In the left-side navigation pane of the ACK console, choose **Knative** \> **Components** to go to the Knative Components page. On the Knative Components page, you can view the IP address of the gateway.

2.  Add the following information to the hosts file to point the domain name of the Service to the IP address of the gateway:

    Gateway IP address + Service domain name

    Example:

    ```
    47.95.XX.XX helloworld-go.default.example.com
    ```

3.  After you modify the hosts file, enter http://helloworld-go.default.example.com in the address bar of the browser and press Enter to access the Service.

    ![Access the Service](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/6065359951/p52568.png)


