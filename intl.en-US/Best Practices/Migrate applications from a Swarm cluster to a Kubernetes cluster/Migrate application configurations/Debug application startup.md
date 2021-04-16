# Debug application startup

This topic describes how to view the status of an application in the Container Service for Kubernetes \(ACK\) console after you migrate the application configurations. This topic also describes how to troubleshoot and fix application startup issues when the application configurations are migrated.

## Check the application startup process

After you manually migrate application configurations or migrate application configurations by using Kompose, you can check whether the application starts up as expected in the ACK console. If any exceptions occur during the startup process, you can view application boot log in the console to locate the causes.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

5.  Select the corresponding namespace and click **Details** on the right side of the application that fails to start up.

    ![Details](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8765748161/p47983.png)

6.  Click the **Pods** tab, find the container that runs the application and click **Logs** in the Actions column.

    ![Pods](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8765748161/p47984.png)

7.  In the Logs tab, view the log of the application.

    ![Logs](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8765748161/p47985.png)


## Fix application startup issues

Analyze and fix application startup issues successively. For example, the preceding log indicates that the nginx-deployment application fails to access config. To fix this issue, perform the following operations:

1.  Manually create a Service in the ACK cluster to allow other applications to access config.

    The **Name** in the following figure indicates the Service name. The name must be the same as the hostname. You can specify **Port Mapping** as needed. For example, you can specify the same value for **Name**, **Service Port**, and **Container Port**. For more information, see [Manage Services in the ACK console](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Manage Services.md).

    ![Create a Service](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8765748161/p47986.png)

2.  After you create the Service, deploy the application again to check whether the issue is fixed.

    If the state of the container is **Running** as shown in the following figure, it indicates that the container starts up as expected.

    ![Container status](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8765748161/p47987.png)


Exceptions may occur during the application startup process. For more information about these exceptions and how to fix them, see [Troubleshooting](/intl.en-US/Best Practices/Migrate applications from a Swarm cluster to a Kubernetes cluster/Migrate application configurations/Troubleshooting.md).

