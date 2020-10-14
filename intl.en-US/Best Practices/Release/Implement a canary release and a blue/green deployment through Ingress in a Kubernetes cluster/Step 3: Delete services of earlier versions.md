# Step 3: Delete services of earlier versions

This topic describes how to delete earlier versions of a service after you release a stable version by using phased release.

## Prerequisites

-   A Container Service for Kubernetes \(ACK\) cluster is created. For more information, see [Create a cluster of ACK Managed Edition](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a cluster of ACK Managed Edition.md).
-   The cluster is connected through kubectl. For more information, see [Use kubectl to connect to a cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Manage and access clusters/Use kubectl to connect to a cluster.md).
-   A service of an earlier version is deployed and a new service version is released by using phased release. For more information, see [Step 1: Deploy a service](/intl.en-US/Best Practices/Release/Implement a canary release and a blue/green deployment through Ingress in a Kubernetes cluster/Step 1: Deploy a service.md) and [Step 2: Release the latest version of a service](/intl.en-US/Best Practices/Release/Implement a canary release and a blue/green deployment through Ingress in a Kubernetes cluster/Step 2: Release the latest version of a service.md).

## Use the CLI to delete an earlier service version

1.  Run the following command to modify the YAML file that has been deployed in [Step 2: Release the latest version of a service](/intl.en-US/Best Practices/Release/Implement a canary release and a blue/green deployment through Ingress in a Kubernetes cluster/Step 2: Release the latest version of a service.md) to delete an earlier service version:

    **Note:** You must also delete the `annotations` in the YAML file.

    ```
    kubectl get ingress gray-release-02
    ```


## Use the ACK console to delete an earlier service version

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, click **Ingresses**.

5.  On the Ingresses page, select the desired namespace from the **Namespace** drop-down list. Find the ingress that you want to update and choose **More** \> **Update** in the **Actions** column.

    ![Update the ingress](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/3144741061/p146977.png)

6.  Perform the following steps to update the ingress:

    1.  In the Update dialog box, find the **Services** section in the **Rules** field, and delete the rule of the earlier service version.

        ![Ingress rules](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/2555359951/p32282.png)

    2.  Click **Update**.


**Execution results**

1.  Return to the Ingresses page. The page shows the only ingress rule that routes to the new-nginx service of the latest version.

    ![Ingresses](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/2555359951/p32283.png)

2.  Log on to a master node of the cluster. Then, use curl to run the following command to send requests to the ingress.

    ```
    curl -H "Host: www.example.com" http://<EXTERNAL_IP>
    ```

    ![nodes](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/2555359951/p32284.png)


According to the command output, the requests are routed to the new-nginx service of the latest version. This indicates that the phased release process is complete. You can also delete the deployment and service that are deployed for the new-nginx service of the deleted version.

