# Create a Knative service

This topic describes how to create a Knative service.

-   [Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md)
-   [Deploy Knative components in a Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Knative management/Manage Knative components/Deploy Knative components in a Kubernetes cluster.md)
-   Knative Serving is deployed. For more information, see [Deploy a Knative component](/intl.en-US/User Guide for Kubernetes Clusters/Knative management/Manage Knative components/Deploy a Knative component.md).

## Deploy a Knative service

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, choose **Knative** \> **Services**.

3.  On the Knative Services page, click **Create Service** in the upper-right corner of the page.

    ![Create a Knative service](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/4065359951/p52550.png)

4.  Configure the Knative service.

    |Parameter|Description|
    |---------|-----------|
    |**Cluster**|The cluster where the Knative service is deployed.|
    |**Namespace**|The namespace to which the Knative service belongs.|
    |**Service name**|The name of the Knative service.|
    |**Image Name**|To select an image, click Select Image. In the dialog box that appears, select an image and click **OK**. You can also enter the address of a private image registry. The registry address must be in the domainname/namespace/imagename:tag format. registry.cn-hangzhou.aliyuncs.com/knative-sample/helloworld-go is used in this example. |
    |**Image Version**|Click Select Image Version and select an image version. 73fbdd56 is selected in this example.

 ![Example](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5065359951/p127510.png) |
    |**Access Protocol**|**HTTP** and **gRPC** are supported. |
    |**Container Port**|The container port that you want to open. Enter a port number from 1 to 65535. |
    |**Internal Access Only**|If you select the **Internal Access Only** check box, the Knative service cannot be accessed over the Internet. |
    |**Maximum Concurrent Requests**|The maximum number of concurrent requests supported by the container. The default value is 0. This indicates that the number of concurrent requests is unlimited. |
    |**Minimum Pods**|The minimum number of pods that are kept running when no request is received. If you set the parameter to 0, the number of running pods is reduced to 0 when no access request is received. |
    |**Maximum Pods**|The minimum number of pods that are kept running when no request is received. |
    |**Resource Limit**|The maximum amount of CPU and memory resources that can be allocated to the Knative service. This prevents the service from occupying too many resources. CPU usage is measured in cores. Memory usage is measured in bytes or MiB. |
    |**Lifecycle**|Includes the command and arguments. Note the following limits:

    -   If no command or argument is specified, the default command and arguments of the image are used.
    -   If you specify only arguments, the default command of the image and the specified arguments are used.
    -   If you specify both the command and arguments, the default configurations of the image are overwritten. |
    |**Environment Variables**|Set environment variables in key-value pairs. |
    |**Volume**||

5.  Click **Create**.


## Access a Knative service

After the Knative service is created, map the domain name of the Knative service to the IP address of the access gateway. Then, you can use the domain name to access the Knative service. Procedure:

1.  In the left-side navigation pane of the ACK console, choose **Knative** \> **Services**, and then click the target service.

    ![The newly created Knative service](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5065359951/p52562.png)

    In the **Basic Information** section, you can view information about the **access gateway** and **domain**.

2.  Add the following information to the Hosts file on your local host to map the domain name of the Knative service to the IP address of the gateway.

    Example:

    ```
    121.xx.xxx.xx helloworld-go.default.example.com
    ```

3.  After you modify the Hosts file, access the Knative service by using the domain name.

    ![Access the Knative service](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/6065359951/p52568.png)


