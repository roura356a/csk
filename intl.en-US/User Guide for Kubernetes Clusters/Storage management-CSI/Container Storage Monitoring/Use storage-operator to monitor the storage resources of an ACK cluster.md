---
keyword: [monitor storage resources of an ACK cluster, install storage-operator]
---

# Use storage-operator to monitor the storage resources of an ACK cluster

You can deploy storage-operator in a Container Service for Kubernetes \(ACK\) cluster. After storage-operator is deployed, you can view the status of persistent volumes \(PVs\) and persistent volume claims \(PVCs\), the trend of PV and PVC status, and the PVs and PVCs that are in the abnormal state. This topic describes how to monitor the storage resources of an ACK cluster by using storage-operator.

An ACK cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).

## Step 1: Install storage-operator

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Operations** \> **Add-ons**.

5.  Click the Storage tab, find storage-operator, and then click **Install**.


## Step 2: View monitoring data

Only PVs and PVCs can be monitored. You can view the monitoring data in Prometheus Service.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Operations** \> **Prometheus Monitoring**.

    If storage-operator is not installed, click **Install** to install the component.

5.  Click the **CSI Cluster** tab to go to the monitoring dashboards of the CSI plug-in. You can view the monitoring data in the dashboards.

    ![K8s csi cluster](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3176051261/p251927.png)

    The following table describes the metrics displayed in the dashboards on the CSI Cluster tab.

    |Metric|Description|Dimension|
    |------|-----------|---------|
    |PV Status Statistics|Distribution of PV status.|Quantity and proportion.|
    |PVC Status Statistics|Distribution of PVC status.|Quantity and proportion.|
    |PV Total Num|Trend of PV status.|Quantity.|
    |PVC Total Num|Trend of PVC status.|Quantity.|
    |Unbound PV Details|Detailed information about PVs that are not in the Bound state. You can filter data by keyword.|Information is displayed in the following dimensions: time, name, status, and type.|
    |Unbound PVC Details|Detailed information about PVCs that are not in the Bound state. You can filter data by keyword.|Information is displayed in the following dimensions: time, name, namespace, status.|


