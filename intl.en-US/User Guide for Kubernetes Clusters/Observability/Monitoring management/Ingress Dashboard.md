# Ingress Dashboard

Ingress controllers of Container Service for Kubernetes \(ACK\) allow you to stream all HTTP request logs to standard outputs. ACK is also integrated with Log Service. You can create dashboards to monitor and analyze logs. This topic describes how to use Ingress Dashboard with Application Real-Time Monitoring Service \(ARMS\) to monitor applications.

You must install the Log Service component before you can use Ingress Dashboard to monitor applications. You can use the following methods to install the component:

-   When you create an ACK cluster, select **Enable Log Service**, **Install Ingress Controllers**, and **Create Ingress Dashboard**.
-   To install the component for an existing ACK cluster, go to the Add-ons page. For more information, see [Use Log Service to collect container logs](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Log management/Use Log Service to collect container logs.md).

## Step 1: View Ingress logs on dashboards

1.  Log on to the [Log Service console](https://sls.console.aliyun.com).

2.  In the **Projects** section, click the name of the project that you want to manage. The details page of the project appears. By default, a project for a Kubernetes cluster is named in the format of **k8s-log-\{cluster-id\}**.

3.  On the page that appears, click the ![Dashboard](../images/p201103.png) icon on the left side of**nginx-ingress** on the **Logstores** tag.

    **Note:** All Ingress access logs are stored in the **nginx-ingress** Logstore.

4.  Click **Visual Dashboard** below **nginx-ingress** to view the dashboards of all Ingresses.

    Ingress Dashboard contains five preset charts: Ingress Overview, Ingress Exceptions Center, Ingress Access Center, Ingress Monitoring Center for Blue/Green Deployment, and Ingress Monitoring Center. The following section introduces the Ingress Overview and Ingress Monitoring Center for Blue/Green Deployment charts. For more information about other preset charts, see [Monitor and analyze the logs of nginx-ingress](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Ingress management/Monitor and analyze the logs of nginx-ingress.md).

    **Ingress Overview**

    The Ingress Overview dashboard displays the data of a website that is monitored by nginx-ingress. You can view the following information:

    -   Website data of the last 24 hours, including the number of page views \(PVs\), the number of unique visitors \(UVs\), inbound and outbound traffic, the average latency, the proportion of mobile terminal users, and the proportions of 5xx errors and 404 errors.
    -   Website data of the last one minute, including the number of PVs, the number of UVs, the success rate of requests, the proportion of 5xx errors, the average latency, the P95 latency, and the P99 latency.
    -   Detailed information about requests in the last 24 hours, including the PV trend \(compared with the PV trend in the last seven days\), regional distribution of request sources, the top 10 source areas and cities, the proportion of mobile terminal users, and the proportions of Android terminal users and iOS terminal users.
    -   Top 10 URLs in the last one hour, including the 10 URLs of the highest PVs, the 10 URLs of the highest latencies, the 10 URLs that return the most 5xx errors, and the 10 URLs that return the most 404 errors.
    ![Ingress Overview](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2445359951/p40696.png)

    **Ingress Monitoring Center for Blue/Green Deployment**

    The Ingress Monitoring Center for Blue/Green Deployment dashboard displays the real-time status of a service version release and compares the specified service versions. This allows you to identify exceptions and roll back the service at the earliest opportunity. You must set **ServiceA** and **ServiceB** for monitoring and comparison. The dashboard displays the following dynamic monitoring data of each service: the number of PVs, the proportion of 5xx errors, the success rate of requests, the average latency, the P95, P99, and P9999 latencies, and the traffic data.

    ![Ingress Monitoring Center for Blue/Green Deployment](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2445359951/p40706.png)


## Enable ARMS for Java applications

To enable ARMS when you create an application, perform the following steps:

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster, or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

5.  On the Deployments page, click **Create from Template** in the upper-right corner of the page.

6.  On the Create page, select a template from the **Sample Template** drop-down list and add the following `annotations` to the spec \> template \> metadata section in the **template editor**.

    **Note:** Replace <your-deployment-name\> with the name of your application.

    ```
    annotations:
      armsPilotAutoEnable: "on"
      armsPilotCreateAppName: "<your-deployment-name>"                                
    ```

    ![YAML Example](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5354934061/p53707.png)

    The following YAML template shows how to create a stateless application and enable ARMS for the application.


## Step 3: Create an Ingress for the application

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster, or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  On the **Ingresses** page, click **Create** in the upper-right corner of the page.

5.  In the **Create** dialog box, configure the Ingress and click **Create**.

    For more information about how to configure an Ingress, see [Create an Ingress](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Ingress management/Create an Ingress.md).


## Step 4: Use Ingress Dashboard with ARMS

1.  Log on to the [Log Service console](https://sls.console.aliyun.com).

2.  In the **Projects** section, click the name of the project that you specified when you created the ACK cluster. The details page of the project appears. By default, a project specified for an ACK cluster is named in the format of **k8s-log-\{cluster-id\}**.

3.  On the details page, you are redirected to the Logstores tab by default. All Ingress access logs are stored in the **nginx-ingress** Logstore. In the left-side navigation pane, click **Visual Dashboards**.

4.  In the dashboard list, click **Ingress Overview V1.2**.

5.  On the **Ingress Overview V1.2** page, click **Top 10 URLs by Latency** to view the top 10 URLs with the highest latency.

6.  On the **Ingress Overview V1.2** page, click **URL \(ARMS\)** to view the monitoring data about the corresponding Service. You can view the trace details of a specific Service in the ARMS console.


