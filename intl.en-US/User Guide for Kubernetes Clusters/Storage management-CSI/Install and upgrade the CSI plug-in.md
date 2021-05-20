---
keyword: [CSI plug-in, Kubernetes, CSI-Plugin, CSI-Provisioner]
---

# Install and upgrade the CSI plug-in

The CSI plug-in consists of CSI-Plugin and CSI-Provisioner. This topic describes how to install and upgrade the CSI plug-in in a Container Service for Kubernetes \(ACK\) cluster.

## Prerequisites

-   A cluster of ACK later than 1.14 is created, and the CSI plug-in is specified as the volume plug-in of the cluster. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   You are connected to the cluster by using kubectl. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl to connect to an ACK cluster.md).

## Install CSI-Plugin and CSI-Provisioner

If you do not specify FlexVolume as the volume plug-in when you create a managed Kubernetes cluster or a dedicated Kubernetes cluster, the system installs CSI-Plugin and CSI-Provisioner by default.

**Verify the installation**

Check whether CSI-Plugin and CSI-Provisioner are installed in the cluster.

-   Run the following command to check whether CSI-Plugin is installed in the cluster:

    ```
    kubectl get pod -n kube-system | grep csi-plugin
    ```

-   Run the following command to check whether CSI-Provisioner is installed in the cluster:

    ```
    kubectl get pod -n kube-system | grep csi-provisioner
    ```


## Upgrade CSI-Plugin and CSI-Provisioner

You can upgrade CSI-Plugin and CSI-Provisioner in the ACK console.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Operations** \> **Add-ons**.

5.  Click the Storage tab, find **csi-plugin** and **csi-provisioner**, and click **Upgrade**.

6.  In the Note message, confirm the versions of the plug-ins and click **OK**.

    After the plug-ins are upgraded, the system prompts that the upgrades are completed and the current versions of the plug-ins are displayed.


If the plug-ins fail to be upgraded in the console or the plug-ins fail to pass the precheck, you can perform the following operations accordingly:

-   CSI-Plugin fails to pass the precheck.
    -   If volumes that use disks, Apsara File Storage NAS \(NAS\) file systems, or Object Storage Service \(OSS\) buckets are not provisioned in the cluster, you must manually upgrade CSI-Plugin. For more information, see [Upgrade CSI-Plugin](https://github.com/kubernetes-sigs/alibaba-cloud-csi-driver/blob/master/deploy/ack/upgrade/upgrade.md).
    -   If volumes that use disks, NAS file systems, or OSS buckets are provisioned in the cluster, and the cluster is created in a staging environment, you must manually upgrade CSI-Plugin. For more information, see [Upgrade CSI-Plugin](https://github.com/kubernetes-sigs/alibaba-cloud-csi-driver/blob/master/deploy/ack/upgrade/upgrade.md).
    -   If volumes that use disks, NAS file systems, or OSS buckets are provisioned in the cluster, and business critical data is stored in the volumes, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm) to request technical support.
-   CSI-Plugin passes the precheck but fails to be upgraded.

    Check whether the nodes in the cluster are in the Ready state. If CSI-Plugin is installed on a node that is in the NotReady state, you must fix the state of the node.

    If you cannot identify the cause of the failure, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm) to request technical support.

-   CSI-Plugin is displayed in the console but CSI-Provisioner is not displayed.

    CSI-Provisioner is deployed by using a StatefulSet. In this case, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm) to request technical support.

-   CSI-Provisioner fails to pass the precheck.
    -   If no volumes that use disks or NAS file systems are dynamically provisioned by using StorageClasses in the cluster, you must manually upgrade CSI-Provisioner. For more information, see [Upgrade CSI-Provisioner](https://github.com/kubernetes-sigs/alibaba-cloud-csi-driver/blob/master/deploy/ack/upgrade/upgrade.md).
    -   If volumes that use disks or NAS file systems are dynamically provisioned by using StorageClasses in the cluster, and the cluster is created in a staging environment, you must manually upgrade CSI-Provisioner. For more information, see [Upgrade CSI-Provisioner](https://github.com/kubernetes-sigs/alibaba-cloud-csi-driver/blob/master/deploy/ack/upgrade/upgrade.md).
    -   If volumes that use disks or NAS file systems are dynamically provisioned by using StorageClasses in the cluster, and business critical data is stored in the volumes, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm) to request technical support.
-   CSI-Provisioner passes the precheck but fails to be upgraded. In this case, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm) to request technical support.

