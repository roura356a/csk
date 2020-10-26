# Delete Istio

You can **delete** Istio that is deployed.

-   -   -   A Helm release named istio-init is running on the cluster. Before you perform the following steps, make sure that istio-init is not deleted.
-   A master node is connected. For more information, see [Use kubectl to connect to a cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to a cluster.md).

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  Log on to the ACK console. In the left-side navigation pane, choose **Service Mesh** \> **Istio Management**.

3.  Click **Delete** in the upper-right corner.

4.  In the Delete dialog box, click **OK**.


1.  In the left-side navigation pane, click **Clusters**.
2.  On the Clusters page, find the cluster that you want to manage and click the cluster name or click **Details** in the **Actions** column.
3.  On the details page of the cluster, click **Workload** in the left-side navigation pane and click the **Pods** tab.
4.  Find the cluster and the namespace from which you delete Istio and verify that Istio is deleted.

**Note:** If the configuration resources of Istio cannot be deleted from namespace istio-system, see [What do I do if custom resources are not removed after I uninstall Istio?](/intl.en-US/User Guide for Kubernetes Clusters/Service mesh/Istio FAQ.md).

