# Modify an Ingress

This topic describes how to modify an Ingress.

-   A Container Service for Kubernetes \(ACK\) cluster is created and an Ingress controller runs as expected in the cluster. For more information, see [Create a cluster of ACK Managed Edition](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a cluster of ACK Managed Edition.md).
-   An Ingress is created. For more information, see [Create an Ingress in the console](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Ingress management/Create an Ingress on the web UI.md).

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, click **Ingresses**.

5.  On the **Ingress** page, select the Ingress that you want to modify and click **** \> **Update** in the **Actions** column of the Ingress.

6.  In the dialog box that appears, modify the parameters and click **Update**. In this example, `test.[cluster-id].[region-id].alicontainer.com` is changed to `testv2.[cluster-id].[region-id].alicontainer.com`.


Go to the Ingresses page and you will find that an Ingress rule has changed.

