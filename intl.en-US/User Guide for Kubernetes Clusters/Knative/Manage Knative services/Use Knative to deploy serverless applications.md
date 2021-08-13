---
keyword: [deploy serverless applications, create Knative Services]
---

# Use Knative to deploy serverless applications

Knative Services are used to deploy applications. This topic describes how to create a Knative Service.

-   [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)
-   [Deploy Knative](/intl.en-US/User Guide for Kubernetes Clusters/Knative/Manage Knative components/Deploy Knative.md)
-   Knative Serving is deployed. For more information, see [Deploy a Knative component](/intl.en-US/User Guide for Kubernetes Clusters/Knative/Manage Knative components/Deploy a Knative component.md).

## Step 1: Deploy a Knative Service

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Applications** \> **Knative**.

5.  On the Services tab, click **Create Service** in the upper-right corner.

6.  Configure the Knative Service.

    |Parameter|Description|
    |---------|-----------|
    |**Namespace**|Select the namespace to which the Service belongs.|
    |**Service Name**|Enter a name for the Service.|
    |**Image Name**|To select an image, click **Select Image**. In the dialog box that appears, select an image and click **OK**. You can also enter the address of a private image registry. The address must be in the domainname/namespace/imagename:tag format. In this example, the address is registry.cn-hangzhou.aliyuncs.com/knative-sample/helloworld-go. |
    |**Image Version**|To select an image version, click **Select Image Version**. In this example, the image version is 73fbdd56.

![Example](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/5065359951/p127510.png) |
    |**Access Protocol**|**HTTP** and **gRPC** are supported. |
    |**Container Port**|The container port that you want to expose. The port number must be in the range of 1 to 65535. |
    |**Internal Access Only**|If you select **Internal Access Only**, the Knative Service cannot be accessed over the Internet. |
    |**Maximum Concurrent Requests**|Set the maximum number of concurrent requests supported by the container. The default value is 0. This indicates that the number of concurrent requests is unlimited. |
    |**Minimum Pods**|Set the minimum number of pods that must be kept running when no request is received. If you set this parameter to 0, the number of running pods is reduced to zero when no request is received. |
    |**Maximum Pods**|Set the maximum number of pods that are allowed to run. |
    |**Resource Limit**|Set the maximum amount of CPU, memory, and GPU resources that can be allocated to the Knative Service. This prevents the Service from occupying an excessive amount of resources. CPU usage is measured in cores. Memory usage is measured in bytes or mebibytes. |
    |**Lifecycle**|You can set the Command and Parameter parameters. Take note of the following limits:

    -   If Command and Parameter are not set, the default values of Command and Parameter in the image configurations are used.
    -   If you set only Parameter, the default value of Command in the image configurations and the specified value of Parameter are used.
    -   If you set both Command and Parameter, the default values of Command and Parameter in the image configurations are overwritten by the specified values. |
    |**Environment Variables**|Set environment variables in key-value pairs. |
    |**Volume**|You can mount local storage volumes and persistent volume claims \(PVCs\) to the container.     -   Add Local Storage: You can select HostPath, ConfigMap, Secret, or EmptyDir. The specified volume is mounted to a path in the container. For more information, see [Volumes](https://kubernetes.io/docs/concepts/storage/volumes/?spm=0.0.0.0.8VJbrE).
    -   Add PVC: Select Cloud Storage. |

7.  Click **Create**.


## Step 2: Access the Knative Service

After the Knative Service is deployed, you can point its domain name to the IP address of a gateway to associate the Service with the gateway. This allows you to access the Knative Service by using its URL. Perform the following steps:

1.  On the Services tab, click the name of the Service.

    In the **Basic Information** section, you can view information about the access gateway and domain name.

    ![The Knative Service](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/5065359951/p52562.png)

2.  Add the following information to the hosts file to point the domain name of the Service to the IP address of the gateway:

    Example:

    ```
    121.xx.xxx.xx helloworld-go.default.example.com
    ```

3.  After you modify the hosts file, you can use the domain name to access the Knative Service.

    ![Access the Knative Service](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/6065359951/p52568.png)


