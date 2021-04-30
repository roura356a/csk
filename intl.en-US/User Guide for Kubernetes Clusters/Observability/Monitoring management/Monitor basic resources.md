---
keyword: [Cloud Monitor, resource monitoring]
---

# Monitor basic resources

Resource monitoring is one of the most commonly used monitoring methods in Kubernetes. You can use resource monitoring to check the resource usage of workloads. The resources include CPU, memory, and network resources. Container Service for Kubernetes \(ACK\) integrates Cloud Monitor to provide resource monitoring features. By default, ACK installs the Cloud Monitor agent for new clusters. This topic describes how to monitor basic resources and configure alerts by using the ACK console.

To use the latest Cloud Monitor version, the metrics-server component must be upgraded to V0.3.8.5 or later. For more information, see [Install the metrics-server component](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Upgrade cluster/Install the metrics-server component.md).

## Features

-   Provides comprehensive metrics to help you gain insight into cluster performance.

    ![Cluster overview](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3381579161/p259094.png)

-   Improves monitoring and alerting capabilities.

    Upgrades Cloud Monitor to the latest version to provide professional capabilities of container resource monitoring. Provides monitoring metrics for native Kubernetes objects, such as namespaces, nodes, workloads, and pods. Upgrades the alerting feature and allows you to configure alert rules based on different perspectives.

-   Provides appropriate metrics for different monitoring scenarios.

    Supports the most appropriate metrics for different scenarios, such as the host infrastructure layer, container layer in Platform as a Service \(PaaS\), and Kubernetes scheduling layer. For example, the memory metrics that affect Kubernetes scheduling in containers are dedicated to the working memory of containers. This helps distinguish container memory usage from host memory usage.


## Go to Resource Monitoring

**Method 1: Go to Resource Monitoring by using the ACK console**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

5.  On the Deployments page, find the application that you want to monitor and click **Monitor** in the **Actions** column to go to the Container Service Monitoring page.

    ![Container Service Monitoring page](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4381579161/p258867.png)

6.  You can click the **Deployment Application**, **Container group list**, and **Container group hotspot** tabs to view monitoring data.


**Method 2: Go to Resource Monitoring by using the Cloud Monitor console**

1.  Log on to the [Cloud Monitor console](https://cms-intl.console.aliyun.com).

2.  In the left-side navigation pane, click **Container Service Monitoring**.

3.  On the Container Service Monitoring page, find the cluster that you want to monitor and click the name of the cluster or click **View the Detail** in the **Actions** column.

    **Note:** The first time you visit the page, a message appears and requires you to perform authorization. You must click **Authorize** to complete authorization before you can go to the details page.

4.  On the page that appears, you can click **Cluster overview**, **Node**, **Namespace**, and **Workload** to view application monitoring data from different perspectives.

    ![Cluster monitoring](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4381579161/p258924.png)


## Configure alerts based on scenarios

|Scenario|Description|How to configure|
|--------|-----------|----------------|
|Monitor the health status of the cluster and send alerts on resource usage exceptions in the cluster or nodes.|When resource usage exceptions occur in the cluster or nodes, alerts need to be sent at the earliest opportunity to prevent service interruptions. We recommend that you configure alert rules to monitor the resource usage of the entire cluster or all nodes in the cluster.|When you create an alert rule, set **Resource Range** to **Cluster** or **Node**. This allows you to detect abnormal metrics in the entire cluster or any node in the cluster. If you set Resource Range to Node, make sure that you select **All** nodes. This triggers an alert when an abnormal value of the metric specified in Rule Description is detected in any node in the cluster.|
|Monitor the resource usage of pods and send alerts on any pod in the cluster.|When a resource usage exception occurs in the cluster, the exception need to be analyzed to find the pod that causes the problem. We recommend that you configure alert rules to monitor the resource usage of all pods in the cluster.|When you create an alert rule, set **Resource Range** to **Container Group \(pod\)** and set both Namespace and Container Group \(pod\) to **All**. This triggers an alert when an abnormal value of the metric specified in Rule Description is detected in any pod in the cluster.|
|Monitor the cluster by namespace and send alerts on pods in a specified namespace in the cluster.|In most cases, a cluster is shared among multiple applications. Namespaces provide a commonly used method to isolate applications in a multi-tenant environment. When a resource usage exception occurs in an application of a specified namespace, alerts need to be sent at the earliest opportunity. We recommend that you configure alert rules to monitor the resource usage of all pods in a specified namespace in the cluster.|When you create an alert rule, set **Resource Range** to **Container Group \(pod\)**, set **Namespace** to the one where your application belongs, and set **Container Group \(pod\)** to **All**. This triggers an alert when an abnormal value of the metric specified in Rule Description is detected in any pod in the specified namespace.|
|Monitor the resource usage of applications and send alerts on pods of a specified workload in a specified namespace.|In most cases, a cluster is shared among multiple applications. Workloads provide a commonly used method to isolate applications in a multi-tenant environment. For example, an application may be run as a Deployment. When a resource usage exception occurs in a Deployment of a specified application, alerts need to be sent at the earliest opportunity. We recommend that you configure alert rules to monitor the resource usage of all pods of a specified workload.|When you create an alert rule, set **Resource Range** to **Container Group \(pod\)**, set **Namespace** to the one where your application belongs, and select the workload type of your application. The following types of workload are supported: Deployment, StatefulSet, DaemonSet, Job, and CronJob. Set **Container Group \(pod\)** to All. This triggers an alert when an abnormal value of the metric specified in Rule Description is detected in any pod of the specified workload.|

## Configure alert rules

**Step 1: Create an alert contact and add it to an alert contact group**

1.  Log on to the [Cloud Monitor console](https://cms-intl.console.aliyun.com).

2.  In the left-side navigation pane, choose **Alerts** \> **Alert Contacts**.

3.  Create an alert contact and add it to an alert contact group.

    For more information, see [Create an alert contact or alert group](/intl.en-US/Alarm service/Alert contacts/Create an alert contact or alert group.md).


**Step 2: Create an alert rule**

1.  Log on to the [Cloud Monitor console](https://cms-intl.console.aliyun.com).

2.  In the left-side navigation pane, click **Container Service Monitoring**.

3.  On the Container Service Monitoring page, select the cluster for which you want to create an alert rule and click **View Alert Rules** in the **Actions** column.

4.  On the Alert Rules page, click **Create Alert Rule**.

5.  In the Create Alert Rule panel, configure the parameters.

    |Parameter|Description|
    |---------|-----------|
    |**Resource Range**|The resources to which the alert rule is applied. Valid values:    -   **Cluster**: The alert rule is applied to the cluster.
    -   **Node**: The alert rule is applied to all nodes or specified nodes in the cluster.
    -   **Container Group \(pod\)**: The alert rule is applied to all pods or specified pods in the specified application under the specified namespace of the cluster. If you select **All**, an alert is triggered when the metric of any pod exceeds the threshold specified in Rule Description. |
    |**Rule Description**|The content of the alert rule. Configure the metric, threshold, and alert level. For more information about pod metrics, see [ACK \(new version\)](/intl.en-US/Appendix 1: Metrics/Cloud essentials/Elastic computing/ACK (new version).md).

The parameters in this section specify the conditions that trigger an alert. |
    |**Effective Time**|The interval at which new alert notifications are sent if the alert is not cleared.|
    |**Effective From**|The time period during which the alert rule is effective. Cloud Monitor checks whether the monitoring data meets the alert rule only during the effective period.|
    |**HTTP Callback**|Cloud Monitor sends a POST request to push an alert to the specified callback URL. Only HTTP requests are supported. **Note:** We recommend that you specify a callback URL that can be accessed over the Internet. |
    |**Alert Contact Group**|The alert contact group that receives alert notifications.|

6.  Click **OK** to create the alert rule.

    On the Alert Rules page, verify that the new alert rule is displayed.


**Verification**

1.  In the left-side navigation pane, choose **Alerts** \> **Alert Logs**.

2.  On the Alert History page, you can view alert trends and details of alert history.

    ![Alert History](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0553579161/p261210.png)


## Previous Resource Monitoring page

If the metrics-server component of your cluster is not upgraded to V0.3.8.5 or later, you can perform the following steps to go to the previous Resource Monitoring page:

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

3.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

4.  Select the Deployment that you want to monitor and click **Monitor** in the Actions column. Then, you are redirected to the Dashboards page in the Cloud Monitor console.

5.  You can click the **Deployment Application**, **Container group list**, and **Container group hotspot** tabs to view monitoring data.

6.  To configure alerts, choose **Alerts** \> **Alert Rules** in the left-side navigation pane.

    The name of a group-based metric starts with group and the name of an instance-based metric starts with pod. For more information, see [Manage alert rules](/intl.en-US/Application groups/Manage alert rules.md).


