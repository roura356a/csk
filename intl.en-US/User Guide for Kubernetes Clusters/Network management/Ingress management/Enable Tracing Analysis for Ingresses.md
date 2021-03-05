---
keyword: [Ingress-tracing, Tracing Analysis, trace]
---

# Enable Tracing Analysis for Ingresses

You can enable Tracing Analysis for Ingresses in an Container Service for Kubernetes \(ACK\) cluster. Tracing data is imported to the Tracing Analysis console. You can view traces and the trace topology in the Tracing Analysis console. This topic describes how to enable Tracing Analysis for Ingresses.

-   [Activate related services and grant required permissions](/intl.en-US/Before you begin/Activate related services and grant required permissions.md)
-   [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)

Tracing Analysis provides various features for distributed applications, including trace mapping, request counting, and trace topology. These features allow you to analyze and diagnose the performance bottlenecks of distributed applications, and improve the efficiency of microservice development and diagnostics. You can enable Tracing Analysis for NGINX Ingress controllers that are provided by ACK based on your requirements. After Tracing Analysis is enabled, you can view the tracing data.

## Step 1: Obtain an endpoint

Obtain an endpoint. Select a client based on your requirements. In this example, a Zipkin client is used.

1.  Log on [Tracing Analysis console](https://tracing-sg.console.aliyun.com/).

2.  In the left-side navigation pane, click **Cluster Configurations**.

3.  On the Cluster Configurations page, click the **Access point information** tab. Then, turn on **Show Token** for the Cluster Information parameter.

4.  Set the Client parameter to **Zipkin**.

5.  In the Related Information column of the table in the lower part, click the copy icon next to the endpoint that you want to use.

    ![Endpoint: Zipkin](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8324484161/p188458.png)

    **Note:** If you deploy your application in an Alibaba Cloud production environment, select a private endpoint. Otherwise, select a public endpoint.


## Step 2: Enable Tracing Analysis for Ingresses

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Configurations** \> **ConfigMaps**.

5.  On the ConfigMap page, enter nginx-configuration into the Name search box and click the search icon. Find the nginx-configuration ConfigMap and click **Edit** in the **Actions** column.

6.  Configure Tracing Analysis.

    Edit the ConfigMap based on the client that is used. In this example, a Zipkin client is used.

    In the **Edit** panel, click **Add**. Set **Name** to zipkin-collector-host. Set **Value** to the endpoint that is obtained in [Step 1: Obtain an endpoint](#section_8f8_sdn_c03).

7.  Enable Tracing Analysis.

    Click **Add**. Set **Name** to enable-opentracing. Set **Value** to true. Then, click **OK**.


## Step 3: View the tracing data

1.  Log on [Tracing Analysis console](https://tracing-sg.console.aliyun.com/).

2.  In the left-side navigation pane, click **Applications**.

3.  On the Applications page, select a region at the top of the page and click the application that you want to manage.

4.  In the left-side navigation pane of the details page, click **Interface Calls**. On the page that appears, click the Traces tab. You can view up to 100 traces on the tab. These traces are sorted in descending order of elapsed time. For more information about the tracing data, see [View the details of span calls](/intl.en-US/Operations in the Console/Application management/View the details of span calls.md).


