---
keyword: [Knative components, alert, Prometheus Monitoring]
---

# Configure alerts for Knative components

You can use Prometheus Monitoring in Application Real-Time Monitoring Service \(ARMS\) to collect metrics of Knative components. This topic describes how to configure alerts for Knative components.

The following metrics of Knative components are collected:

-   Number of ready pods where the components are deployed.
-   CPU usage of the pods where the components are deployed.
-   Memory usage of the pods where the components are deployed.

Knative components include:

-   knative-serving
    -   activator
    -   autoscaler
    -   autoscaler-hpa
    -   controller
    -   webhook
-   knative-eventing
    -   eventing-controller
    -   eventing-webhook

## Step 1: Install the Prometheus Monitoring component

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, choose **Marketplace** \> **App Catalog**.

3.  On the App Catalog page, click the**Alibaba Cloud Apps** tab and click **ack-arms-prometheus**.

4.  On the App Catalog - ack-arms-prometheus page, click the **Parameters** tab.

5.  On the **Parameters** tab, set parameter cluster\_id.

    **Note:**

    The system automatically sets the value to the ID of the cluster that you create. You can perform the following steps to view the cluster ID: In the left-side navigation pane of the ACK console, choose **Clusters** \> **Clusters**. On the page that appears, the cluster ID is displayed below each cluster name.

6.  In the **Deploy** section of the App Catalog - ack-arms-prometheus page, select the cluster for which you want to enable ARMS Prometheus, and click **Create**.

    **Note:** By default, **Namespace** and **Release Name** are set to **arms-prom**.


## Step 2: View pod monitoring information

Before you set an alert policy for a pod where the components are deployed, check the monitoring information about the pod.

1.  Log on to the[ARMS console](https://arms-intl.console.aliyun.com/).

2.  In the left-side navigation pane, click **Prometheus Monitoring**.

3.  On the Prometheus Monitoring page, click **k8s state** in the **Installed Plugins** column. On the page that appears, you can view the number of pods, the CPU usage of the pods, and the memory usage of the pods.


## Step 3: Set alert policies for Knative components



1.  You can select one of the two available methods to go to the Create Alarm page.

    -   On the [Prometheus Grafana dashboard](http://grafana.console.aliyun.com/) page of the NewDashBoard, click And jump to the Prometheus create alarm dialog box.
    -   In the left-side navigation pane of the arms console, choose **Alarm management** \> **Alert policy management** On the alert policies page. Click **Create alarms** \> **Prometheus**.
2.  In the Create Alarm dialog box, set the parameters.

    The activator component of Knative Serving is used in this example. If the number of ready pods is less than the specified number, an alert is triggered. In this case, the number of unavailable pods is greater than or equal to one. The following figure shows the alert policy.

    ![Alert policy](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1065359951/p128009.png)

    PromQL queries for the activator component:

    ```
    ((sum(kube_deployment_status_replicas{deployment=~"activator"}) or vector(0))) - ((sum(kube_deployment_status_replicas_available{deployment=~"activator"}) or vector(0)))
    ```

    For more information, see [Create an alert](/intl.en-US/Dashboard and alerting/Create an alert.md).

3.  Click **Save**.


