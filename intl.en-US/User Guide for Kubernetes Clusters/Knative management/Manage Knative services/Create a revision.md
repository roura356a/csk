---
keyword: [upgrade service, create revision, roll back]
---

# Create a revision

You can add a revision to Knative to upgrade a Knative Service. This topic describes how to create a revision for a Knative Service. If you want to roll back from a newly released application version to an earlier version, you only need to switch inbound traffic to the application of the specified version.

-   [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)
-   [Deploy Knative](/intl.en-US/User Guide for Kubernetes Clusters/Knative management/Manage Knative components/Deploy Knative.md)
-   [Deploy a Knative component](/intl.en-US/User Guide for Kubernetes Clusters/Knative management/Manage Knative components/Deploy a Knative component.md)
-   [A Knative Service is deployed](/intl.en-US/User Guide for Kubernetes Clusters/Knative management/Manage Knative services/Create a Knative Service.md).

When you deploy a Knative Service, the system creates a revision of stock-service-example-v1 and forwards all inbound traffic to stock-service-example-v1.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster, or click **Details** in the **Actions** column. The details page of the cluster appears.

3.  In the left-side navigation pane of the details page, choose **Applications** \> **Knative**.

4.  On the Services tab, select the namespace, find the Service, and then click **Details** in the Actions column.

5.  In the upper-corner of the details page, click **Create Revision**.

6.  On the Create Revision page, set the required parameters.

    1.  Complete the **basic settings**.

        For more information about how to complete the basic settings of a revision, see [Set revision parameters](/intl.en-US/User Guide for Kubernetes Clusters/Knative management/Manage Knative services/Create a Knative Service.mdstep_mrk_8ar_bk6).

    2.  Complete the **traffic splitting settings**.

        -   Revisions: A revision is created each time a Knative Service is released.
        -   Percent %: The percentage of traffic that is forwarded to a revision. The total sum of the traffic percentage values specified for all revisions must be 100%.
7.  Click **Create**.

    On the details page of the created revision, you can click the **Revision Information** tab to view information about the revision.

    ![Revision information](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6065359951/p97823.png)


