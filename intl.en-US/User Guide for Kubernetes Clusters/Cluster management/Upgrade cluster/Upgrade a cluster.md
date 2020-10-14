# Upgrade a cluster

This topic describes how to upgrade the Kubernetes version of your cluster. You can go to the Clusters page to check the Kubernetes version of your cluster and check whether the existing version can be upgraded. The cluster upgrade process involves the following phases: precheck, master node upgrade, and node upgrade. For dedicated clusters, the serial number of the master node that is being upgrade will be displayed during master node upgrade. The serial number starts from 1. During node upgrade, the information about upgraded nodes and total nodes are displayed.

## How the upgrade works

The following figure shows how the upgrade works. It also provides more information about the steps that are involved in the upgrade process.

![Upgrade diagram](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/3535359951/p67022.png)

-   Upgrade policy

    The upgrade policy defines how the upgrade is implemented. The default policy is batch upgrade. The batch upgrade policy is used during **node upgrade**. This allows you to upgrade multiple nodes in the cluster at a time. The policy works in the following way:

    -   The first batch includes one node. In subsequent batches, the number of nodes is increased by the power of 2. If you pause the upgrade, the first batch after the pause includes one node. In subsequent batches, the number of nodes is increased by the power of 2.
    -   The maximum number of nodes in each batch does not exceed 10% of the total number of nodes.
-   Precheck

    When you start the upgrade process, a precheck is automatically started to detect potential upgrade issues. The system performs multiple health checks for the cluster. This ensures a successful upgrade of the cluster.

    If your cluster contains configuration errors or potential risks, the precheck may fail. The following figure shows a precheck failure.

    ![](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/3883068951/p75559.png)

    Click **View Details**. You are redirected to the details page. On this page, you can check the cause of the failure.

    ![Precheck failed](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/3535359951/p66963.png)

    **Note:**

    -   If the precheck fails, you must fix the issue. If you require technical support, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).
    -   A precheck is required only before the cluster upgrade. A failed precheck does not affect the running of the cluster.
    -   If the cluster passes the precheck, the upgrade process automatically starts.
-   Pause the upgrade

    You can pause the upgrade process.

    **Note:**

    -   After you pause the upgrade, the upgrade will be completed on nodes where the upgrade has already started. The upgrade will not be performed on nodes on which the upgrade has not started.
    -   We recommend that you do not adjust the cluster settings when the upgrade is paused. We recommend that you resume the upgrade at your earliest convenience.
    To resume the upgrade, click **Continue**. This allows you to resume the upgrade process.

    If an error occurs during the upgrade, the system pauses the upgrade process. The cause of the error appears at the bottom of the page. Based on the error message, you can troubleshoot the error or [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm) to seek further technical support.

-   Cancel the upgrade

    After the upgrade is paused, you can click **Cancel** to cancel the upgrade.

    **Note:**

    -   After you cancel the upgrade, the upgrade will be completed on nodes where the upgrade has already started. The upgrade will not be performed on nodes on which the upgrade has not started.
    -   Nodes on which the upgrade has been completed are unaffected.

## Precautions

-   To upgrade a cluster, nodes in the cluster must support access over the Internet. This allows you to download additional upgrade packages.
-   Failures may occur during the upgrade process. To ensure data security, we recommend that you create snapshots of volumes before you start the upgrade. For more information about how to create a snapshot of an Elastic Compute Service \(ECS\) instance, see [Create a normal snapshot](/intl.en-US/Snapshots/Use snapshots/Create a normal snapshot.md).
-   Applications that run in the cluster are not interrupted during the upgrade. Applications that are dependent on the API server may be temporarily interrupted.
-   Object Storage Service \(OSS\) volumes that are mounted to the Kubernetes cluster based on FlexVolume 1.11.2.5 or earlier will be remounted during the upgrade. You must recreate the pods that use OSS volumes after the upgrade is completed.
-   You can modify the configurations of the cluster during the upgrade process. For example, you can create SWAP partitions. In this case, the upgrade may fail.
-   You can pause the upgrade after multiple nodes are upgraded. We recommend that you do not adjust the cluster settings when the upgrade is paused. We recommend that you resume the upgrade at your earliest convenience. If the upgrade is paused for more than 15 days, the system automatically terminates the upgrade process. Then, the events and log data that have been generated during the upgrade process are deleted.
-   During the upgrade process, do not modify the resources that belong to the kube-upgrade namespace unless an error has occurred.
-   If an error occurs during the upgrade, the upgrade is paused. You must troubleshoot the error and delete the failed pods that belong to the kube-upgrade namespace. You can restart the upgrade after the error is fixed. If you require technical support, contact Alibaba Cloud customer service.
-   After the upgrade is completed, we recommend that you upgrade the kubectl on your local worker node. Otherwise, the kubectl version may not be compatible with the API server version. In this case, the error message **invalid object doesn't have additional properties** may appear.

## Before you begin

**Note:** If the cluster that you want to upgrade is not deployed in the production environment, before you start the upgrade in the production environment, we recommend that you verify that the cluster meets the upgrade requirements.

Before you upgrade a cluster, check the health status of the cluster to make sure that the cluster meets upgrade requirements.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster on which you want to perform a cluster check and select **Cluster Check** from the **More** drop-down list in the **Actions** column.

4.  The Container Service Operation Center page appears. In the left-side navigation pane, choose **Cluster Check** \> **Upgrade Check**.

5.  On the Upgrade Check page, click **Start**.

6.  In the **Upgrade Check** pane, select the check box under **Warning** and click **Start**.

    After the upgrade check is completed, click **Details**.

    If **Cluster Resources Result** is **Normal** in the report, it indicates that the cluster passes the check and you can perform upgrade operations.


If issues are found in the cluster, you must fix the issues before you can upgrade the cluster. If you require technical support, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).

## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster you want to upgrade and select **Upgrade Cluster** from the **More** drop-down list in the **Actions** column.

4.  Click **Upgrade**.

5.  In the dialog box that appears, click **Confirm**.

    You can view the progress of the upgrade.

    **Note:**

    -   If you want to pause the upgrade, click **Pause**.
    -   After the upgrade is paused, you can click **Cancel** to cancel the upgrade process.

After the upgrade is completed, you can go to the Clusters page and check the current Kubernetes version of your edge cluster.

