# Create a revision

This topic describes how to create revisions for Knative services. If you want to roll back from a newly released application version to an earlier version, you only need to switch inbound traffic to the application of the earlier version.

-   [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)
-   [Deploy Knative](/intl.en-US/User Guide for Kubernetes Clusters/Knative management/Manage Knative components/Deploy Knative.md)
-   [Deploy components](/intl.en-US/User Guide for Kubernetes Clusters/Knative management/Manage Knative components/Deploy a Knative component.md)
-   [A Knative service is deployed](/intl.en-US/User Guide for Kubernetes Clusters/Knative management/Manage Knative services/Create a Knative Service.md).

When you deploy a Knative service, the system creates a revision of stock-service-example-v1 and forwards all inbound traffic to stock-service-example-v1.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

3.  In the left-side navigation pane, choose **Knative** \> **Service**.

4.  On the Knative Services page, select the cluster and namespace to which the Knative service belongs, find the Knative service, and click **Details** in the Actions column.

5.  In the upper-corner of the details page, click **Create Revision**.

6.  On the Create Revision page, set the required parameters.

    1.  Configure **basic settings**.

        For more information about how to configure basic settings of a revision, see [Set revision parameters](/intl.en-US/User Guide for Kubernetes Clusters/Knative management/Manage Knative services/Create a Knative Service.mdstep_mrk_8ar_bk6).

    2.  Configure **traffic splitting settings**.

        -   Revisions: A revision is created each time a Knative service is released.
        -   Percent %: The percentage of traffic that is forwarded to a revision. The total sum of the traffic percentiles specified for all revisions must be 100%.
7.  Click **Create**.

    On the details page of the created revision, click the **Revision Information** tab to view information about the revision.

    ![Revision information](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6065359951/p97823.png)


