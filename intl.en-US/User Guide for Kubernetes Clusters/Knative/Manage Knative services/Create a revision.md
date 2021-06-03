---
keyword: [upgrade a Knative Service, create a revision, roll back]
---

# Create a revision

You can upgrade a Knative Service by adding a revision to Knative. This topic describes how to create a revision for a Knative Service. If you want to roll back from a newly released application version to an earlier version, you only need to switch inbound traffic to the application of the specified version.

-   [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)
-   [Deploy Knative](/intl.en-US/User Guide for Kubernetes Clusters/Knative/Manage Knative components/Deploy Knative.md)
-   [Deploy a Knative component](/intl.en-US/User Guide for Kubernetes Clusters/Knative/Manage Knative components/Deploy a Knative component.md)
-   [A Knative Service is deployed](/intl.en-US/User Guide for Kubernetes Clusters/Knative/Manage Knative services/Use Knative to deploy serverless applications.md).

When you deploy a Knative Service, the system creates a revision named stock-service-example-v1 and forwards all inbound traffic to stock-service-example-v1.

## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Applications** \> **Knative**.

5.  On the Services tab, select the namespace, find the Service, and then click **Details** in the Actions column.

6.  In the upper-corner of the details page, click **Create Revision**.

7.  On the Create Revision page, set the required parameters.

    1.  Configure the parameters on the **Basic Information** wizard page.

        For more information about how to configure the basic settings of a revision, see [Set revision parameters](/intl.en-US/User Guide for Kubernetes Clusters/Knative/Manage Knative services/Use Knative to deploy serverless applications.mdstep_mrk_8ar_bk6).

    2.  Configure the parameters on the **Traffic Splitting Settings** wizard page.

        -   Revisions: A revision is created each time a Knative Service is released.
        -   Percent %: The percentage of traffic that is forwarded to a revision. The total sum of the traffic percentage values specified for all revisions must be 100%.
8.  Click **Create**.

    On the details page of the Knative Service, you can view information about the newly created revision on the **Revision Information** tab.


## Additional information

After a revision is created, you can delete the revision.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

3.  In the left-side navigation pane of the details page, choose **Applications** \> **Knative**.

4.  On the Services tab, select the namespace to which the Service belongs and click the name of the Service.

5.  In the **Revision Information** section, find the revision that you want to delete and click **Delete** in the **Actions** column.

6.  In the Delele Revision message, click **OK**.


