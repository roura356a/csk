---
keyword: [how to upgrade the Sandboxed-Container runtime, how the upgrade works, considerations]
---

# Upgrade the Sandboxed-Container runtime

You can upgrade the Sandboxed-Container runtime that is deployed on your nodes in the Container Service for Kubernetes \(ACK\) console. This topic describes how the Sandboxed-Container runtime is upgraded and how to perform an upgrade. It also lists the considerations to which you must pay attention during the upgrade.

## How the Sandboxed-Container runtime is upgraded

-   Upgrade process and status transitions

    After you start an upgrade, the system automatically creates and executes upgrade tasks. The tasks are automatically divided into batches, distributed to nodes that require upgrades, and executed by running pods. During the upgrade process, you can pause, resume, or cancel the upgrade based on your business requirements.

    ![Upgrade flowchart](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/2665359951/p97122.png)

    -   After you click **Upgrade**, the task changes to the **Upgrading** state.
    -   You can pause an **upgrading** task. After an upgrading task is paused, the task changes to the **Paused** state.
    -   You can resume a **paused** task. After a paused task is resumed, the task changes to the **Upgrading** state.
    -   You can also cancel a **paused** task. After a paused task is canceled, the task changes to the **Canceled** state.
-   Upgrade policy

    Runtime upgrade is performed in batches:

    -   The first batch includes one node. In subsequent batches, the number of nodes is increased by the power of 2. If you resume a paused upgrade, the first batch after the pause includes one node. The number of nodes is increased by the power of 2 in batches thereafter.
    -   The maximum number of nodes in a batch does not exceed 10% of the total number of nodes.
    Assume that a cluster has 50 nodes that require upgrades. The upgrade process is proceeded in the following batches:

    -   The first batch includes one node.
    -   The second batch includes two nodes.
    -   The third batch includes four nodes.
    -   The fourth batch includes five nodes. Based on the calculation method, the number of nodes in the fourth batch is 8 \(the third power of 2 is 8\). However, the maximum number of nodes in a batch cannot exceed 10% of the total number of nodes. In this example, the maximum number = 50 × 10% = 5.
    -   The fifth batch includes five nodes.
    -   The following batches upgrade nodes in this way until all nodes are upgraded.
-   Pause an upgrade

    You can pause an upgrade process at any time. Take the following notes when you pause an upgrade:

    -   After you pause the upgrade, the upgrade will be completed on nodes where the upgrade has already started. The upgrade will not be performed on nodes where the upgrade has not started.
    -   We recommend that you resume and complete a paused upgrade at your earliest convenience. Do not perform operations on the cluster when the upgrade is paused.
    -   When you pause the upgrade, the system automatically checks whether nodes that have not started the upgrade exist. If no such nodes exist, you fail to pause the upgrade.
    After the upgrade is paused, you can click **Continue** to resume the upgrade.

    If an error occurs during the upgrade, the system automatically pauses the upgrade process. The cause of the error will be displayed at the bottom of the page. You can troubleshoot the error or [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm) to request technical support.

-   Cancel an upgrade

    After an upgrade is paused, you can click **Cancel** to cancel the upgrade. Take the following notes when you cancel an upgrade:

    -   After you cancel the upgrade, the upgrade will be completed on nodes where the upgrade has already started. The upgrade will not be started on nodes where the upgrade has not started.
    -   You cannot cancel the upgrade on the nodes where the upgrade is complete.

## Notes

-   To upgrade the runtime, nodes must have Internet access so that they can download upgrade packages.
-   Failures may occur during the upgrade. To ensure data security, we recommend that you take snapshots of storage volumes before the upgrade.
-   Applications that run in the cluster are not interrupted during the upgrade. We recommend that you check the release notes of the runtime to decide whether you need to release applications again. For more information, see [Release notes for Sandboxed-Container](/intl.en-US/Release notes/System Component change Records/Sandboxed-Container release notes/Release notes for Sandboxed-Container.md).
-   The upgrade is performed in batches. You can pause the upgrade after a batch has been upgraded. We recommend that you resume and complete a paused upgrade at your earliest convenience. Do not perform operations on the cluster when the upgrade is paused. If the upgrade has been paused for more than 15 days, it will be automatically canceled. The related events and log information are deleted.
-   Do not add nodes to or remove nodes from the cluster during the upgrade. To add or remove nodes, you must first cancel the upgrade.
-   Do not modify the resources in the **runtime-upgrade** namespace during the upgrade unless an error has occurred.
-   If an error occurs during the upgrade, the process will be paused. You need to troubleshoot the error and delete the failed pods in the **runtime-upgrade** namespace. After the error is fixed, you can resume the upgrade.

    **Note:** Do not delete or modify resources other than the failed pods in the **runtime-upgrade** namespace even if an error occurs. You can contact the Alibaba Cloud technical support team for assistance.


## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  On the details page of the cluster, choose **Operations** \> **Runtime Upgrade**.

5.  On the **Runtime Upgrade** page, click **Upgrade**.

6.  In the message that appears, click **OK**.

    To pause the upgrade, click **Pause**. After the upgrade is paused, you can click **Continue** to resume the upgrade or click **Cancel** to cancel the upgrade.

    ![Resume or cancel](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/2665359951/p97128.png)

    After you click **Upgrade** or **Continue**, you can view the operation records in the events list.

    ![Events list](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/2665359951/p97132.png)


After the upgrade is complete, the upgraded runtime no longer appears when you refresh or reopen the current page.

