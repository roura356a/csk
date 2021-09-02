---
keyword: [cluster health check, global check]
---

# Use the global check feature to troubleshoot cluster issues

This topic describes how to use the global check feature to troubleshoot issues in Container Service for Kubernetes \(ACK\) clusters and networks.

-   An ACK cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   The cluster is running as expected. You can navigate to the Clusters page and check whether the cluster is in the **Running** state.

## Use scenarios

To perform a global check on your cluster resources, components, and configurations and obtain suggestions on how to fix issues, you need only to click **Global Check**. No parameter configurations are required. We recommend that you check and maintain your cluster on a regular basis.

## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster on which you want to perform a check and choose **More** \> **Cluster Check** in the **Actions** column.

4.  In the left-side navigation pane, choose **Cluster Check** \> **Global Check**. You can also click the **Global Check** card in the Toolbox section to go to the Global Check page.

    ![Global Check](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/2560473261/p44809.png)

    -   Cluster: displays the cluster name, type, status, and the most recent check result.
    -   Global Check: displays the time and results of the previous five checks in reverse chronological order. If this is the first time that you perform a global check, the **Start** button appears in this section. You can click Start to go to the Global Check page.
    -   Toolbox: displays features that are related to cluster health checks. The global check feature is available. More features such as node health checks will be available soon.
5.  Click **Start**. In the **Global Check** panel, read and select the Warning check box, and click **Start**.

6.  To view the progress of the global check, click **Details**. On the Report page, you can view the status of cluster resources and cluster configurations.

    ![Report](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/2560473261/p44813.png)

7.  Perform an action based on the check result.

    -   Normal: No actions are required.
    -   Warning: Confirm the severity of each issue. Troubleshoot the issues that may cause cluster anomalies.
    -   Error: Troubleshoot the issues at the earliest opportunity to avoid cluster errors.
    You can click the color box to view instance information and the detailed check result. The check result includes check items, possible impacts, and suggestions on how to fix issues. The information helps you troubleshoot issues.


If you cannot locate the causes of node, pod, and network issues based on the check report, we recommend that you use the cluster diagnosis feature to troubleshoot the issues. For more information, see [Use the cluster diagnosis feature to troubleshoot cluster issues]().

