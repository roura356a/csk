---
keyword: [expand an edge cluster, upgrade nodes in an edge cluster, Edge Node Service \(ENS\), cloud management]
---

# Expand an edge cluster

To expand an edge cluster, you can add edge nodes to the cluster in the Container Service for Kubernetes \(ACK\) console. In earlier versions, if you want to expand an edge cluster, you must purchase edge nodes and then add these nodes to the cluster. You can now purchase edge nodes on the cluster expansion page. Then, the purchased nodes are automatically added to the cluster. This topic describes how to expand an edge cluster by adding edge nodes of Edge Node Service \(ENS\) or by adding Elastic Compute Service \(ECS\) nodes.

-   [A managed edge cluster is created](/intl.en-US/User Guide for Edge Container Service/Edge cluster management/Create a managed edge cluster.md).
-   ENS is activated in the [ENS console](https://ens.console.aliyun.com/?spm=5176.12818093.products-grouped.dens.488716d0cSdYx8#/overview).

## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the **Clusters** page, find the managed edge cluster that you want to expand and click **Expand** in the **Actions** column.

4.  On the Expand page, perform the following steps to expand the cluster.

    To expand an edge cluster, you can **add ENS nodes** or **add ECS nodes** in the ACK console.

    -   **Add ENS nodes**

        To expand an edge cluster, you can add edge nodes to the cluster in the ACK console. Click the **Add ENS Node** tab, and then configure the edge nodes to be added.

        |Parameter|Description|
        |---------|-----------|
        |**Cluster Name**|The name of the managed edge cluster.|
        |**Edge Node Type**|Select the region where the edge nodes to be added are deployed.|
        |**Instance Type**|Select the instance types of the edge nodes to be added. You can select multiple instance types.|
        |**Existing Worker Nodes**|The number of existing worker nodes in the cluster.|
        |**Nodes to Add**|Select the number of worker nodes to be added.|
        |**System Disk**|Select the system disk for the nodes. The minimum size is 20 GiB. The default disk type is basic disk.|
        |**Data Disk**|Specify whether to mount data disks.|
        |**Image**|The default operating system is **centos\_7\_04\_64\_20G\_alibase\_20171211**.|
        |**Internet Bandwidth Billing Method**|ENS is billed based on the fourth peak bandwidth of each month by default. If your monthly bandwidth usage is higher than 10 Gbit/s, we recommend that you contact the business manager to request the monthly 95th percentile billing method. |
        |**Password**|Enter a password for logging on to the nodes.|
        |**Confirm Password**|Enter the password again.|
        |**Billing Method**|Only the subscription billing method is supported.|
        |**Duration**|Select the subscription duration. You can select 1, 2, 3, 6, or 12 months.|
        |**Auto Renewal**|Specify whether to automatically renew the subscription of the nodes.|

    -   **Add ECS nodes**

        To expand an edge cluster, you can also add ECS nodes to the cluster in the ACK console. Click the **Add ECS Node** tab and configure the ECS nodes to be added. For more information about how to **add ECS nodes**, see [Parameters for cluster expansion](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Expand a cluster.md).

5.  On the right side of the Expand page, click **Submit**.

6.  On the Confirm page, select the check box after you read the terms of service, and click **OK**.


Go to the **Clusters** page, you can find that the cluster changes to the **Scaling** **state**. It indicates that the expansion is in progress. When the cluster changes to the **Running** **state**, the expansion is complete.

