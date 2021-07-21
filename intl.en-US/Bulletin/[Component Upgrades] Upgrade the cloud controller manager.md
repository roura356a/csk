# \[Component Upgrades\] Upgrade the cloud controller manager

To improve the stability of Container Service for Kubernetes \(ACK\) clusters, we recommend that you upgrade the cloud control manager to V1.9.3.340. This topic describes how to manually upgrade the cloud controller manager.

Starting from December 15, 2020, ACK will upgrade the cloud controller manager to the latest version for your clusters where the installed versions are earlier than V1.9.3.340. This improves the stability of your clusters. If you manually upgrade the cloud controller manager before December 15, 2020, ACK skips the upgrade plan for your clusters. You can also [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm) to skip the upgrade and continue using the current versions. We recommend that you join the upgrade plan or manually upgrade the cloud controller manager.

## Manually upgrade the cloud controller manager

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Operations** \> **Add-ons**.

5.  On the Add-ons page, click the **Core Components** tab, find the **Cloud Control Manager** section, and then click **Upgrade**.

    **Note:** If no Upgrade button is displayed in the **Cloud Control Manager** section, it indicates that the installed cloud controller manager is the latest version.

6.  In the **Note** message, click **OK**.


