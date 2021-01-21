# Create a Knative Service

This topic describes how to create a Knative Service in the Container Service for Kubernetes \(ACK\) console.

-   [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)
-   [Deploy Knative](/intl.en-US/User Guide for Kubernetes Clusters/Knative management/Manage Knative components/Deploy Knative.md)
-   Knative Serving is deployed. For more information, see [Deploy components](/intl.en-US/User Guide for Kubernetes Clusters/Knative management/Manage Knative components/Deploy a Knative component.md).

## Create a Knative Service

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, choose **Knative** \> **Services**.

3.  In the upper-right corner of the Knative Services page, click **Create Service**.

4.  Configure the Knative Service.

    |Parameter|Description|
    |---------|-----------|
    |**Cluster**|The cluster where the Knative Service is deployed.|
    |**Namespace**|The namespace where the Knative Service is deployed.|
    |**Service Name**|The name of the Knative Service.|
    |**Image Name**|To select an image, cick Select Image. In the dialog box that appears, select an image and click **OK**. You can also enter the address of a private image registry. The registry address must be in the domainname/namespace/imagename:tag format. In this example, registry.cn-hangzhou.aliyuncs.com/knative-sample/helloworld-go is used. |
    |**Image Version**|Click Select Image Version and select an image version. 73fbdd56 is selected in this example.

![Examples](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5065359951/p127510.png) |
    |**Access Protocol**|**HTTP** and **gRPC** are supported. |
    |**Container Port**|The container port that you want to open. Enter a port number from 1 to 65535. |
    |**Internal Access Only**|If you select **Internal Access Only**, the Knative Service cannot be accessed over the Internet. |
    |**Maximum Concurrent Requests**|The maximum number of concurrent requests that are supported by the container. The default value is 0. This indicates that the number of concurrent requests is unlimited. |
    |**Minimum Pods**|The minimum number of pods that are kept running when no request is received. If you set the parameter to 0, the number of running pods is reduced to 0 when no request is received. |
    |**Maximum Pods**|The maximum number of pods that are kept running when no request is received. |
    |**Resource Limit**|The maximum amount of CPU and memory resources that can be allocated to the Knative Service. This prevents the Service from occupying excessive resources. CPU resources are measured in cores. Memory resources are measured in bytes or MiB. |
    |**Lifecycle**|You need to specify the command and arguments based on the following descriptions:

    -   If no command or argument is specified, the default command and arguments of the image are used.
    -   If you specify only arguments, the default command of the image and the specified arguments are used.
    -   If you specify both the command and arguments, the default configurations of the image are overwritten. |
    |**Environment Variables**|Set environment variables in key-value pairs. |
    |**Volume**|You can mount local storage volumes and persistent volume claims \(PVCs\) to the container.    -   Local Storage: You can select hostPath, ConfigMap, Secret, or EmptyDir. The volume is mounted to a path in the container. For more information, see [Volumes](https://kubernetes.io/docs/concepts/storage/volumes/?spm=0.0.0.0.8VJbrE).
    -   PVC: Select Cloud Storage. |

5.  Click **Create**.


## Access a Knative Service

After the Knative Service is created, map the domain name of the Knative Service to the IP address of the access gateway. Then, you can use the domain name to access the Knative Service. Procedure:

1.  Go the details page of the cluster where the Knative Service is deployed. In the left-side navigation pane, choose **Knative** \> **Services**. On the Knative Services page, find and click the newly created Knative Service.

    ![The newly created Knative Service](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5065359951/p52562.png)

    In the **Basic Information** section of the details page of the Knative Service, you can view detailed information about the **access gateway** and **domain**.

2.  Add the following information to the Hosts file on your on-premises machine to map the domain name of the Knative Service to the IP address of the gateway.

    Example:

    ```
    121.xx.xxx.xx helloworld-go.default.example.com
    ```

3.  After you modify the Hosts file, access the Knative Service by using the domain name.

    ![Access the Knative Service](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6065359951/p52568.png)


