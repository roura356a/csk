---
keyword: [Knative, Prometheus, concurrent requests for pods]
---

# Use ARMS Prometheus to collect pod request-related metrics from a Knative Service

In practical scenarios, the number of concurrent pod requests is commonly used to evaluate a Service. Knative collects request-related metrics by using the queue-proxy container. This topic describes how to use Application Real-Time Monitoring Service \(ARMS\) Prometheus to collect pod request-related metrics from a Knative Service.

## Step 1: Install the Prometheus Monitoring component

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, choose **Marketplace** \> **App Catalog**.

3.  On the App Catalog page, click the **Alibaba Cloud Apps** tab. Then, find and click **ack-arms-prometheus**.

4.  On the App Catalog - ack-arms-prometheus page, click the **Parameters** tab.

5.  In the **Deploy** section of the App Catalog - ack-arms-prometheus page, select the cluster for which you want to enable ARMS Prometheus, and click **Create**.

    **Note:** By default, **Namespace** and **Release Name** are set to **arms-prom**.


## Step 2: Set collection job rules for the queue-proxy container

1.  Log on to the[ARMS console](https://arms-intl.console.aliyun.com/).

2.  In the left-side navigation pane, click **Prometheus Monitoring**.

3.  On the Prometheus Monitoring page, click **Settings** in the **Actions** column.

4.  Click the **Prometheus Settings** tab, add the following content to the Prometheus.yaml file, and click **Save**.

    ```
    global:  
      scrape_interval: 30s
      scrape_timeout: 10s
      evaluation_interval: 30s
    scrape_configs:
    - job_name: queue-proxy
      scrape_interval: 3s
      scrape_timeout: 3s
      kubernetes_sd_configs:
      - role: pod
      relabel_configs:
      # Rename metadata labels to be reader friendly
      - source_labels: [__meta_kubernetes_pod_label_serving_knative_dev_revision, __meta_kubernetes_pod_container_port_name]
        action: keep
        regex: .+;http-autometric
      - source_labels: [__meta_kubernetes_namespace]
        target_label: namespace
      - source_labels: [__meta_kubernetes_pod_name]
        target_label: pod
      - source_labels: [__meta_kubernetes_service_name]
        target_label: service
    ```

5.  Click the **Metrics** tab to view the metrics related to the queue-proxy.

    ![Metrics](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4165359951/p128175.png)


## Step 3: Query the number of concurrent pod requests

1.  In the left-side navigation pane of the cluster configuration page, click **Dashboards**.

2.  On the Dashboards page, click **Prometheus** in the Name column.

3.  On the left side of the Prometheus page, click the **Explore** icon.

    ![explore](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3142896161/p128189.png)

4.  In the **Metrics** drop-down list, select queue to view metrics related to queue-proxy.

    The **queue\_requests\_per\_second** metric is collected in this example.

    ![metrics](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4165359951/p128193.png)

    You can also query the number of concurrent pod requests per second by specifying the namespace, revision, and pod name. The following code block is an example:

    ```
    queue_requests_per_second{destination_configuration="helloworld-go",destination_namespace="default",destination_pod="helloworld-go-ttf52-deployment-5778d86bd6-dnxw2"}
    ```

    ![Number of concurrent pod requests](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4165359951/p128195.png)


