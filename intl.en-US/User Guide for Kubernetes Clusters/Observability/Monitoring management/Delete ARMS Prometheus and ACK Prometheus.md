# Delete ARMS Prometheus and ACK Prometheus

This topic describes how to uninstall Application Real-Time Monitoring Service \(ARMS\) Prometheus and Container Service for Kubernetes \(ACK\) Prometheus.

## Procedure

You can uninstall ARMS Prometheus and ACK Prometheus in the ACK console. All data of ARMS Prometheus and ACK Prometheus is uninstalled. The procedures for deleting ARMS Prometheus and ACK Prometheus are similar. In the following example, ARMS Prometheus is uninstalled.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster, or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Applications** \> **Helm**.

5.  On the Helm page, find the component whose **Release Name** is arms-prom. Then, click **Delete** in the Actions column.

    **Note:** If you want to uninstall ACK Prometheus, navigate to the Helm page and find the component whose **Release Name** is ack-prometheus-operator. Then, click **Delete** in the **Actions** column.

6.  In the **Delete** dialog box, select **Clear Release Records** and click **OK**.


