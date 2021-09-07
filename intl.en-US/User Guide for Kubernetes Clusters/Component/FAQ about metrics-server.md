---
keyword: FAQ about metrics-server
---

# FAQ about metrics-server

The metrics-server component collects resource metrics and helps you monitor resource usage data in the cluster. This topic provides answers to some frequently asked questions about metrics-server.

## Why is no data returned after I run the kubectl top pod or kubectl top node command?

Perform the following checks:

1.  Run the following command to check whether the metrics-server API service works as normal:

    ```
    kubectl get apiservices
    ```

    ![metris](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/6961290361/p302016.png)

    If the output shows that `v1beta1.metrics.k8s.io` is `True`, it indicates that the metrics-server API service works as normal.

2.  If the metrics-server API service does not work as normal, run the following command on the node where metrics-server is deployed to check whether metrics-server can be accessed through port 443 and port 8082 within the cluster:

    ```
    curl -v 127.0.0.1:8082/apis/metrics/v1alpha1/nodes
    ```

    If data is returned after you run the preceding command, it indicates that metrics-server can be accessed through port 443 and port 8082 within the cluster.

3.  If metrics-server cannot be accessed through port 443 or port 8082 within the cluster, restart metrics-server.

    You can delete the pod that runs metrics-server to restart metrics-server.

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane of the ACK console, click **Clusters**.

    3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

    4.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

    5.  In the upper part of the **Deployments** page, set **Namespace** to kube-system and then click metrics-server.

    6.  On the **Pods** tab, select the pod of metrics-server and choose **More** \> **Delete** in the **Actions** column.

    7.  In the **Note** message, click **OK**.


If no error is found after you perform the preceding checks, use the following ticket template to [submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).

Ticket template

1.  Does the metrics-server API service work as normal?

    Yes, the metrics-server API service works as normal.

2.  Can metrics-server be accessed through port 443 and port 8082 within the cluster?

    Yes, metrics-server can be accessed through port 443 and port 8082 within the cluster.

3.  Provide the cluster ID in the ticket.

## Why is data missing after I run the kubectl top pod or kubectl top node command?

Perform the following checks:

-   Check whether the data of all pods on a node is missing or only the data of some pods is missing. If the data of all pods on a specific node is missing, check whether a timezone difference exists on the node. You can run the ntpdate command to synchronize the time.
-   Check whether the pod that runs metrics-server can connect to the node through port 10255.

If no error is found after you perform the preceding checks, use the following ticket template to [submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).

Ticket template

1.  Is the missing data about all pods on a specific node?

    Yes, the missing data is about all pods on a specific node.

2.  Does a timezone difference exist on the node?

    No, no timetime difference exists.

3.  Can metrics-server connect to the specific node?

    Yes, metrics-server can connect to the specific node.


## Why is Horizontal Pod Autoscaler \(HPA\) unable to retrieve metrics?

Perform the following checks:

Run the `kubectl top pod pod-id` command to check whether metrics about specific pods are returned. If the output returns no data or missing data, perform the steps introduced in the preceding sections to troubleshoot.

If no error is found after you perform the preceding checks, use the following ticket template to [submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).

Ticket template

1.  Does the monitoring data show anomalies?

    No, the monitoring data does not show anomalies.

2.  Run the `kubectl describe hpa hpa-name` command and provide the metadata in the ticket.

## Why are excess pods added by HPA during a rolling update?

Perform the following checks:

Check whether metrics-server is upgraded to the latest version. If metrics-server is upgraded to the latest version, set the following startup parameters in the kube-system namespace for the pod that runs metrics-server.

```
--metric-resolution=15s
--enable-hpa-rolling-update-skipped=true
```

If no error is found after you perform the preceding checks, use the following ticket template to [submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).

Ticket template

1.  Is metrics-server upgraded to the latest version?

    Yes, metrics-server is upgraded to the latest version.

2.  Are startup parameters added to prevent excess pods?

    Yes, startup parameters are added.

3.  Run the `kubectl describe hpa hpa-name` command and provide the HPA description in the ticket.

