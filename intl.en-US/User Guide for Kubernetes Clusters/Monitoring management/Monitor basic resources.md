---
keyword: [Cloud Monitor, resource monitoring, deployments]
---

# Monitor basic resources

Resource monitoring is one of the most commonly used monitoring methods in Container Service for Kubernetes \(ACK\). Resource monitoring allows you to view the resource usage of workloads. The resources include CPU, memory, and network resources. ACK integrates Cloud Monitor to monitor resources. By default, ACK installs Cloud Monitor for all new clusters. This topic describes how to view monitoring data of basic resources in the console.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

3.  In the left-side navigation pane, click **Workload**.

4.  On the page that appears, select a namespace from the **Namespace** drop-down list and click the **Deployments** tab.

5.  Select a deployment and click **Monitor** in the Actions column. Then, you are redirected to the Dashboards page in the Cloud Monitor console.

6.  In the **Dimensions** field, select **Group Name** or **Instances**. You can view monitoring data by group or instance.

7.  To set alert rules, click **Alert Rule** in the left-side navigation pane. For more information, see [Manage alert rules](/intl.en-US/Application groups/Manage alert rules.md).

    The name of a group-based metric starts with group and the name of an instance-based metric starts with pod.

    ![Configure an alert rule](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4762934061/p143914.png)


