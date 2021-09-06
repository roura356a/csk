---
keyword: FAQ about ack-arms-prometheus
---

# FAQ about ack-arms-prometheus

The ack-arms-prometheus component is a resource monitoring tool used by Application Real-Time Monitoring Service \(ARMS\) Prometheus to monitor Container Service for Kubernetes \(ACK\) clusters. This topic provides answers to some frequently asked questions about ack-arms-prometheus installation.

## What do I do if I failed to install ack-arms-prometheus after the Component Not Installed message is returned?

-   Check whether ack-arms-prometheus is already installed.
    1.  Log on to the [ACK console](https://cs.console.aliyun.com).
    2.  In the left-side navigation pane of the ACK console, click **Clusters**.
    3.  On the Clusters page, find the cluster that you want to manage and click the name or click **Details** in the **Actions** column.
    4.  In the left-side navigation pane of the cluster details page, choose **Applications** \> **Helm**.

        On the **Helm** page, check whether ack-arms-prometheus is displayed.

        -   If ack-arms-prometheus is displayed on the **Helm** page, perform the following steps:

            Delete ack-arms-prometheus on the **Helm** page and then install ack-arms-prometheus on the **Add-ons** page. For more information about how to install ack-arms-prometheus, see [Manage system components](/intl.en-US/User Guide for Kubernetes Clusters/Component/Manage system components.md).

        -   If ack-arms-prometheus is not displayed on the **Helm** page, perform the following steps:
            1.  If ack-arms-prometheus is not displayed on the Helm page, it indicates that residual data exists after ack-arms-prometheus is deleted. You must delete the residual data. For more information about how to delete residual data about ack-arms-prometheus, see [FAQ about ARMS Prometheus](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Monitoring management/Enable ARMS Prometheus.md).
            2.  Install ack-arms-prometheus on the **Add-ons** page. For more information about how to install ack-arms-prometheus, see [Manage system components](/intl.en-US/User Guide for Kubernetes Clusters/Component/Manage system components.md).
            3.  If ack-arms-prometheus still cannot be installed after you perform the preceding steps,[submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).
-   Check whether the arms-prometheus-ack-arms-prometheus log shows errors.
    1.  Log on to the [ACK console](https://cs.console.aliyun.com).
    2.  In the left-side navigation pane of the ACK console, click **Clusters**.
    3.  On the Clusters page, find the cluster that you want to manage and click the name or click **Details** in the **Actions** column.
    4.  In the left-side navigation pane of the cluster details page, choose **Workloads** \> **Deployments**.
    5.  In the upper part of the **Deployments** page, set **Namespace** to **arms-prom** and then click arms-prometheus-ack-arms-prometheus.
    6.  Click the **Logs** tab and check whether errors are found in the log.

        If errors are reported in the log, [submit a ticket](https://selfservice.console.aliyun.com/ticket/category/arms).

-   Check whether installation errors are reported by the ARMS agent.
    1.  Log on to the [ARMS console](https://arms-intl.console.aliyun.com/).
    2.  In the top navigation bar, select the region where your cluster is deployed.
    3.  In the left-side navigation pane, click **Prometheus Monitoring**.
    4.  On the **Prometheus Monitoring** page, click the name of your cluster.
    5.  In the left-side navigation pane, click **Settings**. On the Settings page, click the **Agent Settings** tab.

        On the **Agent Settings** tab, you can find the health check result. If an error is reported, [submit a ticket](https://selfservice.console.aliyun.com/ticket/category/arms).


## What do I do if the xxx in use error occurs when I install ack-arms-prometheus?

1.  Log on to the [ACK console](https://cs.console.aliyun.com).
2.  In the left-side navigation pane of the ACK console, click **Clusters**.
3.  On the Clusters page, find the cluster that you want to manage and click the name or click **Details** in the **Actions** column.
4.  In the left-side navigation pane of the cluster details page, choose **Applications** \> **Helm**.

    On the **Helm** page, check whether ack-arms-prometheus is displayed.

    -   If ack-arms-prometheus is displayed on the **Helm** page, perform the following steps:

        Delete ack-arms-prometheus on the **Helm** page and then install ack-arms-prometheus on the **Add-ons** page. For more information about how to install ack-arms-prometheus, see [Manage system components](/intl.en-US/User Guide for Kubernetes Clusters/Component/Manage system components.md).

    -   If ack-arms-prometheus is not displayed on the **Helm** page, perform the following steps:
        1.  If ack-arms-prometheus is not displayed on the Helm page, it indicates that residual data exists after ack-arms-prometheus is deleted. You must delete the residual data. For more information about how to delete residual data about ack-arms-prometheus, see [FAQ about ARMS Prometheus](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Monitoring management/Enable ARMS Prometheus.md).
        2.  Install ack-arms-prometheus on the **Add-ons** page. For more information about how to install ack-arms-prometheus, see [Manage system components](/intl.en-US/User Guide for Kubernetes Clusters/Component/Manage system components.md).
        3.  If ack-arms-prometheus still cannot be installed after you perform the preceding steps,[submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).

