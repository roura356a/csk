---
keyword: [Knative, canary release based on traffic splitting]
---

# Deploy a canary release for a Knative Service

Knative supports canary releases. You can deploy a canary release to split traffic between two application versions based on a specified ratio. This topic describes how to deploy a canary release for a Knative Service.

Deploy Knative:

-   If you want to deploy Knative in a Container Service for Kubernetes \(ACK\) cluster, see [Deploy Knative](/intl.en-US/User Guide for Kubernetes Clusters/Knative/Manage Knative components/Deploy Knative.md).
-   If you want to deploy Knative in a serverless Kubernetes \(ASK\) cluster, see [Enable Knative](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Knative/Component management/Enable Knative.md).

## Step 1: Create a Knative Service

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Applications** \> **Knative**.

5.  On the Services tab, click **Create Service** in the upper-right corner.

6.  On the Create Service page, set the **Namespace** and **Service Name** parameters. Then, select an image and an image version.

    |Parameter|Description|
    |---------|-----------|
    |**Namespace**|Select the namespace to which the Service belongs.|
    |**Service Name**|Enter a name for the Service. In this example, helloworld-go is used.|
    |**Image Name**|To select an image, click **Select Image**. In the dialog box that appears, select an image and click **OK**. You can also enter the address of a private image **registry**. The registry address must be in the domainname/namespace/imagename:tag format. In this example, registry.cn-hangzhou.aliyuncs.com/knative-sample/helloworld-go is used.|
    |**Image Version**|Click **Select Image Version** and select an image version. By default, the **latest** version is used. In this example, 73fbdd56 is selected.|
    |**Access Protocol**|**HTTP** and **gRPC** are supported.

**Note:** gRPC is developed based on the HTTP/2 standard and Protocol Buffers \(protobuf\) serialization protocol, and supports various programming languages. Compared with HTTP/1.1, HTTP/2 allows you to send and receive packets more efficiently. |
    |**Container Port**|The container port that you want to expose. The port number must be from 1 to 65535. |

    For more information about the other parameters, see [Create a Knative Service](/intl.en-US/User Guide for Kubernetes Clusters/Knative/Manage Knative services/Use Knative to deploy serverless applications.md).

7.  Click **Create**.

    After the Service is created, you can find the Service on the Services tab.

8.  Run the following command to access the Service:

    ```
    curl -H "<Default domain>" http://<Gateway address>
    ```

    -   Default domain: In this example, "host: helloworld-go.default.example.com" is used.
    -   Gateway address: In this example, "39.106.XX.XX" is used.
    Expected output:

    ```
    Hello World!
    ```


## Step 2: Create a revision to deploy a canary release

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Applications** \> **Knative**.

5.  Create a revision.

    1.  On the Services tab, select the Service that you created and click **Details** in the **Actions** column.

    2.  Click **Create Revision**.

    3.  On the **Basic Information** wizard page, click **Advanced** and add the following environment variable setting: `TARGET=Knative`.

        ![11](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1689880261/p252004.png)

    4.  Click **Next Step**.

    5.  On the **Traffic Splitting Settings** wizard page, set the **Percent %** parameter of the revision to 0. Then, click **Create**.

        **Note:** The sum of traffic ratios of all revisions must be 100.

    6.  After the revision is created, you can find details about the new version on the **Services** tab.

    7.  Run the following command to access the Service:

        **Note:** The **Percent** parameter is set to 0 for the new version. This means that all requests to access the helloworld-go Service are sent to the earlier version.

        ```
        curl -H "host: helloworld-go.default.example.com" http://39.106.XX.XX
        ```

        Expected output:

        ```
        Hello World!
        ```

6.  Modify the traffic splitting ratio to deploy a canary release.

    1.  On the Services tab, select the Service that you created and click **Details** in the **Actions** column.

    2.  Click **Split Traffic**.

    3.  In the **Split Traffic** dialog box, set the **Percent** parameter to 50% for both the earlier and new versions. Then, click **OK**.

    4.  After you change the values of the **Percent** parameters, check the details about the old and new versions on the **Services** tab.

    5.  Run the following command to access the Service:

        ```
        curl -H "host: helloworld-go.default.example.com" http://39.106.XX.XX
        ```

        Expected output:

        ![12](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1689880261/p252029.png)

        The **Percent** parameter is set to 50% for both the earlier and new versions. This means that requests to access the helloworld-go Service are evenly distributed to the earlier and new version.

        You can change the value of the **Percent** parameter to implement canary releases. The new version is completely released if you set the **Percent** parameter to 100% for the new version. During the process, you can change the value of the **Percent** parameter to roll back if issues are found in the new version.


