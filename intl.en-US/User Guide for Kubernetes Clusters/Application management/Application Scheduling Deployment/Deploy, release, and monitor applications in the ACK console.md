---
keyword: [deploy an NGINX application, configure an application, query application logs]
---

# Deploy, release, and monitor applications in the ACK console

The Container Service for Kubernetes \(ACK\) console provides extensive features to help you manage and maintain clusters and applications. This topic describes how to deploy an NGINX application, configure an Ingress, and query log data in the ACK console.

-   An ACK cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   Log Service is enabled and Logtail is installed. For more information, see [Collect log files from containers by using Log Service](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Log management/Collect log files from containers by using Log Service.md).

## Step 1: Deploy an NGINX application

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

5.  On the Deployments page, click **Create from YAML** in the upper-right corner.

6.  Configure the parameters and click **Create**.

    -   In the upper-part of the page, set **Namespace** to **default**.
    -   Set **Sample Template** to **Resource - basic Deployment**.
    -   Configure log collection. For more information about how to configure log collection parameters, see [Collect log files from containers by using Log Service](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Log management/Collect log files from containers by using Log Service.md).

        Add the following content to the `spec` field in the template.

        **Note:** `aliyun_logs_log-nginxvarlog` specifies that the name of the created Logstore is log-nginxvarlog. The value stdout specifies that the Logstore collects standard outputs of containers from the specified path.

        ```
        env:
        - name: aliyun_logs_log-nginxvarlog
          value: stdout
        ```

        ![Configure log collection](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7133121161/p189745.png)

    -   Change the version of the image that is used to deploy the NGINX application to 1.9.1.

        ![Set the image](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9030394161/p189750.png)

    After the NGINX application is created, you can view the application on the Deployments page.


## Step 2: Release the NGINX application

1.  In the left-side navigation pane of the details page, choose **Network** \> **Services**.

2.  On the Services page, click **Create** in the upper-right corner of the page.

3.  In the **Create Service** dialog box, set the parameters and click **Create**.

    |Parameter|Description|
    |---------|-----------|
    |Name|Enter a name for the Service.|
    |Type|Set **Type** to **Cluster IP**.|
    |Backend|Select **nginx-deployment-basic** from the drop-down list.|
    |Port Mapping|Enter a name for the port. Set **Service Port** to 80, **Container Port** to 80, and **Protocol** to **TCP**.|
    |Annotations|Add one or more annotations to the Service and configure Server Load Balancer \(SLB\) parameters. For example, the annotation `service.beta.kubernetes.io/alicloud-loadbalancer-bandwidth:20` specifies that the maximum bandwidth of the Service is 20 Mbit/s. This limits the amount of traffic that flows through the Service. For more information, see [Use annotations to configure load balancing](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Use annotations to configure load balancing.md).|
    |Label|Add one or more labels to the Service. Labels are used to identify the Service.|


## Step 3: Create an Ingress

1.  In the left-side navigation pane of the details page, choose **Network** \> **Ingresses**.

2.  On the Ingresses page, click **Create** in the upper-right corner of the page.

3.  In the **Create** dialog box, set the required parameters and click **Create**.

    The following section describes the key parameters. For more information about how to configure other parameters, see [t16682.md\#section\_g4f\_wex\_ruo](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Basic operations of an Ingress.md).

    -   Name: Enter a name for the Ingress. The name is set to ingress-demo in this example.
    -   Rules: Enter a custom domain name in the **Domain** field and enter the path /nginx in the **Path** field. Set Name in the Services section to the name of the Service that you created in [Step 2: Release the NGINX application](#section_cr5_ms8_nof) and use the automatically matched port 80 for **Port**.
    On the Ingresses page, obtain the IP address of ingress-demo in the **Endpoint** column. Enter the endpoint IP address in the address bar of your browser. If the following page is displayed, it indicates that the Ingress is created.

    ![Nginx](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4943381161/p189883.png)


## Step 4: View the access log of the NGINX application

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, click **Cluster Information**.

5.  On the page that appears, click the **Cluster Resources** tab, find **Log Service Project**, and then click its ID to check the Logstore.

6.  On the Logstores tab, select log-nginxvarlog to view the latest access log of the application.

    ![View log](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4943381161/p189888.png)


