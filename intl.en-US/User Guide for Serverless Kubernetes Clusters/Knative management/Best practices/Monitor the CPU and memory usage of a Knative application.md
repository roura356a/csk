# Monitor the CPU and memory usage of a Knative application

You can monitor the CPU and memory usage of a Knative application. This topic describes how to configure the monitoring of these items.

-   Knative is enabled for a serverless Kubernetes \(ASK\) cluster. For more information, see [Deploy Knative in an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Knative management/Manage Knative components/One-click Deploy Knative in an ASK cluster.md).
-   Prometheus Monitoring is enabled for the ASK cluster. For more information, see [Enable ARMS Prometheus](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Monitoring management/Enable ARMS Prometheus.md).

## Procedure

1.  Log on to the [ARMS console](https://arms-ap-southeast-1.console.aliyun.com/#/home).

2.  In the left-side navigation pane, click **Prometheus Monitoring** and click the ASK cluster that you want to manage.

    **Note:** If you have not installed Prometheus, click **Install** on the Prometheus Monitoring page.

    ![98-ch](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5092234161/p207447.png)

3.  On the Dashboards page, click **Prometheus** in the **Name** column.

4.  On the left side of the page that appears, click the **Explore** icon and select the ASK cluster from the **Select datasource** drop-down list.

    ![99](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5092234161/p207728.png)

5.  Monitor the CPU usage of a Knative application.

    Enter the following PromQL statement into the **Metrics** text box. In this example, the **helloworld-go** application is queried. Click **Run Query** to query the CPU usage of the application.

    ```
    sum (rate (container_cpu_usage_seconds_total{pod_name=~"helloworld-go.*",namespace="default"}[1m])) 
    ```

    ![100](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5092234161/p207740.png)

6.  Monitor the memory usage of a Knative application.

    Enter the following PromQL statement into the **Metrics** text box. In this example, the **helloworld-go** application is queried. Click **Run Query** to query the memory usage of the application.

    ```
    sum (rate (container_memory_working_set_bytes{pod_name=~"helloworld-go.*",namespace="default"}[1m])) 
    ```

    ![101](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5092234161/p207742.png)


