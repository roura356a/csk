---
keyword: [upgrade an edge cluster, edge Kubernetes, edge cluster]
---

# Upgrade an edge cluster

This topic describes how to upgrade the Kubernetes version of your edge cluster in the Container Service for Kubernetes \(ACK\) console. The cluster upgrade process involves three phases: pre-check, master node upgrade, and worker node upgrade. When you upgrade a dedicated Kubernetes cluster, the serial number of the master node that is being upgraded is displayed. When the worker nodes are upgraded, the number of upgraded worker nodes and the total number of worker nodes are displayed.

-   An edge cluster is created. For more information, see [Create a managed edge Kubernetes cluster](/intl.en-US/User Guide for Edge Container Service/Edge cluster management/Create a managed edge Kubernetes cluster.md).
-   Docker is installed on your on-premises machine. For more information, see [Install Docker](https://docs.docker.com/install/).

You can go to the **Clusters** page in the ACK console to check the Kubernetes version of your edge cluster and check whether a new version is available for upgrade.

## How the upgrade works

The following content describes how the upgrade works and the steps that are involved in the upgrade process.

![Upgrade flowchart](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3535359951/p67022.png)

-   Upgrade policy

    The upgrade policy defines how the upgrade is implemented. The default policy is batch upgrade. Batch upgrade is performed during the **worker node upgrade** phase to upgrade worker nodes in batches. The cluster is upgraded in batches:

    -   The first batch includes one node and the number of nodes increases by the power of 2 in batches thereafter. If you resume a paused upgrade, the first batch after the pause includes one node and the number of nodes increases by the power of 2 in batches thereafter.
    -   The maximum number of nodes in each batch does not exceed 10% of the total number of nodes.
-   Pre-check

    When you start the upgrade process, a pre-check is automatically run to detect possible issues that may affect the upgrade. The pre-check includes multiple check items to ensure that the upgrade can be completed without disruptions.

    If your cluster contains configuration errors or potential risks, the pre-check fails, as shown in the following figure.

    ![Pre-check](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3883068951/p75559.png)

    Click **View Details**. On the details page that appears, you can view the cause of the failure.

    ![Failed pre-check](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3535359951/p66963.png)

-   Pause the upgrade

    You can pause the upgrade process at any time during the upgrade.

    **Note:**

    -   After you pause the upgrade, the upgrade will be completed on nodes where the upgrade has already started. The upgrade will not be performed on nodes where the upgrade has not started.
    -   We recommend that you resume and complete a paused upgrade at your earliest convenience and do not perform operations on the cluster when the upgrade is paused.
    After the upgrade is paused, you can click **Continue** to resume the upgrade process.

    If an error occurs during the upgrade, the system automatically pauses the upgrade process. The cause of the error will be displayed at the bottom of the page.

-   Cancel the upgrade

    After the upgrade is paused, you can click **Cancel** to cancel the upgrade.

    **Note:**

    -   After you cancel the upgrade, the upgrade will be completed on nodes where the upgrade has already started. The upgrade will not be performed on nodes where the upgrade has not started.
    -   You cannot cancel the upgrade on the nodes where the upgrade has been completed.

## Notes

-   To upgrade a cluster, nodes in the cluster must have Internet access so that they can download the upgrade packages.
-   Applications that run in the cluster are not interrupted during the upgrade. Applications that are strongly reliant on the API server may be interrupted temporarily.
-   If you change the cluster configurations during the upgrade, for example, creating SWAP partitions, errors may occur during the upgrade process.
-   The upgrade is performed in batches. You can pause the upgrade after a batch is upgraded. We recommend that you resume and complete a paused upgrade at your earliest convenience and do not perform operations on the cluster when the upgrade is paused. If the upgrade has been paused for more than **15 days**, it will be automatically canceled and related events and log information are deleted.
-   During the upgrade process, do not modify the resources in the kube-upgrade namespace unless an error has occurred.
-   If an error occurs during the upgrade, the upgrade will be paused. You need to troubleshoot the error and delete the failed pods in the kube-upgrade namespace. After the error is fixed, you can resume the upgrade. You can contact the Alibaba Cloud technical support team for assistance.

## Preparations

**Note:** If the cluster that you want to upgrade is not deployed in the production environment, we recommend that you check whether the cluster meets the upgrade requirements before you start the upgrade in the production environment.

Before you upgrade a cluster, you must check the health status of the cluster to make sure that the cluster meets the upgrade requirements.

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  Pre-upgrades cluster nodes.

    1.  In the left-side navigation pane, click **Clusters**.

    2.  On the **Clusters** page, find the cluster that you want to upgrade and click **Details** in the **Actions** column.

    3.  On the **details** page of the cluster, click the **Connection Information** tab. Click the **Public Access** tab and copy the content of the **kubeconfig** file of the cluster to the $HOME/.kube/config file in your on-premises machine.

    4.  Run the following command on your on-premises machine.

        ```
        docker run -it -v ~/.kube:/root/.kube registry.cn-hangzhou.aliyuncs.com/edge-kubernetes/node-preprocess:v0.1.0 [Nodes in the cloud]
        ```

        **Note:** You must specify nodes in the cloud in this command. This parameter is not required if your cluster does not have a node in the cloud.

3.  In the left-side navigation pane of the ACK console, click **Clusters**.

4.  Find the cluster that you want to upgrade and choose **More** \> **Cluster Check** in the **Actions** column.

5.  In the left-side navigation pane of **Container Service Operation Center**, choose **Cluster Check** \> **Upgrade Check**.

6.  On the Upgrade Check page, click **Start**.

7.  In the **Upgrade Check** panel, select the check box under **Warning** and click **Start**.

    After the upgrade check is complete, click **Details**.

    If the **result** shows **normal** in the report, it indicates that the cluster passes the check and you can perform upgrade operations.


If errors are found in the cluster, you must fix the errors before you can upgrade the cluster. You can also submit a ticket for assistance.

## Procedure

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the **Clusters** page, find the cluster that you want to upgrade and choose **More** \> **Upgrade Cluster** in the **Actions** column.

4.  In the Upgrade Cluster dialog box, click **Confirm**.

    You can view the progress of the upgrade.


After the upgrade is complete, you can go to the Clusters page and check the current Kubernetes version of your edge cluster.

**Related topics**  


[ACK@Edge overview](/intl.en-US/User Guide for Edge Container Service/ACK@Edge overview.md)

[Create a managed edge Kubernetes cluster](/intl.en-US/User Guide for Edge Container Service/Edge cluster management/Create a managed edge Kubernetes cluster.md)

