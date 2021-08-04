# Use cluster check to troubleshoot cluster issues

This topic describes how to use the cluster check feature to troubleshoot issues on Alibaba Cloud Container Service for Kubernetes \(ACK\) clusters and networking.

-   An ACK cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   The ACK cluster is in the Running state. You can check the cluster status on the Clusters page in the ACK console.

## Procedure

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster on which you want to perform a check and choose **More** \> **Cluster Check** in the **Actions** column.

4.  In the left-side navigation pane, choose **Cluster Check** \> **Global Check**. You can also click the **Global Check** card in the Toolbox section to go to the Global Check page.

    ![Global Check](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/2560473261/p44809.png)

    -   Cluster: displays the cluster name, type, status, and last check result.
    -   Global Check: displays the time and results of the last five checks in reverse chronological order. If you have not performed a global check, the **Start** button appears in this section. Click Start to go to the Global Check page.
    -   Toolbox: displays cluster check features. The global check feature is available. More features such as node check will be available later.
5.  Click **Start**. In the **Global Check** panel, read and select the Warning check box, and click **Start**.

6.  To view the progress of the global check, click **Details**. On the Report page, you can view the status of cluster resources and cluster configurations.

    ![Report](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/2560473261/p44813.png)

7.  Perform an action based on the check result:

    -   Normal: No actions are required.
    -   Warning: Confirm the issue severity. Troubleshoot the issues that may cause cluster exceptions.
    -   Error: Troubleshoot the issues immediately to avoid cluster failures.
    You can click the color box to view instance information and the detailed check results. The check results include check items, possible impacts, and recommended solutions. Troubleshoot the issues based on the information.


