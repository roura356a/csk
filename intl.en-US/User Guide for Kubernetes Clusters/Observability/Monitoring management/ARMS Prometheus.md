---
keyword: [enable ARMS Prometheus in ACK, configure alerts in ARMS Prometheus, customize monitoring metrics in ARMS Prometheus, Use Grafana to display monitoring metrics]
---

# ARMS Prometheus

You can view preset dashboards and performance metrics provided by Container Service for Kubernetes \(ACK\) by using Application Real-Time Monitoring Service \(ARMS\) Prometheus. This topic describes how to enable ARMS Prometheus in ACK, how to configure alerts in ARMS Prometheus, and how to customize monitoring metrics and use Grafana to display monitoring metrics.

ARMS Prometheus is fully compatible with the open source Prometheus ecosystem. It monitors a wide array of components and provides multiple out-of-the-box dashboards. ARMS Prometheus also provides fully managed monitoring services. ARMS Prometheus saves you the effort of managing the underlying services, such as data storage, data presentation, and system operations and maintenance \(O&M\).

Compared with the open source Prometheus, Alibaba Cloud Prometheus Service has the following advantages:

-   Lightweight, stable, and accurate retry mechanism
    -   Compared with the open source Prometheus, the deployment of Alibaba Cloud Prometheus Service is more lightweight. Instead of building a Prometheus monitoring system, you can install the Prometheus agent \(PromAgent\) to monitor your business.

        ![dg_arms_prometheus_advantage](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6735344851/p76745.png)

    -   The open source Prometheus consumes 16 GB to 128 GB of memory. Alibaba Cloud Prometheus Service consumes only 200 MB to 1 GB of memory and 1 CPU core. Alibaba Cloud Prometheus Service provides higher system stability than the open source Prometheus.
    -   The open source Prometheus retrieves data only once, and data may be discarded when it is written to the storage component. Alibaba Cloud Prometheus Service retries multiple times if it fails to retrieve data. It ensures high concurrency when it writes data to the storage component. No data is discarded.
-   Unlimited amount of data
    -   The open source Prometheus can collect data based on up to one million metrics. Alibaba Cloud Prometheus Service can scale its data collection capability based on the number of Kubernetes replicas. Collection tasks can be distributed across replicas.
    -   The maximum storage capacity of the open source Prometheus is limited by the size of the local disk. Alibaba Cloud Prometheus Service uses the central cloud storage service. The storage capacity is not limited.
-   Compatibility with open source systems
    -   Alibaba Cloud Prometheus Service is compatible with the clients and query languages in the open source Prometheus ecosystem. Alibaba Cloud Prometheus Service provides optimized collection rules and usage values.

        ![dg_arms_prometheus_ecology](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6735344851/p76971.png)

    -   Alibaba Cloud Prometheus Service is compatible with three mainstream collection rules: the open source prometheus.yaml configuration file, ServiceMonitor, and the default collection rule named Annotation. ServiceMonitor is suitable for the monitoring of custom Kubernetes clusters. Compared with the open source Prometheus, Alibaba Cloud Prometheus Service allows you to update collection rules by using the prometheus.yaml configuration file. You do not need to write multiple lines of code in the Deployment file. You only need to add the following three Annotations.

        ```
        prometheus.io/scrape: "true"
        prometheus.io/port: "9090"
        prometheus.io/path: "/metrics"
        ```

    -   Alibaba Cloud Prometheus Service allows you to visualize data by using Grafana. By configuring the Prometheus HTTP API URL, you can implement multi-tenant isolation for the data source in Grafana and multi-tenant isolation for the Grafana dashboard. Alibaba Cloud Prometheus Service is also compatible with the Explore data debugging module of Grafana.
    -   Alibaba Cloud Prometheus Service is compatible with the HTTP API module of the open source Prometheus. It supports three standard API operations: query, query\_range, and labelValues. In addition, you can add /userId/clusterId/regionId/ to the data URL to achieve multi-tenant isolation.
    -   Alibaba Cloud Prometheus Service uses the built-in alerting system of Application Real-Time Monitoring Service \(ARMS\) and is compatible with the alert rules of the open source Prometheus.
-   Cost-effectiveness
    -   Alibaba Cloud Prometheus Service supports the default Kubernetes monitoring. After you install the default Kubernetes monitoring, Alibaba Cloud Prometheus Service automatically creates exporters, collection rules, Grafana dashboards, and ARMS alerts. Compared with the open source Prometheus, Alibaba Cloud Prometheus Service reduces your time cost from about 3 days to about 10 minutes.

        ![Dashboard 1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8885468061/p103080.png)

    -   Alibaba Cloud Prometheus Service supports the monitoring of open source components. You only need to enter the AccessKey ID and AccessKey secret of your Alibaba Cloud account, and the accounts and passwords of the RDS and Redis components. Alibaba Cloud Prometheus Service creates exporters and dashboards for these components. Compared with the open source Prometheus, Alibaba Cloud Prometheus Service reduces your time cost from about 7 days to about 3 minutes.

        ![Dashboard 2](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8885468061/p103081.png)

    -   Alibaba Cloud Prometheus Service supports easy installation and removal. You can debug the service by performing health checks. Compared with the open source Prometheus, Alibaba Cloud Prometheus Service reduces your time cost from about 1 day to about 3 minutes.

For more information about ARMS Prometheus, see [ARMS Prometheus overview]().

## Enable ARMS Prometheus

You can use one of the following methods to enable ARMS Prometheus:

**Configure cluster parameters.**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  In the upper-right corner of the Clusters page, click **Create Kubernetes Cluster**.

4.  Select the cluster template that you want to use and configure parameters for the new cluster. On the **Component Configurations** page, select **Enable Prometheus Monitoring**.

    ![Component configurations](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3432719161/p248500.png)

    For more information about how to create a cluster, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).

    **Note:** By default, **Enable Prometheus Monitoring** is selected when you create a cluster.

    After the cluster is created, the system automatically configures ARMS Prometheus.


**Enable ARMS Prometheus on the Prometheus Monitoring page in the ACK console.**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the cluster management page, choose **Operations** \> **Prometheus Monitoring**.

5.  In the middle of the **Prometheus Monitoring** page, click **Install**.


**Enable the feature on the App Catalog page in the ACK console.**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, choose **Marketplace** \> **App Catalog**.

3.  On the App Catalog page, click the **Alibaba Cloud Apps** tab. Then, find and click **ack-arms-prometheus**.

4.  On the App Catalog - ack-arms-prometheus page, click the **Parameters** tab.

5.  In the **Deploy** section of the App Catalog - ack-arms-prometheus page, select the cluster for which you want to enable ARMS Prometheus, and click **Create**.

    **Note:** By default, **Namespace** and **Release Name** are set to **arms-prom**.


**Execution result**

After the installation is complete, the arms-prom page appears. You can view application information on this page.

![arm-prom](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3432719161/p248613.png)

## View ARMS Prometheus dashboards

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Prometheus Monitoring**.

3.  Click the name of the cluster that you want to manage.

    ![View in ARMS console](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3432719161/p245256.png)

4.  On the **Dashboards** page, click the dashboard that you want to view. The Grafana page appears.

    ![Dashboard details](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3432719161/p245261.png)


## Configure alerts in ARMS Prometheus

ARMS allows you to create alerts for monitoring jobs. When alert conditions are met, you can receive alerts through emails, SMS messages, and DingTalk in real time. This helps you detect errors in a proactive manner. When an alert rule is triggered, notifications are sent to the specified contact group. Before you can create a contact group, you must create a contact. When you create a contact, you can specify a mobile number and an email address on which the contact can receive notifications. You can also provide the webhook URL of a DingTalk chatbot that can automatically send alert notifications.

**Note:** To add a DingTalk chatbot as a contact, you must first obtain the webhook URL of the chatbot. For more information, see [t152339.dita\#concept\_106247\_zh](/intl.en-US/Dashboard and Alerting/Configure a DingTalk chatbot to send alert notifications.md).

1.  Log on to the [ARMS console](https://arms-ap-southeast-1.console.aliyun.com/#/home).

2.  In the left-side navigation pane, choose **Alerts** \> **Contacts**.

3.  On the **Contact** tab, click **New contact** in the upper-right corner.

    ![Create Contact](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3432719161/p245266.png)

4.  Configure an alert rule.

    1.  Log on to the [ARMS console](https://prometheus.console.aliyun.com/#/home).

    2.  In the left-side navigation pane, click **Prometheus Monitoring**. In the **K8s** column, click the cluster that you want to manage.

    3.  In the left-side navigation pane, click **Alarm configuration**.

    4.  Select the alert that you want to manage and click **Edit** in the **Actions** column. Modify the PromQL statement and click **OK**. You can also select a preset alert and click **Enable** to enable the alert.

        For more information about how to configure PromQL statements, see [Create ARMS alerts](/intl.en-US/Quick Start/Create ARMS alerts.md).

    **Note:** You can also choose **Alerts** \> **Alert Policies** in the [ARMS console](https://arms-ap-southeast-1.console.aliyun.com/#/home) to manage alerts.

    **Verify the result**

    Perform a manual test to trigger an alert in DingTalk. The following figure shows a sample alert:

    ![Monitoring and alerting](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3432719161/p249833.png)


## Customize monitoring metrics and use Grafana to display monitoring metrics

**Method 1: Use annotations to customize monitoring metrics**

You can add annotations to pod configuration templates to define custom monitoring metrics. The application monitoring component of ARMS uses ARMS Prometheus to automatically obtain these custom monitoring metrics.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

5.  On the Deployments page, create an application.

    1.  Click **Create from Image**.

    2.  On the **Basic Information** wizard page, configure basic parameters and click **Next**.

    3.  Create a web application and open port 5000 for the application.

        In this example, the `yejianhonghong/pindex` image is used.

        ![Container configurations](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2294669161/p245358.png)

    4.  Click **Next**.

    5.  Add annotations that are related to ARMS to the pod.

        The prometheus.io/port annotation specifies the endpoint port that ARMS Prometheus scrapes. The prometheus.io/path annotation specifies the endpoint path that ARMS Prometheus scrapes.

        ![Labels and annotations](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3432719161/p245359.png)

    6.  Click **Create** to create the application.

6.  On the Services page, create a Service.

    1.  In the left-side navigation pane, choose **Services and Ingresses** \> **Services**.

    2.  In the upper-right corner of the Services page, click **Create**.

    3.  Select **Server Load Balancer** and **Public Access** for **Type**.

    4.  Select the application that is created in [Step 4](#step_fez_f3e_d8s) for **Backend**.

    5.  Click **Create** to create the Service.

    For more information, see [Manage Services](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Manage Services.md).

7.  Configure custom performance metrics.

    1.  Log on to the [ARMS console](https://prometheus.console.aliyun.com/#/home).

    2.  In the left-side navigation pane, click **Prometheus Monitoring**. Click the cluster that you want to manage in the **K8s** column.

    3.  In the left-side navigation pane, click **Settings**. Click the **Targets\(beta\)** tab and verify that the custom metrics are configured.

        ![Custom metrics](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3432719161/p245360.png)

8.  Access the public IP address of the Service that is created in [Step 5](#step_x73_rdc_yg6). This increases the value of the following custom metric.

    For more information about metrics, see [Data model](https://prometheus.io/docs/concepts/data_model/).

    ![Increase the value of a custom metric](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3432719161/p245361.png)

9.  Go to the Dashboards page of the ARMS console and click a dashboard to go to the Grafana page. Click **Add panel** in the upper-right corner, select the **Graph** type, and then enter current\_person\_counts for **Metrics**.

10. Save the settings to view the Grafana chart of the custom metric.

    ![Grafana](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3432719161/p245363.png)


**Method 2: Use ServiceMonitors to customize monitoring metrics**

To use ServiceMonitors to customize monitoring metrics, you must add labels to Services. You do not need to add annotations.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  On the Deployments page, create an application.

    1.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

    2.  Click **Create from Image**.

    3.  On the **Basic Information** wizard page, configure basic parameters and click **Next**.

    4.  Create a web application and open port 5000 for the application.

        In this example, the `yejianhonghong/pindex` image is used.

        ![Container configurations](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2294669161/p245358.png)

        Click **Next**.

    5.  Click **Create** to create the application.

5.  On the Services page, create a Service.

    1.  In the left-side navigation pane of the details page, choose **Services and Ingresses** \> **Services**.

    2.  In the upper-right corner of the Services page, click **Create**.

    3.  Select **Server Load Balancer** and **Public Access** for **Type**.

    4.  Select the application that is created in [Step 4](#step_fez_f3e_d8s) for **Backend**.

    5.  Add the following label to the Service.

        This label is used by ServiceMonitor as a selector.

        ![Create a Service](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4432719161/p246409.png)

    6.  Click **Create** to create the Service.

    For more information, see [Manage Services](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Manage Services.md).

6.  Specify the endpoint that ARMS Prometheus scrapes.

    1.  Log on to the [ARMS console](https://prometheus.console.aliyun.com/#/home).

    2.  In the left-side navigation pane, click **Prometheus Monitoring**. In the **K8s** column, click the cluster that you want to manage.

    3.  In the left-side navigation pane, click **Settings**. Then, click the **Service Discovery** tab.

    4.  Click the ServiceMonitor tab. In the upper-right corner, click **Add ServiceMonitor**.

        In this example, the following template is used to create a ServiceMonitor.

        ```
        apiVersion: monitoring.coreos.com/v1
        kind: ServiceMonitor
        metadata:
          #Enter a unique name.
          name: custom-metrics-pindex
          #Specify a namespace.
          namespace: default
        spec:
          endpoints:
          - interval: 30s
            #Enter the name of the port that you specified in the Port Mapping section when you created the Service in Step 5.
            port: web
            #Enter the path of the Service.
            path: /access
          namespaceSelector:
            any: true
            #The namespace of the NGINX demo application.
          selector:
            matchLabels:
              # Enter the label that you added to the Service in Step 5.
              app: custom-metrics-pindex
        ```

        Click **Create** to create the ServiceMonitor.

    5.  On the **Targets\(beta\)** tab, verify that the endpoints that ARMS Prometheus scrapes are displayed.

        ![Scape Endpioint](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4432719161/p246444.png)

        **Note:** The definition of a ServiceMonitor provides more information than an annotation, and includes the namespace and name of the Service.

7.  Access the public IP address of the Service that is created in [Step 5](#step_v29_tra_ics). This increases the value of the following custom metric.

    For more information about metrics, see [Data model](https://prometheus.io/docs/concepts/data_model/).

    ![Increase the value of a custom metric](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3432719161/p245361.png)

8.  Go to the Dashboards page of the ARMS console and click a dashboard to go to the Grafana page. Click **Add panel** in the upper-right corner, select the **Graph** type, and then enter current\_person\_counts for **Metrics**.

9.  Save the settings to view the Grafana chart of the custom metric.

    ![Grafana](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3432719161/p245363.png)


## FAQ

What can I do if I fail to reinstall ARMS Prometheus after I delete the namespace of ARMS Prometheus?

If you delete only the namespace of ARMS Prometheus, resource settings may be retained. In this case, you may fail to reinstall ARMS Prometheus. You can perform the following operations to delete the resource settings:

1.  Run the following commands to delete the related ClusterRoles.

    ```
    kubectl delete ClusterRole arms-kube-state-metricskubectl delete ClusterRole arms-node-exporter
    kubectl delete ClusterRole arms-prom-ack-arms-prometheus-role
    kubectl delete ClusterRole arms-prometheus-oper3
    ```

2.  Run the following commands to delete the related ClusterRoleBindings.

    ```
    kubectl delete ClusterRoleBinding arms-node-exporter
    kubectl delete ClusterRoleBinding arms-prom-ack-arms-prometheus-role-binding
    kubectl delete ClusterRoleBinding arms-prometheus-oper-bind2
    kubectl delete ClusterRoleBinding kube-state-metrics
    ```


**Related topics**  


[View monitoring dashboards]()

[Configure a data collection rule for Prometheus Monitoring]()

