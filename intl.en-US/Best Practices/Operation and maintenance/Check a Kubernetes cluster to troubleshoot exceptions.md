# Check a Kubernetes cluster to troubleshoot exceptions {#concept_185802 .concept}

This topic describes how to quickly check a Kubernetes cluster in the Container Service console to troubleshoot exceptions.

## Prerequisites {#section_3ck_mc6_6ba .section}

-   A Kubernetes cluster is created. For more information, see [Create a Kubernetes cluster](../../../../reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#).
-   The Kubernetes cluster is in the running status.

    **Note:** On the Cluster List page, you can check whether the Kubernetes cluster status is in the Running status.


## Procedure {#section_94n_wxe_kj5 .section}

1.  Log on to the [Container Service console](https://cs.console.aliyun.com/).
2.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
3.  In the left-side navigation pane under Container Service-Kubernetes, choose **Clusters** \> **Clusters**. Find the target Kubernetes cluster, and choose **More** \> **Cluster Check** in the action column.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/159904/155736553644808_en-US.png)

4.  In the left-side navigation pane, choose **Inspect** \> **Inspection**.

    **Note:** You can also click **Inspection** in the toolbox area.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/159904/155736553644809_en-US.png)

    -   Cluster: displays the cluster name, type, status, and the result of the latest check.
    -   Inspection: displays the corresponding time and results for the latest five cluster checks.

        **Note:** If you check a Kubernetes cluster for the first time, this area does not display any data except for the **Start** button. Clicking this button opens the the Inspection page where you can then enable cluster checking.

    -   Toolbox: displays the cluster check module.
5.  Click **Start**. Then, in the displayed page, select the **Warning** check box, and click **Start**.

    **Note:** After you enable the cluster checks, the page displays the progress for the cluster checks in real time.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/159904/155736553644812_en-US.png)

6.  Click **Details** to view the results for the cluster resource and configuration checks.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/159904/155736553644813_en-US.png)

7.  Perform the action that corresponds to the result returned from the check.

    -   Normal: No action is required.
    -   Warning: Confirm the severity of the issue. If the issue may result in cluster exceptions, perform the appropriate actions to troubleshoot the issue.
    -   Exception: Perform the required actions to troubleshoot the issue immediately.
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/159904/155736553644815_en-US.png)

    **Note:** 

    -   For each resource or configuration \(which contains result and instance information\) in the result of a check, you can click the corresponding box icon on the right to view the instance and result information.
    -   If a resource or configuration is not in the normal state, the result displays the checked items, severity of exceptions, and recommended solutions. You can perform actions to troubleshoot as needed.

