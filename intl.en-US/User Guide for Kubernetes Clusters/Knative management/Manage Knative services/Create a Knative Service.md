# Create a Knative Service

Knative Services are used to deploy applications. This topic describes how to create a Knative Service.

-   [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)
-   [Deploy Knative](/intl.en-US/User Guide for Kubernetes Clusters/Knative management/Manage Knative components/Deploy Knative.md)
-   Knative Serving is deployed. For more information, see [Deploy a Knative component](/intl.en-US/User Guide for Kubernetes Clusters/Knative management/Manage Knative components/Deploy a Knative component.md).

## Step 1: Deploy a Knative Service

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster, or click **Details** in the **Actions** column. The details page of the cluster appears.

3.  In the left-side navigation pane of the details page, choose **Applications** \> **Knative**.

4.  On the Services tab, click **Create Service** in the upper-right corner.

5.  Configure the Knative Service.

    |Parameter|Description|
    |---------|-----------|
    |**Cluster**|Select the cluster where you want to deploy the Service.|
    |**Namespace**|Select the namespace to which the Service belongs.|
    |**Service Name**|Enter a name for the Service.|
    |**Image Name**|To select an image, click Select Image. In the dialog box that appears, select an image and click **OK**. You can also enter the address of a private image registry. The registry address must be in the following format: domainname/namespace/imagename:tag. registry.cn-hangzhou.aliyuncs.com/knative-sample/helloworld-go is used in this example. |
    |**Image Version**|Click Select Image Version and select an image version. 73fbdd56 is selected in this example.

![Examples](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5065359951/p127510.png) |
    |**Access Protocol**|Both **HTTP** and **gRPC** are supported. |
    |**Container Port**|The container port that you want to expose. The port number must be in the range of 1 to 65535. |
    |**Internal Access Only**|If you select **Internal Access Only**, the Knative Service cannot be accessed over the Internet. |
    |**Maximum Concurrent Requests**|Set the maximum number of concurrent requests supported by the container. The default value is 0. This indicates that the number of concurrent requests is unlimited. |
    |**Minimum Pods**|Set the minimum number of pods that must be kept running when no request is received. If you set this parameter to 0, the number of running pods is reduced to zero when no request is received. |
    |**Maximum Pods**|Set the maximum number of pods that are allowed to run. |
    |**Resource Limit**|Set the maximum amount of CPU and memory resources that can be allocated to the Knative Service. This prevents the Service from occupying an excessive amount of resources. CPU usage is measured in cores. Memory usage is measured in bytes or MiB. |
    |**Lifecycle**|Includes the Command and Parameter parameters. Take note of the following limits:

    -   If Command and Parameter are not set, the default values of Command and Parameter of the image are used.
    -   If you set only Parameter, the default value of Command of the image and the specified value of Parameter are used.
    -   If you set both Command and Parameter, the default values of Command and Parameter of the image are overwritten by the specified values. |
    |**Environment Variables**|Set environment variables in key-value pairs. |
    |**Volume**|You can mount local storage volumes and persistent volume claims \(PVCs\) to the container.    -   Add Local Storage: You can select HostPath, ConfigMap, Secret, or EmptyDir. The volume is mounted to a path in the container. For more information, see [Volumes](https://kubernetes.io/docs/concepts/storage/volumes/?spm=0.0.0.0.8VJbrE).
    -   Add PVC: supports the cloud storage PVC type. |

6.  Click **Create**.


## Step 2: Access the Knative Service

After the Knative Service is created, point the domain name of the Knative Service to the IP address of the access gateway. Then, you can use the domain name to access the Knative Service. Perform the following steps:

1.  On the Services tab, click the name of the Service.

    In the **Basic Information** section, you can view information about the access gateway and domain name.

    ![The Knative Service](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5065359951/p52562.png)

2.  Modify your hosts file to point the domain name of the Knative Service to the IP address of the gateway.

    Example:

    ```
    121.xx.xxx.xx helloworld-go.default.example.com
    ```

3.  After you modify the hosts file, use the domain name to access the Knative Service.

    ![Access the Knative Service](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6065359951/p52568.png)


