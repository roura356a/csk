# Step 3: Delete services of earlier versions

This topic describes how to how to delete earlier versions of a service after you release a stable version by using phased release .

## Prerequisites

-   A cluster of Alibaba Cloud Container Service for Kubernetes \(ACK\) is created. For more information, see [Create a cluster of ACK Proprietary Edition](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a cluster of ACK Proprietary Edition.md).
-   The ACK cluster can be accessed by using kubectl. For more information, see [Use kubectl to connect to a cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Manage and access clusters/Use kubectl to connect to a cluster.md).
-   A service of an earlier version is deployed and a new service version is released by using phased release. For more information, see [Step 1: Deploy a service](/intl.en-US/Best Practices/Release/Implement a canary release and a blue/green deployment through Ingress in a Kubernetes cluster/Step 1: Deploy a service.md) and [Step 2: Release the latest version of a service](/intl.en-US/Best Practices/Release/Implement a canary release and a blue/green deployment through Ingress in a Kubernetes cluster/Step 2: Release the latest version of a service.md).

## Delete an earlier service version from the CLI

1.  Run the following command to modify the YAML file that has been deployed in [Step 2: Release the latest version of a service](/intl.en-US/Best Practices/Release/Implement a canary release and a blue/green deployment through Ingress in a Kubernetes cluster/Step 2: Release the latest version of a service.md) to delete an earlier service version.

    **Note:** The `annotations` in the YAML file must also be deleted.

    ```
    $ kubectl get ingress gray-release-02
    ```


## Delete an earlier service version in the ACK console

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, choose **Ingresses and Load Balancing** \> **Ingresses**.

3.  On the Ingresses page, select the target cluster from the Cluster drop-down list and select the target namespace from the Namespace drop-down list. Find the target Ingress and select **Update** from the **More** drop-down list in the **Actions** column.

    ![Ingress](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/1555359951/p32281.png)

4.  Perform the following steps:

    1.  In the Update dialog box, choose **Rules** \> **Services**, find the service version that you want to delete, and click the delete icon.

        ![Ingress rules](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/2555359951/p32282.png)

    2.  Click **Update**.


1.  Return to the Ingresses page. The page shows only the Ingress rule that routes to the new-nginx service of the latest version.

    ![Ingress](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/2555359951/p32283.png)

2.  Log on to a master node of the cluster. Then, run the following curl command to send requests to the Ingress.

    ```
    $ curl -H "Host: www.example.com" http://<EXTERNAL_IP>
    ```

    ![nodes](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/2555359951/p32284.png)


The output shows that requests are routed to the new-nginx service of the latest version after the Ingress is updated. This means that the phased release process is complete. You can also delete the Deployments and Services that are deployed for the new-nginx service of the deleted version.

