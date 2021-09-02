---
keyword: [FlexVolume, Disk Controller, NAS Controller]
---

# Install and upgrade FlexVolume

If you specify FlexVolume as the volume plug-in for a Container Service for Kubernetes \(ACK\) cluster that runs Kubernetes earlier than 1.16, the system installs FlexVolume and Disk Controller in the cluster by default. However, the system does not automatically install NAS Controller. This topic describes how to install and upgrade FlexVolume and how to install NAS Controller.

## Prerequisites

-   An ACK cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   FlexVolume is specified as the volume plug-in of the ACK cluster.
-   You are connected to the cluster by using kubectl. For more information, see [Step 2: Select a type of cluster credentials](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to ACK clusters by using kubectl.md).

## Limits

Only CentOS 7 and Aliyun Linux 2 are supported.

## Install the components

**Install FlexVolume**

-   Clusters that run Kubernetes 1.16 and later do not support FlexVolume. You must install CSI-Plugin in these clusters. For more information, see [Differences between the CSI and FlexVolume plug-ins](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/Storage overview.md).
-   If you specify FlexVolume as the volume plug-in for an ACK cluster that runs Kubernetes earlier than 1.16, the system installs FlexVolume in the cluster by default. For more information, see [Component configurations](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/Create a professional managed Kubernetes cluster.md).

**Install Disk Controller**

-   Clusters that run Kubernetes 1.16 and later do not support Disk Controller. You must install CSI-Provisioner in these clusters. For more information, see [Differences between the CSI and FlexVolume plug-ins](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/Storage overview.md).
-   If you specify FlexVolume as the volume plug-in for an ACK cluster that runs Kubernetes earlier than 1.16, the system installs Disk Controller in the cluster by default. For more information, see [Component configurations](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/Create a professional managed Kubernetes cluster.md).

**Install NAS Controller**

If your cluster already has FlexVolume installed, you can manually install NAS Controller and then dynamically provision volumes that use Apsara File Storage NAS \(NAS\) file systems.

You can use the following YAML template to manually install NAS Controller:

```
kind: Deployment
apiVersion: apps/v1
metadata:
  name: alicloud-nas-controller
  namespace: kube-system
spec:
  selector:
    matchLabels:
      app: alicloud-nas-controller
  strategy:
    type: Recreate
  template:
    metadata:
      labels:
        app: alicloud-nas-controller
    spec:
      tolerations:
      - operator: Exists
      affinity:
        nodeAffinity:
          preferredDuringSchedulingIgnoredDuringExecution:
          - weight: 1
            preference:
              matchExpressions:
              - key: node-role.kubernetes.io/master
                operator: Exists
      priorityClassName: system-node-critical
      serviceAccount: admin
      hostNetwork: true
      containers:
        - name: nfs-provisioner
          image: registry.cn-hangzhou.aliyuncs.com/acs/alicloud-nas-controller:v1.14.8.17-7b898e5-aliyun
          env:
          - name: PROVISIONER_NAME
            value: alicloud/nas
          securityContext:
            privileged: true
          volumeMounts:
          - mountPath: /var/log
            name: log
      volumes:
      - hostPath:
          path: /var/log
        name: log
```

**Verify the installation**

Check whether FlexVolume, Disk Controller, and NAS Controller are installed in the cluster.

-   Run the following command to check whether FlexVolume is installed in the cluster:

    ```
    kubectl get pod -nkube-system | grep flexvolume
    ```

-   Run the following command to check whether Disk Controller is installed in the cluster:

    ```
    kubectl get pod -nkube-system | grep alicloud-disk-controller
    ```

-   Run the following command to check whether NAS Controller is installed in the cluster:

    ```
    kubectl get pod -nkube-system | grep alicloud-nas-controller
    ```


## Upgrade the components

You can upgrade FlexVolume and Disk Controller in the ACK console. You cannot upgrade NAS Controller in the ACK console.

If your ACK cluster is upgraded to Kubernetes 1.16 or later, the cluster still supports FlexVolume. You can upgrade FlexVolume in the ACK console.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Operations** \> **Add-ons**.

5.  Click the Storage tab, find **flexvolume** and **alicloud-disk-controller**, and then click **Upgrade**.

6.  In the Note message, confirm the versions of the plug-ins and click **OK**.

    After the plug-ins are upgraded, the system prompts that the upgrades are completed and the current versions of the plug-ins are displayed.


-   When you upgrade FlexVolume in the following scenarios, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm) to request technical support.
    -   The system fails to update FlexVolume in the ACK console.
    -   The version of FlexVolume is 1.12 or earlier, and volumes that use disks and Object Storage Service \(OSS\) buckets are provisioned in the cluster.
    -   You want to ensure a successful upgrade because sensitive business data is stored in the cluster and a large number of volumes are used.
-   The system fails to upgrade Disk Controller. In this case, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm) to request technical support.

